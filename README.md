# 💊 pharma_insights

### End-to-End SQL Data Warehouse Project (PostgreSQL)

---

## 🚀 Project Summary

`pharma_insights` is a production-style SQL data warehouse project that transforms raw, denormalised pharmacy sales data into a structured **Star Schema model** for analytics and business intelligence.

This project demonstrates my ability to:

* Clean and standardise messy operational datasets
* Engineer proper data types and enforce validation rules
* Design and implement a dimensional data model
* Build fact and dimension tables with foreign key integrity
* Write analytical SQL queries that generate business insight
* Structure a scalable foundation for BI tools like Power BI

---

# 🏗 Architecture Overview

### 🔹 Stage 1 — Raw Operational Table

The original dataset was fully denormalised and stored multiple entities in a single table.

Challenges included:

* Text-based numeric fields
* Fragmented date fields (Month + Year)
* Inconsistent naming conventions
* Whitespace and formatting inconsistencies

---

### 🔹 Stage 2 — Data Cleaning & Transformation

Key transformations performed:

✔ Standardised categorical values using `TRIM()` and conditional logic
✔ Converted TEXT numeric fields to NUMERIC using safe casting
✔ Engineered a proper `record_date` field from fragmented columns
✔ Removed obsolete columns post-validation
✔ Ensured no blank strings were cast to numeric (using `NULLIF()`)

Example:

```sql
ALTER TABLE pharmacy_records
ALTER COLUMN sales TYPE NUMERIC(12,2)
USING NULLIF(TRIM(sales),'')::NUMERIC;
```

This ensured analytical reliability and eliminated runtime casting errors.

---

# ⭐ Dimensional Modelling (Star Schema)

The cleaned dataset was transformed into a structured warehouse model.

## 📊 Fact Table

### `fact_sales`

Captures transactional-level metrics:

* record_date
* customer_id
* product_id
* distributor_id
* salesrep_id
* channel
* subchannel
* quantity
* price
* sales

This enables scalable aggregation across time, product, geography, and sales teams.

---

## 📂 Dimension Tables

### `dim_customer`

Customer and location attributes

### `dim_product`

Product and product class hierarchy

### `dim_distributor`

Distributor-level breakdown

### `dim_staff`

Sales rep and management structure

---

## 🔗 Referential Integrity Enforcement

Fact records were inserted via controlled joins to dimension tables:

```sql
INSERT INTO fact_sales (...)
SELECT ...
FROM pharmacy_records r
JOIN dim_customer c ON ...
JOIN dim_product p ON ...
JOIN dim_distributor d ON ...
JOIN dim_staff s ON ...;
```

This approach:

* Eliminates NULL foreign keys
* Prevents orphan records
* Enforces warehouse consistency
* Reflects best practice dimensional modelling

---

# 📈 Business Analysis Capability

Using structured joins and aggregations, the model supports:

### Revenue Trend Analysis

```sql
SELECT EXTRACT(YEAR FROM record_date) AS year,
       SUM(sales) AS total_revenue
FROM fact_sales
GROUP BY year;
```

### Product Revenue Contribution

```sql
SELECT p.productclass,
       SUM(f.sales) AS revenue
FROM fact_sales f
JOIN dim_product p ON f.product_id = p.product_id
GROUP BY p.productclass
ORDER BY revenue DESC;
```

### Channel Performance Analysis

* Revenue by Channel
* Revenue by Subchannel
* Volume vs Revenue comparisons

---

# 📊 Analytical Insights Enabled

The model allows identification of:

* Revenue-driving product classes
* Underperforming months and seasonal dips
* Demand-driven revenue declines
* High-value prescription impact on revenue growth
* Customer-level revenue concentration

This creates a structured foundation for executive-level KPI dashboards.

---

# 🛠 Technical Stack

* PostgreSQL
* SQL (DDL, DML, Aggregations, Constraints)
* Dimensional Modelling
* Data Type Engineering
* Referential Integrity Management

---

# 👤 Author

Elijah Okpako
Data Analyst | SQL | Power BI | Python
GitHub: Jay0494

