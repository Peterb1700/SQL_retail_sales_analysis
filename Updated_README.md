
# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Tools Used**: Google Drive, PostGRESQL, GitHub  
**Level**: Beginner  
**Database**: `retailsalesanalysis2`  

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## 1. Database Setup & First Query

**Database Creation**: The project starts by creating a database named `retailsalesanalysis2` in PostGRESQL.  
I did some minor cleaning/analysis in Excel before creating the table in PostGRESQL. I formatted the `Date` to year-month-day and used the `MAX(LENGTH)` function to determine the `VARCHAR` length (15 characters).  

**Table Creation**: A table named `retail_sales` was created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE DATABASE retailsalesanalysis2;

DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(15),
    age INT,
    category VARCHAR(15),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT, 
    total_sale FLOAT
);
```
After running this query, the data was imported from Excel into PostGRESQL.

## 2. Data Exploration & Cleaning

**Record Count**: Determine the total number of records in the dataset. (**RESULT: 2000**)

```sql
SELECT COUNT(*) FROM retail_sales;
```

**Customer Count**: Find out how many unique customers are in the dataset. (**RESULT: 155**)

```sql
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
```

**Category Count**: Identify all unique product categories in the dataset. (**RESULT: Beauty, Electronics, and Clothing**)

```sql
SELECT DISTINCT category FROM retail_sales;
```

**Null Value Check**: Check for any null values in the dataset and delete records with missing data.  
**RESULT**: This deleted 13 null values, leaving 1987 records remaining.

```sql
SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

**Retrieve All Sales for '2022-11-05'**:  

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

**Retrieve All Transactions for 'Clothing' with Quantity > 4 (November 2022)**:  

```sql
SELECT *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity >= 4;
```

## 3. Data Analysis & Findings

### Key Insights:

1. **Total Sales by Category**:  
    - **Electronics**: $311,445 (678 total orders)  
    - **Clothing**: $309,995 (698 total orders)  
    - **Beauty**: $286,790 (611 total orders)  

    ```sql
    SELECT 
        category,
        SUM(total_sale) as net_sale,
        COUNT(*) as total_orders
    FROM retail_sales
    GROUP BY 1;
    ```

2. **Average Age of Customers (Beauty)**:  
    - **Result**: 40.42 years  

    ```sql
    SELECT
        ROUND(AVG(age), 2) as avg_age
    FROM retail_sales
    WHERE category = 'Beauty';
    ```

3. **Transactions > $1000**:  
    - **Result**: 306 total rows  

    ```sql
    SELECT * FROM retail_sales
    WHERE total_sale > 1000;
    ```

4. **Transactions by Gender and Category**:  
    - Female Beauty: 330  
    - Male Beauty: 281  
    - Female Clothing: 347  
    - Male Clothing: 351  
    - Male Electronics: 343  
    - Female Electronics: 335  

    ```sql
    SELECT 
        category,
        gender,
        COUNT(*) as total_trans
    FROM retail_sales
    GROUP BY category, gender
    ORDER BY 1;
    ```

5. **Top 5 Customers by Sales**:  
    - Customer 3: $38,440  
    - Customer 1: $30,750  
    - Customer 5: $30,405  
    - Customer 2: $25,295  
    - Customer 4: $23,580  

    ```sql
    SELECT 
        customer_id,
        SUM(total_sale) as total_sales
    FROM retail_sales
    GROUP BY 1
    ORDER BY 2 DESC
    LIMIT 5;
    ```

6. **Unique Customers by Category**:  
    - Beauty: 141  
    - Clothing: 149  
    - Electronics: 144  

    ```sql
    SELECT 
        category,    
        COUNT(DISTINCT customer_id) as cnt_unique_cs
    FROM retail_sales
    GROUP BY category;
    ```

7. **Orders by Shift**:  
    - Morning: 548 orders  
    - Afternoon: 1062 orders  
    - Evening: 377 orders  

    ```sql
    WITH hourly_sale AS (
        SELECT *,
            CASE
                WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
                WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
                ELSE 'Evening'
            END as shift
        FROM retail_sales
    )
    SELECT 
        shift,
        COUNT(*) as total_orders    
    FROM hourly_sale
    GROUP BY shift;
    ```

## Conclusion

This project served as a comprehensive introduction to SQL, covering database setup, data cleaning, exploratory data analysis, and advanced queries to derive actionable business insights. Key findings include detailed analysis of customer behavior, sales trends, and category performance. These insights can help drive business decisions to improve sales and customer engagement.
