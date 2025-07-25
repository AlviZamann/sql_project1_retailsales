
# 🗃️ Retail Sales SQL Analysis — Project v1

## 📌 Overview

This project demonstrates the use of SQL for data cleaning, exploration, and analysis using a fictional **Retail Sales** dataset. The aim is to derive business insights such as sales trends, customer behavior, and product performance.

---

## 🏗️ 1. Database Setup

We begin by creating a database and a table called `retail_sales` to store transactional data.

**Schema:**
- `transactions_id`: Transaction identifier
- `sale_date`: Date of sale
- `sale_time`: Time of sale
- `customer_id`: Unique customer identifier
- `gender`: Gender of the customer
- `age`: Age of the customer
- `category`: Product category
- `quantity`: Quantity sold
- `price_per_unit`: Price of one unit
- `cogs`: Cost of goods sold
- `total_sale`: Total transaction value

```sql
CREATE DATABASE SQL_Project_v1;

CREATE TABLE retail_sales (
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);
```

---

## 🧹 2. Data Cleaning & Exploration

### ✅ Initial Checks:
- Total number of records  
- Number of unique customers  
- List of product categories  
- Null value checks  

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;
```

### ❌ Null Records Handling:
Records with any `NULL` value are deleted for analysis consistency.

```sql
DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

---

## 📊 3. Data Analysis & Business Insights

A set of targeted SQL queries were developed to answer key business questions.

### A. Sales on Specific Date
```sql
SELECT * FROM retail_sales
WHERE sale_date = '2022-11-05';
```

### B. High-Volume Clothing Orders in Nov 2022
```sql
SELECT * FROM retail_sales
WHERE category = 'Clothing' AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11' AND quantity >= 4;
```

### C. Total Sales by Category
```sql
SELECT category, SUM(total_sale) AS net_sale, COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

### D. Average Age of Beauty Category Buyers
```sql
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

### E. High-Value Transactions (>1000)
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000;
```

### F. Transactions by Gender and Category
```sql
SELECT category, gender, COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender;
```

### G. Best Selling Month in Each Year
```sql
SELECT year, month, avg_sale
FROM (
    SELECT 
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank
    FROM retail_sales
    GROUP BY year, month
) AS t
WHERE rank = 1;
```

### H. Top 5 Customers by Sales
```sql
SELECT customer_id, SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

### I. Unique Customers per Category
```sql
SELECT category, COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;
```

### J. Order Count by Time Shift
```sql
WITH hourly_sale AS (
    SELECT *,
        CASE 
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT shift, COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
```

---

## ✅ Project Status

**Completed:** Version 1  
**Scope:** Setup → Cleaning → Exploratory Analysis → Business Insights  
**Next Steps (Optional):**
- Integrate visualization tools (Power BI/Tableau)
- Optimize query performance
- Add stored procedures for automation

---

## 📁 File Structure

```
├── SQL_Project_v1/
│   ├── schema.sql
│   ├── data_cleaning.sql
│   ├── business_queries.sql
│   └── README.md
```

---

## 📧 Contact

**Author:** Alvi Zaman  
For any queries or suggestions, feel free to reach out via GitHub or email.
