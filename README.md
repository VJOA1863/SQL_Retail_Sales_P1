# Retail Sales Analysis Using SQL

## Project Overview

**Project Title**: Retail Sales Analysis  
**Tools Used**: SQL (PostgreSQL / MySQL compatible)  
**Dataset**: Retail transaction-level sales data  

This project demonstrates how SQL can be used to explore, clean, and analyze retail sales data to support **business decision-making**.  
The focus is not only on writing SQL queries, but on translating query outputs into **clear, actionable business insights** related to sales performance, customer behavior, and operational efficiency.

---

## Business Objective

The objective of this analysis is to help a retail business:

- Understand overall sales performance  
- Identify high-value customers and revenue-driving categories  
- Analyze customer demographics and purchasing behavior  
- Uncover operational patterns based on time of day and seasonality  

These insights can support decisions across **marketing, inventory planning, and operations**.

---

## Database & Table Structure

The analysis is performed on a single table containing retail transaction data.

~~~sql
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
~~~

---

## Data Validation & Preparation

Before analysis, the dataset was explored to assess structure and data quality.

### Key Checks Performed
- Total record count  
- Number of unique customers  
- Identification of product categories  
- Detection of missing or incomplete records  

~~~sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;
~~~

Records with missing critical values were **excluded from analysis** to ensure reliable outputs.

> **Note:** In real-world business environments, missing data would typically be investigated further.  
> For this analytical project, incomplete records were excluded to maintain consistency and accuracy.

---

## Business Questions, SQL Analysis & Insights

---

### 1. Sales on a Specific Date

**Business Question:**  
What sales occurred on a specific date (2022-11-05)?

~~~sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
~~~

**Business Insight:**  
Sales on 5th November 2022 show steady transaction activity across different customer demographics and times of day, particularly in the Clothing category. This indicates normal operational performance rather than an anomaly or promotion-driven spike.

---

### 2. High-Volume Clothing Sales (November 2022)

**Business Question:**  
Which Clothing transactions involved higher quantities during November 2022?

~~~sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND quantity >= 4
  AND sale_date BETWEEN '2022-11-01' AND '2022-11-30';
~~~

**Business Insight:**  
High-quantity Clothing purchases were made by customers across multiple age groups, genders, and times of day. This suggests that bulk or multi-item Clothing purchases are broadly distributed rather than concentrated in a single customer segment, making wide-reaching promotions more effective than highly niche targeting.

---

### 3. Total Sales by Category

**Business Question:**  
Which product categories generate the highest total sales?

~~~sql
SELECT 
    category,
    SUM(total_sale) AS total_sales,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
~~~

**Business Insight:**  
Electronics is the highest revenue-generating category, closely followed by Clothing, while Beauty contributes comparatively lower total sales. Electronics and Clothing are the primary revenue drivers and should remain core focus areas, while Beauty may benefit from targeted promotions or pricing strategies.

---

### 4. Average Age of Beauty Customers

**Business Question:**  
What is the average age of customers purchasing Beauty products?

~~~sql
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
~~~

**Business Insight:**  
The average age of Beauty customers is approximately 40 years, indicating that Beauty products are primarily purchased by mid-age consumers. Marketing and product positioning for this category may therefore perform better when aligned with mature customer preferences.

---

### 5. High-Value Transactions

**Business Question:**  
Which transactions exceed a total sale value of 1000?

~~~sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
~~~

**Business Insight:**  
The presence of multiple high-value transactions across categories highlights opportunities to identify premium customers. These customers could be targeted with loyalty programs, personalised offers, or exclusive services to drive repeat high-value purchases.

---

### 6. Transactions by Gender and Category

**Business Question:**  
How are transactions distributed across gender within each category?

~~~sql
SELECT 
    category,
    gender,
    COUNT(*) AS total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
~~~

**Business Insight:**  
Transaction volumes for Clothing and Electronics are evenly distributed between genders, suggesting broad appeal across customer groups. Beauty transactions show a stronger female skew, indicating that gender-focused marketing strategies may be more effective for Beauty than for other categories.

---

### 7. Best-Selling Month per Year

**Business Question:**  
Which month performs best each year based on average sales?

~~~sql
SELECT year, month, avg_sale
FROM (
    SELECT
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY year, month
) t
WHERE rank = 1;
~~~

**Business Insight:**  
Best-performing months vary by year, reflecting seasonal demand patterns. Identifying these peaks allows businesses to optimise inventory levels, staffing, and promotional timing to maximise revenue during high-demand periods.

---

### 8. Top 5 Customers by Total Sales

**Business Question:**  
Who are the top customers based on total sales?

~~~sql
SELECT 
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
~~~

**Business Insight:**  
A small group of customers contributes a disproportionately high share of total revenue. Retaining these high-value customers through targeted engagement and loyalty initiatives could significantly improve revenue stability.

---

### 9. Unique Customers by Category

**Business Question:**  
How many unique customers purchase from each category?

~~~sql
SELECT 
    category,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
~~~

**Business Insight:**  
All categories show a similar number of unique customers, indicating comparable customer reach. Revenue differences between categories are therefore driven more by purchase value and frequency rather than customer count alone.

---

### 10. Order Distribution by Time of Day

**Business Question:**  
How are orders distributed across different times of day?

~~~sql
WITH hourly_sales AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) AS total_orders
FROM hourly_sales
GROUP BY shift;
~~~

**Business Insight:**  
The majority of orders occur during the evening, indicating that customer purchasing activity peaks after working hours. This insight can inform staffing schedules, system capacity planning, and time-based marketing campaigns.

---

## Key Takeaways

- Electronics and Clothing are the primary revenue drivers  
- Customer demographics vary by category, supporting targeted marketing strategies  
- A small group of customers contributes a large share of total revenue  
- Sales activity peaks during evenings and seasonal high-demand months  

---

## Conclusion

This project demonstrates how SQL can be applied to real-world retail data to generate **business-relevant insights**.  
The workflow reflects practical analyst techniques, combining data validation, aggregation logic, window functions, and actionable interpretation of results.

---

## How to Use This Project

1. Clone the repository  
2. Create the table using the provided schema  
3. Load the retail sales dataset  
4. Run the SQL queries to reproduce the analysis  

---

## Author

This project is part of my professional data analytics portfolio.  
Feedback, collaboration, and discussion are welcome.
