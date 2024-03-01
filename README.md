# Querying MS SQL using [FLO](https://www.flo.com.tr/)'s Dataset

**Dataset Story**

The dataset consists of the information obtained from the past shopping behaviors of customers who made their last purchases from **FLO** as OmniChannel *(both online and offline shopper)* in 2020-2021.

**Variables**
- **master_id:** Customer ID
- **order_channel:** Shopping platform *(Android, ios, Desktop, Mobile, Offline)*
- **last_order_channel:** The channel where the most recent purchase was made
- **first_order_date:** Customer's first order date
- **last_order_date:** Customer's last order date
- **last_order_date_online:** Customer's last offline order date
- **last_order_date_offline:** Customer's last online order date
- **order_num_total_ever_online:** The total number of orders made by the customer online
- **order_num_total_ever_offline:** The total number of orders made by the customer offline
- **customer_value_total_ever_offline:** The total price paid by the customer for offline orders
- **customer_value_total_ever_online:** The total price paid by the customer for online orders
- **interested_in_categories_12:** List of categories the customer has shopped in the last 12 months

---

### Task 1: 
Create a table named **FLO** to include variables from a database named **'Customers'** and the given dataset. 

**Solution:**
- Creating Database:

~~~sql
CREATE DATABASE CUSTOMERS;
~~~

- Importing Dataset:

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_1_1.png)

Retrieve all rows and columns from the **'FLO'** table.

~~~sql
SELECT *
FROM FLO;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_1_2.png)

### Task 2: 
Write a query to show the number of distinct customers who made purchases.

**Solution:**

~~~sql
SELECT
    COUNT(DISTINCT master_id) AS TOTAL_CUSTOMER
FROM FLO;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_2.png)

### Task 3: 
Write a query to retrieve the total number of transactions and revenue.

**Solution:**

~~~sql
SELECT
    SUM(order_num_total_ever_online + order_num_total_ever_offline) AS TOTAL_TRANSACTION,
    SUM(customer_value_total_ever_online + customer_value_total_ever_offline) AS TOTAL_REVENUE
FROM FLO;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_3.png)

### Task 4: 
Write a query to calculate the average revenue per transaction.

**Solution:**

~~~sql
SELECT
    (SUM(customer_value_total_ever_online + customer_value_total_ever_offline) /
    SUM(order_num_total_ever_online + order_num_total_ever_offline)) AS AVG_REVENUE_PER_TRANSACTION
FROM FLO;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_4.png)

### Task 5: 
Write a query to retrieve the total revenue and number of transactions for purchases made through the last order channel *(**'last_order_channel'**)*.

**Solution:**

~~~sql
SELECT
    last_order_channel AS LAST_ORDER_CHANNEL,
    SUM(order_num_total_ever_online + order_num_total_ever_offline) AS TOTAL_TRANSACTION,
    SUM(customer_value_total_ever_online + customer_value_total_ever_offline) AS TOTAL_REVENUE
FROM FLO
GROUP BY last_order_channel
ORDER BY TOTAL_REVENUE DESC;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_5.png)

### Task 6: 
Write a query to retrieve the total revenue obtained in the breakdown of **'store_type'**.

**Solution:**

~~~sql
SELECT
    store_type AS STORE_TYPE,
    SUM(customer_value_total_ever_online + customer_value_total_ever_offline) AS TOTAL_REVENUE
FROM FLO
GROUP BY store_type
ORDER BY TOTAL_REVENUE DESC;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_6.png)

### Task 7: 
Write a query to retrieve the number of transactions breakdown by year *(Consider the customer's first order date (**'first_order_date'**) year)*.

**Solution:**

~~~sql
SELECT
    DATEPART(YEAR, first_order_date) AS YEAR_,
    SUM(order_num_total_ever_online + order_num_total_ever_offline) AS TOTAL_TRANSACTION
FROM FLO
GROUP BY DATEPART(YEAR, first_order_date)
ORDER BY YEAR_ DESC;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_7.png)

### Task 8: 
Write a query to calculate the average revenue per transaction based on the last order channel breakdown.

**Solution:**

~~~sql
SELECT
    last_order_channel AS LAST_ORDER_CHANNEL,
    (SUM(customer_value_total_ever_online + customer_value_total_ever_offline) /
    SUM(order_num_total_ever_online + order_num_total_ever_offline)) AS AVG_REVENUE_PER_TRANSACTION
FROM FLO
GROUP BY last_order_channel
ORDER BY AVG_REVENUE_PER_TRANSACTION DESC;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_8.png)

### Task 9: 
Write a query to find the most popular category in the last 12 months.

**Solution:**

~~~sql
SELECT
    interested_in_categories_12 AS LAST_12_MONTH_CATEGORIES,
    COUNT(*) AS CATEGORY_COUNT
FROM FLO
GROUP BY interested_in_categories_12;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_9.png)

### Task 10: 
Write a query to find the most preferred **'store_type'** information.

**Solution:**

~~~sql
SELECT TOP 1
    store_type AS STORE_TYPE,
    COUNT(*) AS STORE_TYPE_COUNT
FROM FLO
GROUP BY store_type;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_10.png)

### Task 11: 
Write a query to find the most popular category and the total amount spent in this category based on the last order channel *(**'last_order_channel'**)*.

**Solution:**

~~~sql
SELECT
    DISTINCT last_order_channel AS LAST_ORDER_CHANNEL,
    (SELECT TOP 1
        interested_in_categories_12
    FROM FLO
    WHERE last_order_channel=f.last_order_channel
    GROUP BY interested_in_categories_12
    ORDER BY SUM(order_num_total_ever_online + order_num_total_ever_offline) DESC
    ) AS POPULAR_CATEGORIES,
    (SELECT TOP 1
        SUM(order_num_total_ever_online + order_num_total_ever_offline)
    FROM FLO
    WHERE last_order_channel=f.last_order_channel
    GROUP BY interested_in_categories_12
    ORDER BY SUM (order_num_total_ever_online + order_num_total_ever_offline) DESC
    ) AS TOTAL_TRANSACTION
FROM FLO f;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_11.png)

### Task 12: 
Write a query to retrieve the ID of the person who made the most purchases.

**Solution:**

~~~sql
SELECT TOP 1
    master_id AS CUSTOMER_ID,
    SUM(order_num_total_ever_online + order_num_total_ever_offline) AS TOTAL_TRANSACTION
FROM FLO
GROUP BY master_id
ORDER BY TOTAL_TRANSACTION DESC;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_12.png)

### Task 13: 
Write a query to retrieve the average revenue per transaction and the average shopping day frequency *(shopping frequency)* of the person who made the most purchases.

**Solution:**

~~~sql
SELECT TOP 1
    master_id AS CUSTOMER_ID,
    (SUM(customer_value_total_ever_online + customer_value_total_ever_offline) /
    SUM(order_num_total_ever_online + order_num_total_ever_offline)) AS AVG_REVENUE_PER_TRANSACTION,
    DATEDIFF(DAY, first_order_date, last_order_date) / (SUM(order_num_total_ever_online + order_num_total_ever_offline)) AS AVG_SHOPPING_DAY_FREQUENCY
FROM FLO
GROUP BY master_id, first_order_date, last_order_date
ORDER BY SUM(order_num_total_ever_online + order_num_total_ever_offline) DESC;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_13.png)

### Task 14: 
Write a query to retrieve the customer who made the most purchases based on the last order channel (**'last_order_channel'**) breakdown.

**Solution:**

~~~sql
SELECT DISTINCT last_order_channel AS LAST_ORDER_CHANNEL,
    (SELECT TOP 1
        master_id
    FROM FLO
    WHERE last_order_channel=f.last_order_channel
    GROUP BY master_id
    ORDER BY SUM(customer_value_total_ever_online + customer_value_total_ever_offline) DESC
    ) AS CUSTOMER_ID,

    (SELECT TOP 1
        SUM(customer_value_total_ever_online + customer_value_total_ever_offline)
    FROM FLO
    WHERE last_order_channel=f.last_order_channel
    GROUP BY master_id
    ORDER BY SUM(customer_value_total_ever_online + customer_value_total_ever_offline) DESC
    ) AS TOTAL_REVENUE
FROM FLO f;
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_14.png)

### Task 15: 
Write a query to retrieve the ID of the person who made the most recent purchase *(There are multiple IDs making purchases on the maximum date; include all)*.

**Solution:**

~~~sql
SELECT
    master_id CUSTOMER_ID,
    last_order_date LAST_ORDER_DATE
FROM FLO
WHERE last_order_date = (SELECT MAX(last_order_date)FROM FLO);
~~~

![Output](https://github.com/oktay-acar/flo-sql/blob/main/img/flo_sql_15.png)

---

## Author
[Oktay Acar](https://github.com/oktay-acar)