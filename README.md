# 🛍️ Customer Shopping Behaviour Analysis

An end to end data analysis project that cleans,transforms and analyzes 3,900+ customer shopping records to uncover actionable business insights from raw CSV to a Python-to-MySQL pipeline, advanced SQL analysis and an interactive Power BI dashboard.

---

##  Project Overview

Retail businesses generate massive amounts of customer data but raw data alone doesn't drive decisions.
<br> This project simulates a real world analytics workflow: taking messy, inconsistent shopping data and turning it into clear, decision-ready insights.

**What this project answers:**
- Who are our highest-value customers, and what drives their spending?
- Does a subscription or discount actually increase revenue?
- Which products and categories perform best and why?
- How can we segment customers to target them more effectively?

---

## 🧰 Tech Stack

| Layer | Tools Used |
|---|---|
| Data Cleaning & Feature Engineering | Python (Pandas) |
| Database & Pipeline | MySQL, SQLAlchemy |
| Business Analysis | SQL (Joins, CTEs, Window Functions, Aggregations) |
| Visualization | Power BI |

---

## 🔄 Project Workflow

```
Raw CSV (3,900+ records)
      ↓
Data Cleaning & Feature Engineering (Python/Pandas)
      ↓
Python → MySQL Pipeline (SQLAlchemy)
      ↓
Business Analysis (Advanced SQL Queries)
      ↓
Interactive Dashboard (Power BI)
```

---

## 🧹 Data Cleaning & Feature Engineering

- **Missing value handling:** Filled missing `Review Rating` values using **category-wise median imputation** (`groupby().transform()`) instead of a blanket average — preserving accuracy across different product categories.
- **Column standardization:** Converted all column names to lowercase, snake_case format (e.g., `Purchase Amount (USD)` → `purchase_amount`) for consistency and easier querying.
- **Redundant column removal:** Verified that `discount_applied` and `promo_code_used` were identical across all rows, then dropped the duplicate column to keep the dataset clean.
- **Feature engineering:**
  - Created `age_group` (Young Adult / Adult / Middle-aged / Senior) using quartile-based binning (`pd.qcut`)
  - Created `purchase_frequency_days`, converting text frequency values (e.g., "Weekly", "Fortnightly") into numeric day intervals for trend analysis

---

## 🗄️ Database Pipeline

Cleaned data was pushed from Python directly into MySQL using **SQLAlchemy**, creating a repeatable, automated pipeline instead of manual CSV imports:

```python
engine = create_engine(f"mysql+mysqlconnector://{user}:{password}@localhost/customer_shopping")

df.to_sql(
    name="customer_shopping",
    con=engine,
    if_exists="replace",
    index=False
)
```

> 🔒 **Note:** Database credentials are stored as environment variables and are not exposed in this repository.

---

## 📊 Business Questions Solved with SQL

Using joins, subqueries, `CASE` statements, CTEs and window functions this project answers 10 real business questions including:

1. Total revenue comparison — Male vs Female customers
2. Customers who used a discount but still spent above average
3. Top 5 highest-rated products
4. Standard vs Express shipping — average spend comparison
5. Do subscribed customers spend more than non-subscribers?
6. Top 5 products with highest discount usage rate
7. Customer segmentation — New / Returning / Loyal buyers
8. Top 3 best-selling products within each category (using `ROW_NUMBER()` + `PARTITION BY`)
9. Correlation between repeat purchases and subscription status
10. Revenue contribution by age group

📁 Full queries available in [`customer_shopping_behaviour_sql.sql`](./customer_shopping_behaviour_sql.sql)

**Example — Top 3 products per category using a window function:**
```sql
WITH item_counts AS (
    SELECT category, item_purchased,
           COUNT(customer_id) AS total_orders,
           ROW_NUMBER() OVER (PARTITION BY category ORDER BY COUNT(customer_id) DESC) AS item_rank
    FROM customer_shopping
    GROUP BY category, item_purchased
)
SELECT item_rank, category, item_purchased, total_orders
FROM item_counts
WHERE item_rank <= 3;
```

---

## 📈 Power BI Dashboard

The cleaned dataset and SQL insights were visualized in an interactive Power BI dashboard featuring:
- Revenue breakdown by gender, age group, and category
- Subscription vs non-subscription spending comparison
- Top-performing products and categories
- Customer segmentation view (New / Returning / Loyal)


## 🔑 Key Insights

- Subscribed customers show meaningfully higher average spend, confirming subscription programs drive revenue.
- A notable share of customers spend above average *even after* applying a discount — indicating strong product-value perception rather than pure price sensitivity.
- Clothing and Footwear dominate the top-performing categories across most segments.

---

## 📂 Repository Structure

```
├── customer_shopping_behavior.csv          # Raw dataset
├── customer_shopping_behaviour.ipynb       # Data cleaning & feature engineering
├── customer_shopping_behaviour_sql.sql     # Business analysis queries
└── README.md
```

---

## 🚀 How to Run

1. Clone this repository
2. Install dependencies: `pip install pandas sqlalchemy mysql-connector-python`
3. Set up a local MySQL database and update connection credentials as environment variables
4. Run the Jupyter Notebook to clean data and push it to MySQL
5. Execute the SQL file against the `customer_shopping` table
6. Open the Power BI file to explore the dashboard

---


