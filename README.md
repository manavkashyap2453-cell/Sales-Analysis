# 📊 Sales Analysis

A detailed analysis of sales at a retail store.

![Supermarket](supermarket-949913_1280.jpg)

---

## 📌 Project Overview

**Project Title:** Retail Sales Analysis  

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data.  
The project involves performing **Exploratory Data Analysis (EDA)** and answering specific business questions through SQL queries.

---

## 🎯 Objectives

- **Exploratory Data Analysis (EDA):**  
  Perform basic exploratory data analysis to understand the dataset.

- **Business Analysis:**  
  Use SQL to answer specific business questions and derive insights from the sales data.

---

## 📂 Project Structure

# Sales Analysis Queries

Below are the SQL queries used to analyze retail sales data:

---
## 1. Total Sales Revenue Across All Branches
```sql
SELECT Branch, 
       ROUND(SUM(`Unit price` * Quantity), 2) AS Total_Sales
FROM `sales`
GROUP BY Branch;
```

## 2. Branch Generating the Highest Revenue
```sql
SELECT Branch, 
       ROUND(SUM(`Unit price` * Quantity), 2) AS Total_Sales
FROM `sales`
GROUP BY Branch
ORDER BY Total_Sales DESC
LIMIT 1;
```

## 3. Distribution of Customer Types (Normal vs Member)
```sql
SELECT `Customer type`, 
       COUNT(*) AS CustomerTypeCount
FROM `sales`
GROUP BY `Customer type`;
```

## 4. Gender Split of Customers
```sql
SELECT Gender, 
       COUNT(*) AS GenderCount
FROM `sales`
GROUP BY Gender;
```

## 5. Product Line with the Highest Sales Volume
```sql
WITH cte_unit_sale AS (
    SELECT *, 
           (`Unit price` * Quantity) AS unit_sale
    FROM `sales`
)
SELECT `Product line`, 
       SUM(unit_sale) AS Product_Sale
FROM cte_unit_sale
GROUP BY `Product line`
ORDER BY Product_Sale DESC
LIMIT 1;
```

## 6. Average Unit Price Across Product Lines
```sql
SELECT `Product line`, 
       ROUND(AVG(`Unit price`), 2) AS AvgProductSale
FROM `sales`
GROUP BY `Product line`;
```

## 7. Average Customer Rating per Branch
```sql
SELECT Branch, 
       ROUND(AVG(Rating), 4) AS AvgRating
FROM `sales`
GROUP BY Branch;
```

## 8. Do members spend more per transaction compared to normal customers?
```sql
select `Customer type`, round(avg(`Unit price`*Quantity),2) as AvgSpent
from `sales`
group by `Customer type`;
```

## 9. Is there a difference in product preferences between male and female customers?
```sql
WITH cte_preference AS (
    SELECT 
        Gender,
        `Product Line`,
        COUNT(*) AS product_count
    FROM `sales`
    GROUP BY Gender, `Product Line`
),
ranked AS (
    SELECT 
        Gender,
        `Product Line`,
        product_count,
        ROW_NUMBER() OVER (PARTITION BY Gender ORDER BY product_count DESC) AS rn
    FROM cte_preference
)
SELECT Gender, `Product Line`, product_count
FROM ranked
WHERE rn = 1;
```

## 10. Which product lines have the highest average rating, and does that correlate with sales?
```sql
with cte_avg_product_rating as(
select `Product Line`, avg(Rating) over(partition by `Product Line`) as avg_rating
from `sales`
)
select distinct `Product Line`, avg_rating
from cte_avg_product_rating
order by avg_rating desc
limit 1;

with cte_sales as(
select `Product Line`, sum(`Unit Price`* Quantity) as product_sale
from `sales`
group by `Product Line`
)

select *
from cte_sales
order by product_sale desc
limit 2;
```
## 11. Are certain payment methods more common in specific branches?
```sql
with cte_payment as (
select Payment,Branch,count(Branch) as branch_count
from `sales`
group by Payment,Branch
), ranked as (
select *, row_number() over(partition by Payment order by branch_count desc) as branch_rank
from cte_payment
)

select Payment, Branch, branch_count 
from ranked
where branch_rank=1;
```

## 12. Does quantity purchased vary significantly by customer type or gender?
```sql
select Gender , sum(Quantity)
from `sales`
group by Gender;

select `Customer Type` , sum(Quantity)
from `sales`
group by `Customer Type`;
```

## 13. Are low-rated product lines also generating lower sales, or is there a mismatch?
```sql
with cte_product_sale as(
select `Product Line`, 
sum(`Unit Price`*Quantity) over(partition by `Product Line`) as product_sale, 
avg(Rating) over(partition by `Product line`) as avg_rating
from `sales`
)

select distinct(`Product Line`),product_sale, avg_rating  
from cte_product_sale
order by product_sale;
```

## 14. Can we predict total sales based on unit price, quantity, and customer type?
```sql
select `Customer type`, sum(`Unit price`*Quantity) as total_sale
from `sales`
group by `Customer type`;
```

## 15. Does customer rating influence repeat purchases (proxy: higher quantity or frequency)?
```sql
select `Product line`,Quantity, Rating
from `sales`
order by Rating desc;
```

## 16. Which branch has the most balanced performance across revenue, customer satisfaction, and product diversity?
```sql
with cte_branch_performance as(
select Branch,
sum(Quantity*`Unit Price`) as branch_sale,
avg(Rating)  as avg_rating,
count(distinct(`Product line`)) as number_of_products
from `sales`
group by Branch
)
select * from cte_branch_performance order by avg_rating;
```

## 17. Are members more likely to purchase higher-priced items compared to normal customers?
```sql
select `Customer type`, avg(`Unit price`)
from `sales`
group by `Customer type`;
```

## 18. Does gender influence payment method choice?
```sql
select Gender,Payment, count(*)
from `sales`
group by Gender,Payment;
```

## 19. Which product line should be promoted more to maximize revenue?
```sql
with cte_product_sale as(
select `Product Line`, 
sum(`Unit Price`*Quantity) over(partition by `Product Line`) as product_sale
from `sales`
)
select distinct(`Product line`), product_sale 
from cte_product_sale
order by product_sale desc;
```

