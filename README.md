## Olist Ecommerce SQL Project
------
### Brazilian E-Commerce Public Dataset by Olist

 #### Welcome!

*    This is a Brazilian ecommerce public dataset of orders made at Olist Store. 

* The dataset has information of 100k orders from 2016 to 2018 made at multiple marketplaces in Brazil.

* Its features allows viewing an order from multiple dimensions: from order status, price, payment and freight performance to customer location, product attributes and finally reviews written by customers.

* We also released a geolocation dataset that relates Brazilian zip codes to lat/lng coordinates

-- --------------------------------------------------------

 *Database: `olist`*

```sql
DROP TABLE IF EXISTS customers;
CREATE TABLE customers
(customer_id VARCHAR(255) PRIMARY KEY,
customer_unique_id VARCHAR(255),
customer_zip_code_prefix INT,
city VARCHAR(255),
state VARCHAR(255)
);
```

```sql
DROP TABLE IF EXISTS locations;
CREATE TABLE locations
(zip_code_prefix INT  ,
latitude FLOAT,
longitude FLOAT,
city VARCHAR(255),
state VARCHAR(255)
);
```
```sql
DROP TABLE IF EXISTS order_items;
CREATE TABLE order_items
(order_id VARCHAR(255) ,
order_item_id INT ,
product_id VARCHAR(255),
seller_id VARCHAR(255),
shipping_limit_date TIMESTAMP,
price FLOAT,
freight_value FLOAT
);
```

```sql
CREATE TABLE order_payments
(order_id VARCHAR(255) ,
payment_sequential  INT,
payment_type VARCHAR(255),
payment_installments INT,
payment_value FLOAT
);
```

```sql
DROP TABLE IF EXISTS reviews;
CREATE TABLE reviews
(review_id VARCHAR(255),
order_id VARCHAR(255),
score INT,
comment_title VARCHAR(255),
comment_message VARCHAR(255),
creation_date TIMESTAMP,
answer_timestamp TIMESTAMP
);
```

```sql
CREATE TABLE orders 
(order_id VARCHAR(255) PRIMARY KEY,
customer_id VARCHAR(255),
order_status VARCHAR(255),
order_purchase_timestamp TIMESTAMP,
order_approved_at TIMESTAMP,
order_delivered_carrier_date TIMESTAMP,
order_delivered_customer_date TIMESTAMP,
order_estimated_delivery_date TIMESTAMP
);
```

```sql
CREATE TABLE products
(product_id VARCHAR(255) PRIMARY KEY,
category_name VARCHAR(255),
name_lenghth INT,
description_lenghth INT,
photos_qty INT,
weight_g FLOAT,
length_cm FLOAT,
height_cm FLOAT,
width_cm FLOAT
);
```

```sql
DROP TABLE IF EXISTS sellers;
CREATE TABLE sellers
(seller_id VARCHAR(255) PRIMARY KEY, 
zip_code_prefix INT,
city VARCHAR(255),
state VARCHAR(255)
);
```

```sql
CREATE TABLE product_category_name_translation
(product_category_name VARCHAR(255),
product_category_name_english VARCHAR(255)
);
```

-----------

**Create the foreign keys**

```sql
ALTER TABLE orders
ADD FOREIGN KEY (customer_id) REFERENCES customers(customer_id);
```

```sql
ALTER TABLE order_items
ADD FOREIGN KEY (order_id) REFERENCES orders(order_id);
```

```sql
ALTER TABLE order_payments
ADD FOREIGN KEY (order_id) REFERENCES orders(order_id);
```

```sql
ALTER TABLE order_items
ADD FOREIGN KEY (product_id) REFERENCES products(product_id);
```

```sql
ALTER TABLE order_items
ADD FOREIGN KEY (seller_id) REFERENCES sellers(seller_id);
```

```sql
ALTER TABLE reviews
ADD FOREIGN KEY (order_id) REFERENCES orders(order_id);
```

```sql
ALTER TABLE customers
ADD FOREIGN KEY (customer_zip_code_prefix) REFERENCES locations(zip_code_prefix);
```

**This is a weak relationship*

```sql
ALTER TABLE sellers
ADD FOREIGN KEY (zip_code_prefix) REFERENCES locations(zip_code_prefix);
````
 
 *This is a weak relationship*


------

### ANALYSIS FOR THE DATABASE

### POSSIBLE QUESTIONS TO BE ANSWERED

#### 📊 Sales & Revenue KPIs
* What is the total revenue generated from all sales?
* Which product category contributes the most to overall revenue?
* What is the average order value (AOV) per customer?
* Which are the top 10 best-selling products in terms of revenue?
* Which are the top 10 best-selling products in terms of revenue? 
* How does revenue trend over time (yearly)?

  ---------

#### 📦 Inventory & Order Management KPIs
* Which products have the highest and lowest stock levels?
* What is the stock turnover rate for each product category?
* Which stores have the highest and lowest stock availability?
* Which products are frequently out of stock?
* How many orders are placed but canceled before fulfillment?

---------------
#### 🛍️ Customer & Marketing KPIs
* What percentage of customers are new vs. returning?
* Which customer segment contributes the most to revenue?
* Which cities or regions have the highest number of customers?
* What is the customer retention rate over the last 6 months?
* Which marketing campaigns drive the most sales?

------------------

#### 🚚 Logistics & Operational KPIs
* What is the average order fulfillment time across all stores?
* What percentage of deliveries are completed on time?
* Which store has the fastest and slowest delivery times?
* What is the return rate for products, and which products are returned most frequently?
* What are the peak hours/days for sales in each store?

**N/B NOT ALL QSTNS APPLY IN OUR SCENIRIOS, IT CONTAIN KEY KPI OF OLIST**

*These questions will help in analyzing performance, optimizing inventory* 
*improving sales strategies, and enhancing customer satisfaction. 🚀*

-------------------------------

#### BENCHMARK ANALYSIS

1.Clustering
* Some customers didn't write a review. But why are they happy or mad?

2. Sales Prediction:
* With purchase date information you'll be able to predict future sales.

3. Delivery Performance:
* You will also be able to work through delivery performance and find ways to optimize delivery times.

4. Product Quality:
*Enjoy yourself discovering the products categories that are more prone to customer insatisfaction.

-----------------------------------

##### EDA ANALYSIS

```sql
SELECT * FROM customers;
```

```sql
SELECT * FROM customers
WHERE customer_id IS NULL
OR customer_unique_id IS NULL
OR customer_zip_code_prefix IS NULL
OR city IS NULL
OR state IS NULL;
```
**No missing values*

```sql
SELECT * FROM locations;
```

```sql
SELECT * FROM locations
WHERE zip_code_prefix IS NULL
OR latitude IS NULL
OR longitude IS NULL
OR city IS NULL
OR state IS NULL;
```
*No missing values*

```sql
SELECT * FROM order_items;
```

```sql
SELECT * FROM order_items
WHERE order_id IS NULL
OR order_item_id IS NULL
OR product_id IS NULL
OR seller_id IS NULL
OR shipping_limit_date IS NULL
OR price IS NULL
OR freight_value IS NULL;
```
*No missing values*

```sql
SELECT * FROM order_payments;
```

```sql
SELECT * FROM order_payments
WHERE order_id IS NULL
OR payment_sequential IS NULL
OR payment_type IS NULL
OR payment_installments IS NULL
OR payment_value IS NULL;
```
*No missing values*

```sql
SELECT * FROM reviews;
```

```sql
SELECT * FROM reviews
WHERE review_id IS NULL
OR order_id IS NULL
OR score IS NULL
OR comment_title IS NULL
OR comment_message IS NULL
OR creation_date IS NULL
OR answer_timestamp IS NULL;
```

*there are missing values in the reviews table for the columns comment_title, comment_message, creation_date and answer_timestamp*
*we cannot drop the rows with missing values because we will need them to identify the customers that did not write a review*

```sql
SELECT * FROM orders;
```

```sql
SELECT * FROM orders
WHERE order_id IS NULL
OR customer_id IS NULL
OR order_status IS NULL
OR order_purchase_timestamp IS NULL
OR order_approved_at IS NULL
OR order_delivered_carrier_date IS NULL
OR order_delivered_customer_date IS NULL
OR order_estimated_delivery_date IS NULL;
```

*we had 2980 missing values in the order_delivered_carrier_date column and 2965 missing values in the order_delivered_customer_date column*
*we need to update these rows for improved analysis*

```sql
UPDATE orders
SET order_delivered_carrier_date = '2021-01-01 00:00:00'
WHERE order_delivered_carrier_date IS NULL
```

```sql
UPDATE orders
SET order_delivered_customer_date = '2021-01-01 00:00:00'
WHERE order_delivered_customer_date IS NULL
```

*we have updated the missing values in the order_delivered_carrier_date and order_delivered_customer_date columns*

```sql
SELECT * FROM products;
```

```sql
SELECT * FROM products
WHERE product_id IS NULL
OR category_name IS NULL
OR name_lenghth IS NULL
OR description_lenghth IS NULL
OR photos_qty IS NULL
OR weight_g IS NULL
OR length_cm IS NULL
OR height_cm IS NULL
OR width_cm IS NULL;
```

*missing values in the columns name_lenghth, description_lenghth, photos_qty, weight_g, length_cm, height_cm and width_cm*
*we should update  these rows for improved analysis*

```sql
UPDATE products
SET category_name = 'unknown'
WHERE category_name IS NULL
```

```sql
UPDATE products
SET name_lenghth = 0
WHERE name_lenghth IS NULL
;
```

```sql
UPDATE products
SET description_lenghth = 0
WHERE description_lenghth IS NULL
;
```

```sql
UPDATE products
SET photos_qty = 0
WHERE photos_qty IS NULL
;
```

```sql
UPDATE products
SET weight_g = 0
WHERE weight_g IS NULL
;
```

```sql
UPDATE products
SET length_cm = 0
WHERE length_cm IS NULL
;
```

```sql
UPDATE products
SET height_cm = 0
WHERE height_cm IS NULL
;
```

```sql
UPDATE products
SET width_cm = 0
WHERE width_cm IS NULL
;
```

```sql
SELECT * FROM sellers;
```

```sql
SELECT * FROM sellers
WHERE seller_id IS NULL
OR zip_code_prefix IS NULL
OR city IS NULL
OR state IS NULL;
```
*No missing values*

```sql
SELECT * FROM product_category_name_translation;
```
##### our database is now clean and ready for analysis

--------------

#### BENCHMARK QUESTIONS

1. Clustering:
* Some customers didn't write a review. But why are they happy or mad?
  
```sql
SELECT * FROM reviews
```

```sql
SELECT 
c.customer_id,
r.score,
r.comment_title,
r.comment_message
 FROM customers  AS c
LEFT JOIN orders AS o
ON c.customer_id = o.customer_id
JOIN reviews AS r
ON o.order_id = r.order_id
WHERE r.comment_title IS NULL
OR r.comment_message IS NULL
```


2. Sales Prediction:
* With purchase date information you'll be able to predict future sales.

```sql
SELECT 
        o.customer_id,
        o.order_purchase_timestamp,
        oi.price,
        SUM(oi.price) AS total_revenue
FROM orders AS o 
LEFT JOIN order_items AS oi
ON o.order_id = oi.order_id
WHERE o.order_purchase_timestamp IS NOT NULL
OR oi.price IS NOT NULL
GROUP BY 1,2,3
ORDER BY 4 DESC
```

3. Delivery Performance:
* You will also be able to work through delivery performance and find ways to optimize delivery times.

```sql
SELECT 
        o.order_id,
        o.order_purchase_timestamp,
        o.order_delivered_customer_date,
        o.order_estimated_delivery_date,
        o.order_delivered_customer_date - o.order_estimated_delivery_date AS delivery_time
FROM orders AS o
WHERE o.order_delivered_customer_date IS NOT NULL
OR o.order_estimated_delivery_date IS NOT NULL
ORDER BY 5 DESC
```

4. Product Quality:
* Enjoy yourself discovering the products categories that are more prone to customer satisfaction.

```sql
SELECT 
      t.product_category_name_english,
      ROUND(SUM(oi.price)::integer,0) AS total_revenue
FROM products AS p
JOIN product_category_name_translation AS t
ON p.category_name = t.product_category_name
LEFT JOIN order_items AS oi
ON p.product_id = oi.product_id
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
```

*top 10 products categories that are more prone to customer satisfaction*

------------------------------

#### SOLUTIONS FOR BUSINESS PROBLEMS

###### 📊 Sales & Revenue KPIs

1. What is the total revenue generated from all sales?

```sql
WITH sales_generated 
AS 
(
SELECT 
       s.city,
        s.state,
       s.seller_id,
       ROUND(SUM(oi.price)::integer,2) AS total_revenue,
       RANK() OVER (PARTITION BY s.state ORDER BY SUM(oi.price) DESC) AS rank
FROM sellers AS s
JOIN order_items AS oi
ON s.seller_id = oi.seller_id
GROUP BY 1,2,3
ORDER BY 4 DESC
)
SELECT 
      SUM(total_revenue) AS total_revenue
FROM sales_generated
```


2. Which product category contributes the most to overall revenue?

```sql
SELECT 
       t.product_category_name_english,
       ROUND(SUM(oi.price)::integer,2) AS total_revenue
FROM order_items AS oi
LEFT JOIN products AS p 
ON oi.product_id = p.product_id
INNER JOIN product_category_name_translation AS t 
ON p.category_name = t.product_category_name
GROUP BY 1
ORDER BY 2 DESC 
```

3. What is the average order value (AOV) per customer?

```sql
WITH aov AS 
(
SELECT 
       c.customer_id,
       SUM(oi.price) AS total_spend_per_cust
FROM customers AS c
LEFT JOIN orders AS o 
ON o.customer_id = c.customer_id
INNER JOIN order_items AS oi 
ON oi.order_id = o.order_id
GROUP BY 1
)
SELECT 
        customer_id ,
        ROUND(AVG(total_spend_per_cust::integer),0) AS aov_per_cust
FROM aov
GROUP BY 1
ORDER BY 2 DESC 
```

4. Which are the top 10 best-selling products in terms of revenue?

```sql
SELECT 
       t.product_category_name_english,
       ROUND(SUM(oi.price)::integer,2) AS total_revenue
FROM products AS p 
LEFT JOIN order_items AS oi 
ON p.product_id = oi.product_id
INNER JOIN product_category_name_translation AS t 
ON p.category_name = t.product_category_name
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
```

5. How does revenue trend over time (yearly)?

```sql
WITH yearly_revenues
AS 
(
SELECT 
      o.order_purchase_timestamp,
      ROUND(SUM(oi.price)::integer,2) AS total_revenue,
      EXTRACT(YEAR FROM o.order_purchase_timestamp) AS year
FROM order_items AS oi 
LEFT JOIN orders AS o 
ON oi.order_id = o.order_id
GROUP BY 1
ORDER BY 2 DESC
)
SELECT 
     year ,
     SUM(total_revenue) AS yearly_revenues
FROM yearly_revenues
GROUP BY 1
ORDER BY 2 DESC
```
------------------------------------------

##### 📦 Inventory & Order Management KPIs

1. How many orders are placed but canceled before fulfillment?

```sql
SELECT 
      *
FROM orders
WHERE order_status = 'cancelled'
```

 *our data had no cancelled orders*

 ------------------------

##### 🛍️ Customer & Marketing KPIs

1. What percentage of customers are new vs. returning?

```sql
WITH customer_orders AS (
    SELECT 
        customer_id,
        COUNT(order_id) AS order_count
    FROM orders
    GROUP BY customer_id
),
customer_classification AS (
    SELECT 
        customer_id,
        CASE 
            WHEN order_count = 1 THEN 'new'
            ELSE 'returning'
        END AS customer_type
    FROM customer_orders
),
customer_counts AS (
    SELECT 
        customer_type,
        COUNT(customer_id) AS customer_count
    FROM customer_classification
    GROUP BY customer_type
)
SELECT 
    customer_type,
    customer_count,
    ROUND((customer_count * 100.0 / SUM(customer_count) OVER ()), 2) AS percentage
FROM customer_counts;
```

2. Which customer segment contributes the most to revenue?

```sql
WITH customer_segment AS 
(
SELECT 
     DISTINCT c.state,
     c.city,
     ROUND(SUM(oi.price)::integer,2) AS total_revenue,
     RANK () OVER (PARTITION BY c.city ORDER BY ROUND(SUM(oi.price)::integer,2) DESC) AS rank
FROM customers AS c 
LEFT JOIN orders AS o 
ON o.customer_id = c.customer_id
INNER JOIN order_items AS oi 
ON oi.order_id = o.order_id
GROUP BY 1,2
ORDER BY 3 DESC
) 
SELECT 
      *
FROM customer_segment 
WHERE rank = 1
```
*classification of customers based on their state of purchase (orders)*

2. Which cities or regions have the highest number of customers?

```sql
WITH cust_region
AS
(
SELECT 
       state,
       city,
       COUNT(customer_id) AS no_of_customers,
       RANK () OVER (PARTITION BY state ORDER BY COUNT(customer_id) DESC) AS rank
FROM customers
GROUP BY 1, 2
ORDER BY 3 DESC 
)
SELECT 
      * 
FROM cust_region 
WHERE rank = 1
```


##### 🚚 Logistics & Operational KPIs

1. What percentage of deliveries are completed on time?

```sql
WITH delivery_status AS (
    SELECT 
        order_id,
        CASE 
            WHEN order_delivered_customer_date <= order_estimated_delivery_date THEN 'on_time'
            ELSE 'late'
        END AS delivery_status
    FROM orders
    WHERE order_delivered_customer_date IS NOT NULL
    AND order_estimated_delivery_date IS NOT NULL
),
delivery_counts AS (
    SELECT 
        delivery_status,
        COUNT(order_id) AS count
    FROM delivery_status
    GROUP BY delivery_status
)
SELECT 
    delivery_status,
    count,
    ROUND((count * 100.0 / SUM(count) OVER ()), 2) AS percentage
FROM delivery_counts;
```


3. What are the peak hours/days for sales in each store?
   
*Peak hours for sales in each store*

```sql
WITH hourly_sales AS (
    SELECT 
        s.seller_id,
        EXTRACT(HOUR FROM o.order_purchase_timestamp) AS hour,
        SUM(oi.price) AS total_sales
    FROM sellers AS s
    JOIN order_items AS oi ON s.seller_id = oi.seller_id
    JOIN orders AS o ON oi.order_id = o.order_id
    GROUP BY s.seller_id, hour
),
peak_hours AS (
    SELECT 
        seller_id,
        hour,
        total_sales,
        RANK() OVER (PARTITION BY seller_id ORDER BY total_sales DESC) AS rank
    FROM hourly_sales
)
SELECT 
    seller_id,
    hour AS peak_hour,
    total_sales
FROM peak_hours
WHERE rank = 1;
```

*Peak days for sales in each store*

```sql
WITH daily_sales AS (
    SELECT 
        s.seller_id,
        EXTRACT(DOW FROM o.order_purchase_timestamp) AS day_of_week,
        ROUND(SUM(oi.price)::integer, 2) AS total_sales
    FROM sellers AS s
    JOIN order_items AS oi ON s.seller_id = oi.seller_id
    JOIN orders AS o ON oi.order_id = o.order_id
    GROUP BY s.seller_id, day_of_week
),
peak_days AS (
    SELECT 
        seller_id,
        day_of_week,
        total_sales,
        RANK() OVER (PARTITION BY seller_id ORDER BY total_sales DESC) AS rank
    FROM daily_sales
)
SELECT 
    seller_id,
    day_of_week AS peak_day,
    total_sales
FROM peak_days
WHERE rank = 1;
```

-------------------------------

### ANALYSIS AND RECOMMENDATIONS

##### Benchmark Analysis

**Clustering**

* By analyzing customers who didn't write a review, we can identify patterns and reasons for their satisfaction or dissatisfaction.
* This helps in improving customer experience and addressing potential issues.

**Sales Prediction**

* By using purchase date information, we can predict future sales trends.
* This helps in planning inventory, marketing campaigns, and resource allocation.

**Delivery Performance**

* By analyzing delivery times, we can identify areas for improvement in the delivery process.
* This helps in optimizing delivery times and enhancing customer satisfaction.

**Product Quality**

* By analyzing the product categories that are more prone to customer satisfaction,
* we can focus on improving product quality and addressing issues in specific categories.
* These conclusions provide a comprehensive overview of the performance and areas for improvement in your e-commerce business.
* They help in making data-driven decisions to optimize operations, enhance customer satisfaction, and drive business growth.

------------------------------

**Sales & Revenue KPIs**
* Total Revenue Generated from All Sales:
* The total revenue generated from all sales can be calculated using the sales_generated CTE.
* This provides a comprehensive view of the revenue performance across different sellers and states.
* 
**Product Category Contributing the Most to Overall Revenue**
* By joining the order_items and products tables, and aggregating the sales data, we can identify which product categories contribute the most to overall revenue.
* This helps in understanding which categories are driving the business.

  **Average Order Value (AOV) per Customer**
* The AOV per customer is calculated by summing the total spend per customer and then averaging it.
* This metric is crucial for understanding customer spending behavior and can help in tailoring marketing strategies.

**Top 10 Best-Selling Products in Terms of Revenue:**

* By aggregating sales data and ranking the products, we can identify the top 10 best-selling products.
* This information is valuable for inventory management and marketing focus.

**Revenue Trend Over Time (Yearly)**

* The yearly revenue trend is analyzed by extracting the year from the order purchase timestamps and summing the sales.
* This helps in understanding the business growth and seasonal trends.

**Inventory & Order Management KPIs**
*Orders Placed but Canceled Before Fulfillment*
* By filtering the orders table for the cancelled status, we can identify the number of orders that were canceled before fulfillment.
* This metric is important for understanding customer satisfaction and operational efficiency.

**Customer & Marketing KPIs**
*Percentage of Customers Who Are New vs. Returning:*
* By classifying customers based on their order count, we can determine the percentage of new vs. returning customers.
* This helps in understanding customer loyalty and the effectiveness of customer retention strategies.

**Customer Segment Contributing the Most to Revenue:**
* By analyzing the revenue contribution from different customer segments (based on state and city), we can identify which segments are the most valuable.
* This information is useful for targeted marketing and resource allocation.

**Cities or Regions with the Highest Number of Customers:**
* By ranking the cities or regions based on the number of customers, we can identify the areas with the highest customer concentration.
* This helps in regional marketing and expansion planning.

**Logistics & Operational KPIs**
*Percentage of Deliveries Completed on Time:*
* By comparing the actual delivery date with the estimated delivery date, we can calculate the percentage of on-time deliveries.
* This metric is crucial for assessing the efficiency of the logistics operations.

*Peak Hours/Days for Sales in Each Store:*
* By analyzing the sales data based on the hour and day of the week, we can identify the peak hours and days for sales in each store.
* This information is valuable for staffing, inventory management, and promotional activities.

--------------------------------------

**AUTHOR** *Awal Alier*

*reading between the data*

-------------------------------------
