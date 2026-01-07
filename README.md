# Big Home Data Inventory And Optimization Analysis 
## Project Overview
This project leverages **SQL** for data extraction, transformation, and analysis to uncover key insights and performance trends across multiple departments and brands. The analysis focuses on sales performance, brand contributions, departmental performance, and discount effects to drive data-informed recommendations,
## Key Performance Indicators (KPIs)
- Total Sales: **$19,409,081**
- Total Quantity Sold: **64,231**
- Departments Analyzed: **15**
- Brands Covered: **22**
## Insights
1. Brand Performance
o Sales contribution across brands shows stability, ranging between **3%** and **7%**.
o Best performing brand: **Samsung** 
o Least performing brand: **Kobalt** 
2. Sales Trends
o Sales were relatively stable overall but experienced instability between months 8–11, indicating possible seasonal or market-driven fluctuations.
3. Departmental Performance
o Top performing department: **HVAC** generating **$4M** in sales with **2,369** units sold.
o Lowest performing department: **Flooring**, recording **$103,092** in revenue despite having the highest sales volume.
* This indicates high demand but low unit price, suggesting opportunities for pricing optimization or product bundling.
4. Discount Analysis
o Discounts become unprofitable at **7%**, where both sales revenue and order quantity drop.
o At **10%** and **15%** discount levels, sales show strong responsiveness, driving higher order volumes, suggesting optimal discount thresholds.

### Deep Analysis
```SQL

SELECT* FROM big_box_home_improvement_dataset

--TO know the top performing Department
SELECT TOP 10 department,
ROUND(SUM(total_after_discount),2)AS total_sales,
SUM (quantity) AS total_quantity_sold
FROM big_box_home_improvement_dataset
GROUP BY department
ORDER BY total_sales DESC

--TO KNOW THE LEAST PERFORMING DEPARTMENT
SELECT department,
ROUND(SUM(total_after_discount),2)AS total_sales,
SUM (quantity) AS total_quantity_sold
FROM big_box_home_improvement_dataset
GROUP BY department
ORDER BY total_sales DESC

--TOP BRANDS DRIVING SALES AND THE PERCENTAGE THE CONTRIBUTE TO THE COMPANY
WITH Brand_Perc AS (
SELECT brand,
SUM(total_after_discount) AS total_sales,
SUM (quantity) AS total_quantity_sold
FROM big_box_home_improvement_dataset
GROUP BY brand
)

SELECT brand,
total_sales,
SUM (total_sales) OVER () overallsales,
CONCAT (ROUND((CAST( total_sales AS FLOAT)/SUM (total_sales) OVER () )*100,2),'%' )AS percentage_total
FROM Brand_Perc
ORDER BY percentage_total DESC

--TOTAL SALES
SELECT
ROUND(SUM(total_after_discount),2) AS total_sales
FROM big_box_home_improvement_dataset
--TOTAL QUANTITY SOLD
SELECT
SUM (quantity) AS total_quantity_sold
FROM big_box_home_improvement_dataset

--NUMBER OF DEPARTMENT
SELECT
COUNT(DISTINCT department) AS NO_of_department
FROM big_box_home_improvement_dataset
-- NUMBER OF BRANDS
SELECT
COUNT(DISTINCT brand) AS NO_of_brand
FROM big_box_home_improvement_dataset

--DISCOUNT IMPACT ON SALES/ AT WHICH DISCOUNT BECOMES UNPROFITABLE
SELECT
bulk_discount_applied AS discount_level,
ROUND(SUM(total_after_discount),2) AS total_sales,
ROUND(SUM (subtotal_before_discount-total_after_discount),2) AS discount_margin,
ROUND(AVG(total_after_discount),2) AS avg_total_sales,
SUM(quantity) AS total_quantity_Ordered
FROM big_box_home_improvement_dataset
GROUP BY bulk_discount_applied
ORDER BY discount_level

--MONTLY SALES TRENDS
SELECT
Month (Purchase_date) AS sales_month,
ROUND(SUM(total_after_discount),2) AS total_sales,
SUM (quantity) AS total_quantity_sold,
COUNT(DISTINCT transaction_id) AS total_transaction
FROM big_box_home_improvement_dataset
GROUP BY Month(Purchase_date) 
ORDER BY Month(Purchase_date) 

--DIFFERENCE BETWEEN MONTHLY SALES

SELECT MONTH(purchase_date)AS order_month,
ROUND(SUM(total_after_discount),2) AS total_sales,
ROUND(LAG(SUM(total_after_discount)) OVER(ORDER BY MONTH(purchase_date)),2)AS previous_month,
ROUND(SUM(total_after_discount)- LAG(SUM(total_after_discount)) OVER(ORDER BY MONTH(purchase_date)),2)AS monthly_change
FROM big_box_home_improvement_dataset
GROUP BY MONTH(purchase_date)



--TO KNOW HOW MUCH EACH RETAILER CONTRIBUTE TO REVENUE
SELECT retailer,
SUM(total_after_discount) AS total_sales,
SUM (quantity) AS total_quantity_sold

FROM big_box_home_improvement_dataset
GROUP BY  retailer
ORDER BY total_sales
```

## Recommendations
1. Strengthen High-Performing Brands: Continue supporting Samsung with promotions and partnerships while developing strategies to uplift weaker brands like Kobalt.
2. Boost Underperforming Brands: Develop targeted promotions and marketing to support weaker brands like Kobalt, while reinforcing Samsung’s market dominance.
3. Investigate Seasonal Instability: Explore external factors (e.g., holidays, supply chain issues) influencing the sales dip between months 8–11 to stabilize performance.
4. Leverage Flooring Department Demand: Since Flooring products have high sales volume but low revenue, consider price adjustments, bundling, or premium product introductions.
5. Optimize Discounts: Maintain discounts in the 10–15% range to maximize both sales revenue and order volume while avoiding unprofitable thresholds.

### TOOLS
SQL
