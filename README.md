# Bike_Stores_Analysis

## Project Overview
This project analyses sales performances across three bike stores between 2016 and 2018 using MySQL for data extraction and analysis and Excel for ulterior data analysis and data visualization. The analysis focuses on revenue trends, customer behaviour, product performance and store comparisons.

## Objectives
- Analyze stores performance over the years 2016 - 2018
- Identify top customers per store
- Analyze category revenue
- Identify best categories and products per each one of them
- Investigate sales trends

## Tools Used
- MySQL
- Excel

## Methodology
Project followed the following workflow:
1.	Explored the dataset to understand the available tables
2.	Queried the dataset using MySQL 
3.	Calculated KPIs such as revenue, order count and average order value
4.	Imported the query results into Excel
5.	Built PivotTables and PivotCharts
6.	Designed an interactive dashboard using slicers

## SQL Analysis
-- PERIOD ANALYZED

'''sql
SELECT MIN(order_date) AS start_date,
	MAX(order_date) AS end_date
FROM orders;
'''
-- analysis starts on 1 january 2016 and finishes on 28 december 2018


-- COUNT OF PRODUCTS FOR EACH CATEGORY

SELECT category_name AS categories,
	COUNT(product_name) AS num_of_products
FROM categories c
JOIN products p
USING(category_id)
GROUP BY categories
ORDER BY num_of_products DESC;

-- REVENUE AND ORDERS DURING YEARS PER STORE

SELECT YEAR(o.order_date) AS year,
	s.store_name,
	s.state,
	ROUND(SUM(i.quantity * i.list_price * (1 - i.discount)),2) AS revenue,
	COUNT(DISTINCT o.order_id) AS orders_num
FROM orders o
JOIN stores s
USING (store_id)
JOIN bike_shop.order_items i
USING (order_id)
GROUP BY year, 
store_name, 
state
ORDER BY year;
    
-- AVERAGE ORDER PRICE PER SHOP PER YEAR

WITH CTE_price AS(
SELECT store_name,
	YEAR (order_date) AS year,
	((list_price * quantity) * (1 - discount)) AS price
FROM orders o1
JOIN order_items o2
USING(order_id)
JOIN stores s
USING(store_id)
)
SELECT year,
	store_name,
	ROUND(AVG(price),2) AS average_sales_per_order
FROM CTE_price
GROUP BY store_name,
year
ORDER BY year,
store_name;

-- CUSTOMERS SALES AND ORDERS NUMBER

SELECT s.store_name,
	CONCAT(c.first_name, " ", c.last_name) AS customer_name,
    ROUND(SUM((o.list_price * o.quantity) * (1 - o.discount)),2) AS sales_per_cust,
    COUNT(DISTINCT o2.order_id) AS orders_made
FROM order_items o
LEFT JOIN orders o2
USING(order_id)
JOIN customers c
ON o2.customer_id = c.customer_id
JOIN stores s
ON o2.store_id = s.store_id
GROUP BY s.store_name,
customer_name
ORDER BY sales_per_cust DESC;

-- MOST SOLD CATEGORIES PER STORES AND REVENUE 

SELECT s.store_name,
	c.category_name,
    SUM(o2.quantity) AS products_sold,
    ROUND(SUM((o2.quantity * o2.list_price) * (1 - o2.discount)),2) AS total_revenue
FROM orders AS o1
JOIN order_items AS o2
USING (order_id)
JOIN stores AS s
USING (store_id)
JOIN products AS p
ON o2.product_id = p.product_id
JOIN categories AS c
ON p.category_id = c.category_id
GROUP BY s.store_name,
c.category_name
ORDER BY s.store_name,
total_revenue DESC;

-- ORDERS NUMBER OF ITEMS FOR EACH CATEGORY

SELECT c.category_name,
	p.product_name,
    COUNT(o.order_id) AS orders_num
FROM order_items o
LEFT JOIN products p
USING(product_id)
JOIN categories c
ON p.category_id = c.category_id
GROUP BY c.category_name,
p.product_name
ORDER BY orders_num DESC;

-- BEST BRANDS ON ORDERS NUMBER

SELECT c.category_name,
	b.brand_name,
	COUNT(o.order_id) AS orders_num
FROM order_items o
LEFT JOIN products p
USING(product_id)
JOIN brands b
ON p.brand_id = b.brand_id
JOIN categories c
ON p.category_id = c.category_id
GROUP BY b.brand_name,
c.category_name
ORDER BY c.category_name;

## Dashboard
 <img width="452" height="230" alt="image" src="https://github.com/user-attachments/assets/8cf2d6f0-9341-4ff6-a687-e51a4bf11b1a" />

## Key Findings
- Baldwin Bikes generated the highest total revenue and recorded the largest number of orders
- Revenue peaked in 2017 across all stores due to an increase in average order price, order number did not increase as significantly as revenue
- Revenue declined significantly from 2017 to 2018 due to an order decrease between the two years, Baldwin Bikes showed the biggest decrease
- Average price per order increased over the years with a spike in 2018 for every shop
- Top 3 categories on revenue are the same for Baldwin Bikes and Rowlett Bikes, being 1st Mountain bikes, 2nd Road bikes and 3rd Cruisers bicycles, while Santa Cruz Bikes made more revenue on Electric bikes being in the 3rd place
-  Electra and Trek brands are the most popular brands between categories based on orders number

## Skills Demonstrated
- SQL
1.	JOINs
2.	Common Table Expressions (CTEs)
3.	Aggregate functions
4.	GROUP BY
5.	ORDER BY

- Excel
1.	PivotTables
2.	PivotCharts
3.	Interactive slicers
4.	KPI cards
5.	Dashboard design

