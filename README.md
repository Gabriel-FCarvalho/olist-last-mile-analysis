# Olist Last Mile Analysis

Analysis of last mile delivery performance using the Brazilian e-commerce public dataset (Olist), covering order fulfillment, delivery delays, freight pricing, and customer satisfaction.

---

## Business Questions

This project is structured around four core questions:

**Q1 — Customer tolerance and satisfaction impact**
What is the relationship between delivery delay (in days) and customer review scores? Is there a threshold from which satisfaction drops significantly?

**Q2 — Cross-region deliveries and delivery performance**
Do orders crossing state or municipal boundaries show higher delivery delays compared to local deliveries?
> Note: this analysis measures total delivery delay, not last mile in isolation.

**Q3 — Freight cost and distance correlation**
Is freight value correlated with the estimated distance between seller and customer zip codes?
> Note: distance is calculated as straight-line (Euclidean) between coordinates — used as a proxy for road distance.

**Q4 — Where does delay concentrate?**
Which phase of the order lifecycle concentrates more delay: between approval and shipment (seller responsibility) or between shipment and delivery (carrier responsibility)?

---

## Stack

| Layer | Tool |
|---|---|
| Exploration & cleaning | Python (pandas, numpy) · Jupyter Notebook |
| Data warehouse | Google BigQuery |
| Transformation & modeling | dbt |
| Visualization | Power BI |
| Version control | Git · GitHub |

---

## Project Roadmap

| Phase | Focus | Key deliverable | Status |
|---|---|---|---|
| 1 | Ingestion & exploration | Load all 9 tables, map relationships and inconsistencies | ✅ In progress |
| 2 | Cleaning & BigQuery | Treat inconsistent dates, load raw data to warehouse | ⏳ Pending |
| 3 | Exploratory analysis | SQL queries answering Q1–Q4 | ⏳ Pending |
| 4 | Visualization | Power BI dashboard connected to BigQuery | ⏳ Pending |
| 5 | ETL pipeline | Automated incremental ingestion pipeline | ⏳ Pending |
| 6 | dbt modeling | Staging, intermediate and mart layers with tests and documentation | ⏳ Pending |

---

## Dataset

**Source:** [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — Kaggle

**Tables:**
- `olist_orders_dataset`
- `olist_order_items_dataset`
- `olist_order_payments_dataset`
- `olist_order_reviews_dataset`
- `olist_customers_dataset`
- `olist_sellers_dataset`
- `olist_products_dataset`
- `olist_product_category_name_translation`
- `olist_geolocation_dataset`

---

## Project Structure

```
olist-last-mile/
│
├── README.md
├── data/
│   └── raw/              # Original Kaggle CSVs — never modified
│
├── notebooks/
│   ├── 01_ingestion.ipynb
│   ├── 02_cleaning.ipynb
│   └── 03_exploratory.ipynb
│
├── sql/
│   └── exploratory/      # BigQuery queries — one file per business question
│
├── dbt/
│   ├── models/
│   │   ├── staging/
│   │   ├── intermediate/
│   │   └── mart/
│   ├── tests/
│   └── dbt_project.yml
│
├── powerbi/
│   └── last_mile.pbix
│
└── docs/
    └── data_model.md
```

---

*Project developed as part of a data career transition — from fiscal analyst to data analyst with analytics engineering focus.*
