# 💊 pharma_insights

**Pharmacy Sales Analytics – PostgreSQL Star Schema Project**

---

## 📌 Project Overview

`pharma_insights` is an end-to-end SQL analytics project that transforms a raw, denormalised pharmacy sales dataset into a structured **Star Schema model** using PostgreSQL.

The project demonstrates:

* Data cleaning and standardisation
* Data type validation and correction
* Date engineering from fragmented fields
* Star schema design with enforced foreign keys
* Fact and dimension modelling
* Analytical querying using joins and aggregations
* Business insight extraction from structured data
  
---

## 🎯 Business Objectives

This analysis answers key business questions:

1. Which product classes drive the highest revenue?
2. What channels and subchannels contribute most to total sales?
3. How has revenue trended from 2022–2025?
4. Which product categories have the highest demand (volume)?
5. Which customer segments contribute most to revenue?
6. What are the peak and low-performing months?

---

## 🗂 Dataset Structure (Raw)

The raw dataset initially contained:

* Distributor
* CustomerName
* City
* Country
* Latitude
* Longitude
* Channel
* Subchannel
* ProductName
* ProductClass
* Quantity (TEXT)
* Price (TEXT)
* Sales (TEXT)
* Months (TEXT)
* Years (TEXT)
* NameofSalesRep
* Manager
* SalesTeam

The dataset was fully denormalised and required cleaning and restructuring.

---

## 🧹 Data Cleaning & Preparation

### 1️⃣ Standardisation

* Applied `TRIM()` to remove whitespace inconsistencies
* Consolidated inconsistent customer names using `CASE WHEN`
* Normalised text-based categorical fields

### 2️⃣ Type Conversion

Several numeric fields were stored as TEXT and converted safely using `NULLIF()` to prevent casting errors:

```sql
ALTER TABLE pharmacy_records
ALTER COLUMN quantity TYPE NUMERIC(12,0)
USING NULLIF(TRIM(quantity),'')::NUMERIC;
```

Fields converted:

* quantity → NUMERIC
* price → NUMERIC
* sales → NUMERIC
* latitude → NUMERIC
* longitude → NUMERIC

### 3️⃣ Date Engineering

Merged fragmented month and year columns into a proper `record_date` column:

```sql
ALTER TABLE pharmacy_records
ADD COLUMN record_date DATE;

UPDATE pharmacy_records
SET record_date =
to_date(TRIM(years) || ' ' || TRIM(months) || ' 01', 'YYYY Month DD');
```

Dropped obsolete `Months` and `Years` columns after validation.

---

# ⭐ Star Schema Design

The cleaned dataset was transformed into a dimensional model.

## 📊 Fact Table

### `fact_sales`

| Column         | Description           |
| -------------- | --------------------- |
| transaction_id | Surrogate primary key |
| record_date    | Sales date            |
| customer_id    | FK → dim_customer     |
| product_id     | FK → dim_product      |
| distributor_id | FK → dim_distributor  |
| salesrep_id    | FK → dim_staff        |
| channel        | Sales channel         |
| subchannel     | Sales subchannel      |
| quantity       | Units sold            |
| price          | Unit price            |
| sales          | Revenue               |

---

## 📂 Dimension Tables

### `dim_customer`

* customer_id (PK)
* customername
* city
* country
* latitude
* longitude

### `dim_product`

* product_id (PK)
* productname
* productclass

### `dim_distributor`

* distributor_id (PK)
* distributor

### `dim_staff`

* salesrep_id (PK)
* nameofsalesrep
* manager
* salesteam

---

## 🔗 Fact Table Population Logic

Fact table populated via joins to ensure foreign keys are never null:

```sql
INSERT INTO fact_sales (...)
SELECT ...
FROM pharmacy_records r
JOIN dim_customer c ON ...
JOIN dim_product p ON ...
JOIN dim_distributor d ON ...
JOIN dim_staff s ON ...;
```

This approach prevents foreign key violations and ensures referential integrity.

---

## 📈 Analytical Queries

### Revenue by Year

```sql
SELECT EXTRACT(YEAR FROM record_date) AS year,
       SUM(sales) AS total_revenue
FROM fact_sales
GROUP BY year
ORDER BY year;
```

### Top Product Classes by Revenue

```sql
SELECT p.productclass,
       SUM(f.sales) AS revenue
FROM fact_sales f
JOIN dim_product p ON f.product_id = p.product_id
GROUP BY p.productclass
ORDER BY revenue DESC;
```

### Monthly Trend Analysis

```sql
SELECT DATE_TRUNC('month', record_date) AS month,
       SUM(sales) AS revenue
FROM fact_sales
GROUP BY month
ORDER BY month;
```

---

# 📊 Key Insights (Example Findings)

* Premium product segments contributed disproportionately to revenue.
* Revenue dips were strongly correlated with demand drops in top-performing product classes.
* Specific months (e.g., May) consistently underperformed.
* High-value prescriptions significantly influenced overall revenue fluctuations.
* Sales recovery observed when demand for revenue-driving products increased.

---

# 🛠 Tools Used

* PostgreSQL
* pgAdmin
* SQL (DDL, DML, Aggregations, Joins, Constraints)

---

# 🚀 Project Value

This project demonstrates:

* Strong understanding of dimensional modelling
* Ability to clean and transform real-world messy data
* Foreign key enforcement and referential integrity handling
* Analytical query optimisation
* Business-driven data storytelling

---

# 📌 Future Improvements

* Index optimisation for performance
* Partitioning fact table by year
* Integration with Power BI for dashboard visualisation
* Python-based forecasting for revenue prediction
* Automated ETL pipeline using scheduled scripts

---

# 👤 Author

Elijah Okpako
Data Analyst | SQL | Power BI | Python
GitHub: Jay0494

---


