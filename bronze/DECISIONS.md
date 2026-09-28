# Bronze Layer — Decisions & Findings

## Context

This document records the decisions made, problems found, and validations performed during the Bronze layer setup of the `olist-last-mile-analysis` project.

---

## 1. Approach

Raw CSV files were loaded into a Databricks Volume at `/Volumes/workspace/olist_raw/bronze/`. From there, each file was registered as a Delta table under the `workspace.bronze` schema using `CREATE TABLE ... USING DELTA AS SELECT * FROM read_files(...)`.

This approach was chosen because:
- Delta tables are queryable via SQL without referencing file paths on every query
- It establishes a clear separation between raw storage (Volume) and queryable data (Delta tables)
- It enables the Silver layer to be built entirely in SQL, which is the focus of Phase 3

---

## 2. Tables Created

| Table | Source file |
|---|---|
| workspace.bronze.orders | olist_orders_dataset.csv |
| workspace.bronze.customers | olist_customers_dataset.csv |
| workspace.bronze.order_items | olist_order_items_dataset.csv |
| workspace.bronze.order_payments | olist_order_payments_dataset.csv |
| workspace.bronze.order_reviews | olist_order_reviews_dataset.csv |
| workspace.bronze.products | olist_products_dataset.csv |
| workspace.bronze.sellers | olist_sellers_dataset.csv |
| workspace.bronze.geolocation | olist_geolocation_dataset.csv |
| workspace.bronze.product_category_translation | product_category_name_translation.csv |

---

## 3. Issue Found — order_reviews Parsing

### Symptom
The first load of `order_reviews` produced unexpected results:
- `review_score` was inferred as `string` instead of `integer`
- Timestamps and comment text fragments were appearing in the `review_score` column
- Row count was inflated: 104,162 rows instead of the expected 99,249

### Root cause
The CSV contains line breaks inside comment fields. The default parser treated each line break as a record terminator, which shifted subsequent columns out of alignment and artificially split single rows into multiple ones.

The same issue affected the earlier Python/Pandas analysis, which also did not handle multiline fields — explaining why the original row count of 104,162 matched between both reads, but was incorrect in both cases.

### Fix
The table was dropped and recreated with `multiLine => 'true'`, which instructs the parser to treat line breaks inside quoted fields as part of the value rather than as record terminators.

### Result after fix

| Metric | Before fix | After fix |
|---|---|---|
| Total rows | 104,162 | 99,249 |
| Null review_score | 2,380 | 10 |
| Null review_comment_title | 92,157 | 87,677 |
| Null review_comment_message | 63,079 | 58,272 |

The 10 remaining null values in `review_score` are extreme edge cases that no CSV parser can recover automatically. They will be handled explicitly in the Silver layer.

---

## 4. Bronze Validation Summary

| Table | Total rows | Findings |
|---|---|---|
| orders | 99,441 | 160 nulls in `order_approved_at`, 1,783 in `order_delivered_carrier_date`, 2,965 in `order_delivered_customer_date`. No nulls in `order_estimated_delivery_date`. |
| order_reviews | 99,249 | Corrected after multiLine fix. 10 residual nulls in `review_score`. |
| customers | 99,441 | No issues. |
| order_items | 112,650 | No issues. `price` and `freight_value` correctly inferred as `double`. |
| order_payments | 103,886 | No issues. |
| sellers | 3,095 | No issues. |
| products | 32,951 | 610 nulls in `product_category_name` — expected, present in the original dataset. |
| geolocation | 1,000,163 | No nulls. Multiple coordinate entries per zip code — aggregation required before joining with orders in Q3. |
| product_category_translation | 71 | No issues. |

---

## 5. Items Carried Forward to Silver

- **review_score** — cast to integer; the 10 residual invalid records will be excluded with an explicit filter.
- **product_name_lenght / product_description_lenght** — typo present in the original Olist dataset. Will be renamed to `product_name_length` and `product_description_length` in Silver.
- **orders** — null delivery dates will not be dropped. An `is_delivered` boolean flag will be added to make the delivery status explicit. `delay_days` will only be calculated when `is_delivered = true`.
- **geolocation** — aggregation by zip code prefix required before joining with orders for Q3 analysis.
- **_rescued_data** — Databricks-generated column from the parsing process. Will be dropped across all Silver tables.