# Olist Last Mile Analysis

Analysis of last-mile delivery performance using the Brazilian e-commerce public dataset (Olist), covering order fulfilment, delivery delays, freight pricing, and customer satisfaction.

---

## Business Questions

This project is structured around three core questions:

**Q1 — Where does delay occur in the delivery cycle?**
- Which phase of the order lifecycle concentrates more delay: between approval and shipment (seller responsibility) or between shipment and delivery (carrier responsibility)?

**Q2 — Delay and customer satisfaction:**
- What is the relationship between delivery delay (in days) and customer review scores? Is there a threshold from which satisfaction drops significantly?

**Q3 — Regions and routes with worst delivery performance:**
- Which regions and routes concentrate the highest delays? Do orders crossing state boundaries show higher delays compared to local deliveries?
> Note: distance is calculated as straight-line (Euclidean) between coordinates — used as a proxy for road distance.

---

## Stack

| Layer | Tool |
|---|---|
| Bronze | Databricks — raw data, never modified |
| Silver | Databricks — cleaned and standardized data |
| Gold | dbt + Databricks — Last Mile metrics and models |
| Visualization | Power BI — connected to Databricks Gold layer |
| Version control | Git · GitHub |

---

## Architecture

```
CSV Olist (Kaggle)
      ↓
Bronze (Databricks) — raw data
      ↓
Silver (Databricks) — cleaned, standardized
      ↓
Gold (dbt + Databricks) — Last Mile metrics
      ↓
Power BI — visualization and consumption
```

---

## Project Roadmap

| Phase | Focus | Key deliverable | Status |
|---|---|---|---|
| 1 & 2 | Data import | Workspace setup, CSVs loaded into Databricks, Bronze ready | ✅ Done |
| 3 | Silver layer | Cleaned and standardized data — ready for analysis | ✅ Done |
| 4 | Business analysis | SQL + Statistics answering Q1–Q3 over Silver | ⏳ Pending |
| 5 | Gold layer | dbt modeling + ETL pipeline + Last Mile metrics | ⏳ Pending |
| 6 | Visualization | Power BI dashboard connected to Databricks Gold layer | ⏳ Pending |
| 7 | Storytelling | Case narrative + portfolio + README final | ⏳ Pending |

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
olist-last-mile-analysis/
│
├── README.md
├── bronze/                              # Databricks — raw Delta tables
│   ├── 01_create_bronze_tables
│   └── DECISIONS.md
│
├── silver/                              # Databricks — cleaning and standardization
│   ├── 01_create_silver_tables
│   └── DECISIONS.md
│
├── sql/
│   └── business_questions/             # Databricks SQL — Q1–Q3 over Silver
│
├── dbt/
│   ├── models/
│   │   └── gold/                       # dbt models — Last Mile metrics
│   ├── tests/
│   └── dbt_project.yml
│
├── powerbi/
│   └── last_mile.pbix
│
└── docs/
    └── data_model.md
```