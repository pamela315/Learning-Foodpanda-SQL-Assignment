# Learning-Foodpanda SQL Assignment

Created on: June 2021

This repository contains the code and instructions for the FoodPanda assignment in class. The assignment focuses on analyzing order data from FoodPanda using SQL queries. Below, you will find an overview of the assignment tasks and the corresponding SQL code
## Usage
To perform the analysis, follow these steps:
-  Set up the necessary database environment with the required tables (orders, vendors, customers, decline_reasons, vendor_zone) in the foodpanda_hk schema.
-  Execute the SQL code provided in Task 1 to create the combined_order table.
-  Execute the SQL code provided in Task 2 to add the order status column to the combined_order table.
-  Execute the SQL code provided in Task 3 to analyze the order data and obtain the desired insights.

## Task 1: Creating the Combined Order Table
In this task, we create a table called combined_order by joining multiple tables and selecting relevant columns. The SQL code for this task is as follows:
```SQL
CREATE TABLE combined_order AS
SELECT
    CAST(o.order_date AS DATE) AS order_date,
    vz.area AS zone_of_vendor,
    v.vendor_code AS vendor_code,
    v.vendor_name AS vendor_name,
    o.order_code AS order_code,
    c.full_name AS customer_name,
    c.email_address AS customer_email,
    o.food_value_local AS food_value,
    d.title AS order_decline_reason
FROM
    foodpanda_hk.orders o
    LEFT JOIN foodpanda_hk.vendors v ON o.vendor_id = v.Vendor_id
    LEFT JOIN foodpanda_hk.customers c ON o.order_date = c.customer_first_order
    LEFT JOIN foodpanda_hk.decline_reasons d ON o.decline_reason_id = d.decline_reason_id
    LEFT JOIN foodpanda_hk.vendor_zone vz ON v.Vendor_code = vz.Vendor_code
WHERE
    o.order_date BETWEEN '2019-06-03 00:00:00' AND '2019-06-05 23:59:59'
ORDER BY
    o.order_date DESC;
```
## Task 2: Adding Order Status to Combined Order Table
In this task, we add an order status column to the `combined_order` table based on the `status_id` value. The SQL code to accomplish this task is as follows:
```SQL
CASE
    WHEN status_id = 22 THEN 'Successful'
    WHEN status_id = 24 THEN 'Successful'
    WHEN status_id = 61 THEN 'Successful'
    WHEN status_id = 12 THEN 'Cancel'
    WHEN status_id = 21 THEN 'Cancel'
    WHEN status_id = 31 THEN 'Cancel'
    ELSE 'Incomplete'
END AS order_status,
```
## Task 3: Analyzing Order Data
In this task, we analyze the order data from the combined_order table. The SQL code for this task is as follows:
```SQL
SELECT
    zone_of_vendor,
    COUNT(order_code) AS order_no,
    COUNT(CASE WHEN order_status = 'Cancel' THEN 1 END) AS cancel_orders_no,
    SUM(food_value),
    (SELECT SUM(food_value) FROM combined_order WHERE status_id = 'Successful') AS food_value_successful_order,
    COUNT(customer_name)
FROM
    combined_order
WHERE
    order_date BETWEEN '2019-06-03' AND '2019-06-05'
GROUP BY
    zone_of_vendor
ORDER BY
    order_date DESC;
```
