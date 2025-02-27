# Retail Sales Analysis – End-to-End Data Pipeline (Python + SQL Server)

## 📌 Project Overview 
This project demonstrates a **comprehensive data analysis pipeline** using **Python and SQL Server** to derive meaningful business insights from a **Retail Sales Dataset**. The pipeline includes **data extraction, cleaning, transformation, database integration, and SQL-based analysis.**  
By leveraging **Python for data processing and SQL for analytical queries**, this project provides valuable insights into **sales trends, revenue generation, and product performance.**  

## **🔗 Workflow**
1️⃣ **Data Extraction:** Retrieved dataset using Kaggle API.  
2️⃣ **Data Cleaning & Processing:** Processed the data using Python (Pandas).  
3️⃣ **Data Storage:** Loaded the cleaned data into SQL Server.  
4️⃣ **Data Analysis:** Executed SQL queries to generate business insights.  
<img width="265" alt="image" src="https://github.com/user-attachments/assets/5c242c9e-0dad-4001-b788-506d99252856" />

## **🛠️ Tech Stack**
- **Python**: Data extraction, preprocessing, and transformation  
- **Pandas**: Data manipulation  
- **SQL Server**: Data storage and querying  
- **Kaggle API**: Dataset extraction  
- **SQLAlchemy & pyodbc**: SQL Server integration with Python

## **📊 SQL-Based Analytical Queries**
### **1️⃣ Top 10 Highest Revenue-Generating Products**
Identified the **top-performing products** based on revenue.  

```sql
SELECT TOP 10 product_name, SUM(revenue) AS total_revenue
FROM sales_data
GROUP BY product_name
ORDER BY total_revenue DESC;
```

### **2️⃣ Top 5 Best-Selling Products in Each Region**  
Ranked products based on total sales volume in different regions.  

```sql
WITH RankedProducts AS (
    SELECT region, product_name, SUM(quantity) AS total_sold,
           RANK() OVER (PARTITION BY region ORDER BY SUM(quantity) DESC) AS rank
    FROM sales_data
    GROUP BY region, product_name
)
SELECT region, product_name, total_sold
FROM RankedProducts WHERE rank <= 5;
```

### **3️⃣ Month-over-Month Sales Growth (2022 vs 2023)**  
Compared sales growth for the same months in different years.  

```sql
SELECT FORMAT(order_date, 'yyyy-MM') AS sales_month,
       SUM(CASE WHEN YEAR(order_date) = 2022 THEN revenue ELSE 0 END) AS revenue_2022,
       SUM(CASE WHEN YEAR(order_date) = 2023 THEN revenue ELSE 0 END) AS revenue_2023,
       (SUM(CASE WHEN YEAR(order_date) = 2023 THEN revenue ELSE 0 END) - 
        SUM(CASE WHEN YEAR(order_date) = 2022 THEN revenue ELSE 0 END)) AS revenue_growth
FROM sales_data
GROUP BY FORMAT(order_date, 'yyyy-MM')
ORDER BY sales_month;
```

### **4️⃣ Best Sales Month for Each Product Category**
Determined the **month with peak sales** for each category.

```sql
WITH MonthlySales AS (
    SELECT category, FORMAT(order_date, 'yyyy-MM') AS sales_month,
           SUM(revenue) AS total_revenue,
           RANK() OVER (PARTITION BY category ORDER BY SUM(revenue) DESC) AS rank
    FROM sales_data
    GROUP BY category, FORMAT(order_date, 'yyyy-MM')
)
SELECT category, sales_month, total_revenue
FROM MonthlySales WHERE rank = 1;
```

### **5️⃣ Sub-Category with Highest Profit Growth (2023 vs 2022)**
Analyzed which product **sub-category** had the highest profit growth.

```sql
WITH ProfitGrowth AS (
    SELECT sub_category,
           SUM(CASE WHEN YEAR(order_date) = 2022 THEN profit ELSE 0 END) AS profit_2022,
           SUM(CASE WHEN YEAR(order_date) = 2023 THEN profit ELSE 0 END) AS profit_2023,
           (SUM(CASE WHEN YEAR(order_date) = 2023 THEN profit ELSE 0 END) - 
            SUM(CASE WHEN YEAR(order_date) = 2022 THEN profit ELSE 0 END)) AS profit_growth
    FROM sales_data
    GROUP BY sub_category
)
SELECT sub_category, profit_2022, profit_2023, profit_growth
FROM ProfitGrowth
ORDER BY profit_growth DESC;
```


