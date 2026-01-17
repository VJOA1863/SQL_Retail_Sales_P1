# Retail Sales Analysis Using SQL

## Project Overview

**Project Title**: Retail Sales Analysis  
**Tools Used**: SQL (PostgreSQL / MySQL compatible)  
**Dataset**: Retail transaction-level sales data  

This project demonstrates how SQL can be used to explore, clean, and analyze retail sales data to support **business decision-making**.  
The focus is not only on writing SQL queries, but on translating query results into **actionable business insights** related to sales performance, customer behavior, and operational efficiency.

---

## Business Objective

The objective of this analysis is to help a retail business:

- Understand overall sales performance  
- Identify high-value customers and products  
- Analyze category and demographic trends  
- Uncover time-based and operational sales patterns  

These insights can support decisions across **marketing, inventory planning, and operations**.

---

## Database & Table Structure

The analysis is performed on a single table containing retail transactions.

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

Before analysis, the dataset was explored to understand its structure and quality.

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

> **Note:** In a real-world environment, missing data would typically be investigated further.  
> For this analytical project, incomplete records were excluded to maintain data integrity.

---

## Business Questions, SQL Analysis & Insights

### 1. Sales on a Specific Date

**Business Question:**  
What sales occurred on a specific date?

~~~sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
~~~

**Business Insight:**  
Used to validate daily sales activity and investigate anomalies or campaign-driven spikes.

---

### 2. High-Volume Clothing Sales (November 2022)

**Business Question:**  
Which Clothing transactions had high quantities in November 2022?

~~~sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND quantity >= 4
  AND sale_date BETWEEN '2022-11-01' AND '2022-11-30';
~~~

**Business Insight:**  
Identifies bulk purchases and high-demand periods to support targeted promotions or inventory planning.

---

### 3. Total Sales by Category

**Business Question:**  
Which product categories generate the most revenue?

~~~sql
SELECT 
    category,
    SUM(total_sale) AS total_sales,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
~~~

**Business Insight:**  
Helps prioritize high-performing categories and review underperforming ones.

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
Supports customer segmentation and targeted marketing strategies.

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
Identifies premium purchases that can be targeted with loyalty programs or personalized offers.

---

### 6. Transactions by Gender and Category

**Business Question:**  
How do transaction volumes differ by gender across categories?

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
Reveals demographic purchasing patterns that inform product positioning and promotions.

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
Highlights seasonal peaks to support inventory planning and promotional timing.

---

### 8. Top 5 Customers by Total Sales

**Business Question:**  
Who are the highest-value customers?

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
Supports customer retention strategies, VIP programs, and personalized engagement.

---

### 9. Unique Customers per Category

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
Indicates category reach and overall market penetration.

---

### 10. Order Distribution by Time of Day

**Business Question:**  
How are orders distributed across different times of the day?

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
Supports staffing optimization, operating-hour decisions, and time-based promotions.

---

## Key Takeaways

- Sales performance varies significantly by category and time period  
- A small group of customers contributes a large share of total revenue  
- Seasonal and time-of-day trends offer operational optimization opportunities  
- Demographic insights can improve targeting and marketing efficiency  

---

## Conclusion

This project demonstrates how SQL can be applied to retail transaction data to generate **business-relevant insights**.  
The workflow reflects practical analyst techniques, combining data validation, aggregations, window functions, and clear interpretation of results.

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
