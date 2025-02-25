# 📊 Food delivery using Advanced SQL

## 🚀 Executive Summary
This project leverages SQL-based data analysis to extract key business insights for Zomato, a leading food delivery platform in India. The findings help optimize restaurant performance, improve customer retention, enhance delivery efficiency, and boost revenue streams.

## 🛠️ Advanced SQL Techniques Used
✅ **Data Cleaning & Transformation** (handling NULL values, deduplication)

✅ **Aggregation & Grouping** (`SUM`, `AVG`, `COUNT`, `GROUP BY`, `HAVING`)

✅ **Joins & Relationships** (`INNER JOIN`, `LEFT JOIN`, `SELF JOIN`)  

✅ **Ranking & Window Functions** (`RANK()`, `DENSE_RANK()`, `PARTITION BY`, `OVER()`)  

✅ **Filtering & Conditional Logic** (`WHERE`, `CASE`, `HAVING`, `YEAR` FROM date)  

✅ **Subqueries & Common Table** Expressions (CTEs) (WITH CTEs, Nested Queries)  

## 🔍 Key Findings & Business Insights from Zomato Data Analysis
### 1️⃣ Customer Ordering Patterns & Preferences
✔ **Most Ordered Dishes:**
- The top 5 most frequently ordered dishes by **Arjun Mehta** in the last year were identified, showcasing **repeat purchase trends**.
- This insight helps **Zomato personalize marketing campaigns** (e.g., targeted promotions or discounts on preferred dishes).
  
✔ **Peak Ordering Time Slots:**
- The busiest ordering time slots occur between **8 PM – 10 PM**, followed by **12 PM – 2 PM**.
- This finding helps in **optimizing restaurant staffing, kitchen operations, and delivery fleet allocation** to reduce delays during high-demand hours.

✔ **High-Value Customers (HVCs):**
- Customers who have **spent over ₹100,000** on food orders were identified.
- **Customer segmentation strategies** (such as loyalty rewards or exclusive discounts) can be applied to **retain premium users** and increase their lifetime value.

✔ **Customer Churn Analysis:**
- Several customers who were **active in 2023** have **not placed any orders in 2024**.
- **Retention strategies** such as **personalized offers and re-engagement campaigns** can be implemented to bring back inactive users.


### 2️⃣ Restaurant Performance & Revenue Optimization
✔ **Top Revenue-Generating Restaurants:**
- Restaurants were ranked by total revenue across different cities.
- **City-wise performance comparison** helps in **targeting high-performing areas for expansion** and identifying underperforming restaurants for improvement.

✔ **Most Popular Dish by City:**
- The most frequently ordered dish in each city was identified.
- Restaurants can use this insight to **optimize their menus and promote best-selling items** to maximize revenue.

✔ **Order Cancellation Rate Analysis:**
- A year-over-year comparison revealed **cancellation rate fluctuations** across different restaurants.
- **High cancellation rates** indicate potential issues like **slow delivery, incorrect orders, or poor food quality**, which require targeted intervention.

✔ **Orders Without Delivery:**
- Some restaurants experienced **a high percentage of orders that were placed but not delivered.**
- This may be due to **rider shortages or operational inefficiencies** and should be addressed to **improve customer experience**.

### 3️⃣ **Delivery Efficiency & Logistics Optimization**
✔ **Rider Performance & Average Delivery Time:**
- The **average delivery time per rider** was calculated, identifying riders who consistently deliver faster or slower than average.
- This insight helps **reward top-performing riders** and **train underperforming ones**, leading to **better delivery efficiency**.

✔ **Delivery Time Analysis:**
- The difference between **order time and delivery time** was analyzed to pinpoint **bottlenecks in the process**.
- Zomato can **adjust delivery routes, optimize rider assignments, and leverage AI-powered logistics solutions** for **faster delivery**.

### 4️⃣ **Financial Insights & Revenue Growth Strategies**
✔ **Average Order Value (AOV) Analysis:**
- Customers who placed **more than 750 orders** had an **above-average AOV**.
- **Upselling and cross-selling strategies** (e.g., suggesting add-ons or combo deals) can be used to **increase order value further**.

✔ **Total Revenue Contribution by City & Restaurant:**
- Some cities generate significantly **higher revenue than others**, influencing **strategic decisions on market expansion**.
- High-performing restaurants can **become models for operational excellence**, while underperforming ones may need **menu changes or better promotions**.

## 📢 Business Recommendations
📌 **1. Optimize Restaurant Operations for Peak Demand**
- **Ensure staffing and kitchen resources** are aligned with peak ordering times (8-10 PM & 12-2 PM).
- Offer **priority delivery services** for premium users during busy hours.
  
📌 **2. Implement Customer Retention Strategies**
- **Target inactive customers** with **personalized discounts** based on their previous orders.
- Introduce a **loyalty program for high-value customers** to increase retention.
  
📌 **3. Improve Delivery Fleet Performance**
- Assign **high-performing riders to high-volume areas.**
- Optimize **route planning using AI-based algorithms** to reduce delivery time.
  
📌 **4. Reduce Order Cancellation Rate**
- Identify **top restaurants with high cancellation rates** and **improve order accuracy and speed.**
- Ensure **better rider allocation** to minimize delivery failures.
  
📌 **5. Enhance Menu & Pricing Strategies**
- Promote **best-selling dishes in each city** through targeted ads.
- Encourage **bundle deals and meal combos** to increase AOV.

## Project Structure

![image Alt](https://github.com/lekhakasinadhuni07/Food_delivery_analysis/blob/cfe0adb5c9d15da2d96f1aeb6bca60aac520fedf/zomato.png)

## 📜 SQL Queries
### **Database Setup and Table Creation:** 
```sql
CREATE DATABASE zomato;

CREATE TABLE customers
(
customer_id INT PRIMARY KEY,
customer_name VARCHAR(25),
reg_date DATE
);

CREATE TABLE restaurants
(
restaurant_id INT PRIMARY KEY,
restaurant_name VARCHAR (55),
city VARCHAR(50),
opening_hours VARCHAR(55)
);

CREATE TABLE orders
(
order_id INT PRIMARY KEY,
customer_id INT, -- coming from customers table (FK)
restaurant_id INT, -- coming from restaurants table (FK)
order_item VARCHAR(50),
order_date DATE,
order_time TIME,
order_status VARCHAR(25),
total_amount FLOAT
);

-- Adding FK CONSTRAINT to orders table --
ALTER TABLE orders
ADD CONSTRAINT fk_customers -- (a random name given to the constraint)
FOREIGN KEY (customer_id)
REFERENCES customers(customer_id); -- (mention from which table(customers) is the FK (customer_id) coming from)

-- Adding FK CONSTRAINT to orders table --
ALTER TABLE orders
ADD CONSTRAINT fk_restaurants
FOREIGN KEY (restaurant_id)
REFERENCES restaurants(restaurant_id);

CREATE TABLE riders
(
rider_id INT PRIMARY KEY,
rider_name VARCHAR(55),
sign_up DATE
);

DROP TABLE IF EXISTS deliveries;

-- to create CONSTRAINT directly while creating a table --

CREATE TABLE deliveries
(
delivery_id INT PRIMARY KEY,
order_id INT, -- comings from orders table (FK)
delivery_status VARCHAR(35),
delivery_time TIME,
rider_id INT, -- coiming from riders table (FK)
CONSTRAINT fk_orders FOREIGN KEY (order_id) REFERENCES orders(order_id),
CONSTRAINT fk_riders FOREIGN KEY (rider_id) REFERENCES riders(rider_id)
);
```

## Analysis & Reports

### 1. Write a query to find the top 5 most frequently ordered dishes by customer called "Arjun Mehta" in the last 1 year.
```sql
SELECT *
FROM -- TABLE NAME
(SELECT 
    c.customer_id,
    c.customer_name,
    o.order_item,
    COUNT(*) AS total_orders,
    DENSE_RANK() OVER (PARTITION BY c.customer_id ORDER BY COUNT(*) DESC) AS order_rank -- dense rank is used so that the rank order is given sequentially when there are multiple ranks
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE 
o.order_date >= DATE_SUB('2024-09-01', INTERVAL 365 DAY) AND
c.customer_name = 'Arjun Mehta'
GROUP BY 
c.customer_id, 
c.customer_name, 
o.order_item
ORDER BY 
c.customer_id, 
total_orders DESC) as t1
WHERE order_rank <= 5;
```
### 2. Popular Time Slots: 
Identify the time slots during which the most orders are placed based on 2-hour intervals.

- Approach 1
```sql
/* EXAMPLE
SELECT 00:59:59 AM - 01:00:00 AM -- 0
SELECT 01:00:01 AM - 01:59:59 AM -- 1
SINCE THE QUERY TAKES THE HOUR, THE NUMBER REMAINS THE SAME TILL THE LAST SECOND
*/
SELECT 
	CASE
    WHEN HOUR(order_time) BETWEEN 0 AND 1 THEN '00:00 - 02:00'
	WHEN HOUR(order_time) BETWEEN 2 AND 3 THEN '02:00 - 04:00'
	WHEN HOUR(order_time) BETWEEN 4 AND 5 THEN '04:00 - 06:00'
    WHEN HOUR(order_time) BETWEEN 6 AND 7 THEN '06:00 - 08:00'
    WHEN HOUR(order_time) BETWEEN 8 AND 9 THEN '08:00 - 10:00'
    WHEN HOUR(order_time) BETWEEN 10 AND 11 THEN '10:00 - 12:00'
    WHEN HOUR(order_time) BETWEEN 12 AND 13 THEN '12:00 - 14:00'
    WHEN HOUR(order_time) BETWEEN 14 AND 15 THEN '14:00 - 16:00'
    WHEN HOUR(order_time) BETWEEN 16 AND 17 THEN '16:00 - 18:00'
    WHEN HOUR(order_time) BETWEEN 18 AND 19 THEN '18:00 - 20:00'
    WHEN HOUR(order_time) BETWEEN 20 AND 21 THEN '20:00 - 22:00'
    WHEN HOUR(order_time) BETWEEN 22 AND 23 THEN '22:00 - 24:00'
	END AS time_slots,
    COUNT(order_id) as count_orders
FROM orders
GROUP BY time_slots
ORDER BY count_orders DESC;
```
- Approach 2 
```sql
SELECT
FLOOR(HOUR(order_time)/2)*2 as start_time, -- floor is for calculating divison without decimal
FLOOR(HOUR(order_time)/2)*2 +2 as end_time,
COUNT(*) as total_orders
FROM orders
GROUP BY start_time, end_time
ORDER BY total_orders DESC;
```
### 3. Order Value Analysis: 
Find the average order value per customer who has placed more than 750 orders.
```sql
SELECT 
c.customer_name,
COUNT(*) AS total_orders,
ROUND(AVG(total_amount),2) AS avg_order_value
FROM orders o
JOIN customers c
ON o.customer_id = c.customer_id
GROUP BY c.customer_name
HAVING total_orders > 750;
```
### 4. High-Value Customers
List the customers who have spent more than 100K in total on food orders.
```sql
SELECT 
c.customer_id,
c.customer_name,
SUM(o.total_amount) AS total_amount_spent
FROM orders o 
JOIN customers c
ON o.customer_id = c.customer_id
GROUP BY c.customer_id, c. customer_name
HAVING SUM(o.total_amount) > 100000;
```
### 5. Orders Without Delivery
Write a query to find orders that were placed but not delivered. 
```sql
SELECT 
r.restaurant_name,
COUNT(o.order_id) as count_of_not_delivered_orders
FROM orders o
LEFT JOIN restaurants r
ON o.restaurant_id = r.restaurant_id
LEFT JOIN deliveries d
ON o.order_id = d.order_id
WHERE d.delivery_id IS NULL
GROUP BY 1
ORDER BY 2 DESC;
```
### 6. Restaurant Revenue Ranking: 
```sql
WITH ranking_table
AS
(
SELECT
r.restaurant_name,
r.city,
SUM(o.total_amount) AS total_revenue,
RANK () OVER(PARTITION BY r.city ORDER BY SUM(o.total_amount) DESC) as rank_amount -- the rank column cannot be filtered in where condition, that is why creating query "WITH"
FROM orders o
JOIN restaurants r
ON o.restaurant_id = r.restaurant_id
WHERE o.order_date = DATE_SUB('2024-09-01', INTERVAL 365 DAY)
GROUP BY r.restaurant_name, r.city
)
SELECT *
FROM ranking_table
WHERE rank_amount = 1;
```
### 7. Most Popular Dish by City: 
```sql
WITH ranking_table
AS
(
SELECT 
r.city,
o.order_item AS dishes,
COUNT(order_id) AS count_orders,
RANK () OVER(PARTITION BY r.city ORDER BY COUNT(order_id) DESC) AS rank_orders
FROM orders o
JOIN restaurants r
ON o.restaurant_id = r.restaurant_id
GROUP BY o.order_item, r.city
) 
SELECT *
FROM ranking_table
WHERE rank_orders = 1
```
### 8. Customer Churn: 
Find customers who haven’t placed an order in 2024 but did in 2023.
```sql
SELECT  DISTINCT customer_id FROM orders
WHERE 
YEAR(order_date) = 2023 
AND 
customer_id NOT IN
(SELECT DISTINCT customer_id FROM orders 
WHERE YEAR(order_date) = 2024)
```
### 9. Cancellation Rate Comparison: 
Calculate and compare the order cancellation rate for each restaurant between the current year and the previous year.
```sql
WITH 
cancellations_ratio_2023
AS
(
SELECT 
o.restaurant_id,
COUNT(o.order_id) AS total_orders,
COUNT(CASE WHEN d.delivery_id IS NULL THEN 1 END) as total_cancellations_2023
FROM orders o
LEFT JOIN deliveries d
ON o.order_id = d.order_id
WHERE YEAR(o.order_date) = 2023
GROUP BY 1
),
cancellations_ratio_2024
AS
(
SELECT 
o.restaurant_id,
COUNT(o.order_id) AS total_orders,
COUNT(CASE WHEN d.delivery_id IS NULL THEN 1 END) as total_cancellations_2024
FROM orders o
LEFT JOIN deliveries d
ON o.order_id = d.order_id
WHERE YEAR(o.order_date) = 2024
GROUP BY 1
),
last_year_data
AS
(
SELECT
restaurant_id,
total_orders,
total_cancellations_2023,
ROUND(total_cancellations_2023/total_orders*100,2) AS cancel_ratio_23
FROM
cancellations_ratio_2023
),
current_year_data
AS
(
SELECT
restaurant_id,
total_orders,
total_cancellations_2024,
ROUND(total_cancellations_2024/total_orders*100,2) AS cancel_ratio_24
FROM
cancellations_ratio_2024
)
SELECT
last_year_data.restaurant_id,
last_year_data.cancel_ratio_23,
current_year_data.cancel_ratio_24
FROM last_year_data
JOIN current_year_data
ON last_year_data.restaurant_id = current_year_data.restaurant_id
```
### 10. Rider Average Delivery Time 
```sql
/* TIME_TO_SEC() converts both times into seconds.
The subtraction gives the difference in seconds.
SEC_TO_TIME() converts the result back into HH:MM:SS format.*/

SELECT 
o.order_id,
d.rider_id,
r.rider_name,
o.order_time,
d.delivery_time,
SEC_TO_TIME(
	CASE 
		WHEN TIME_TO_SEC(d.delivery_time) >= TIME_TO_SEC(o.order_time) THEN
			TIME_TO_SEC(d.delivery_time) - TIME_TO_SEC(o.order_time) 
		ELSE
			(TIME_TO_SEC('24:00:00') - TIME_TO_SEC(o.order_time)) + TIME_TO_SEC(d.delivery_time)
END) AS time_diff
FROM orders o
JOIN deliveries d
ON o.order_id = d.order_id
JOIN riders r
ON d.rider_id = r.rider_id
WHERE d.delivery_status = 'Delivered'
```
