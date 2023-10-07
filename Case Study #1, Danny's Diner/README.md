# Case Study #1, Danny’s Diner

I’m taking on the first case study in Danny Ma’s #8WeekSQLChallenge to test my SQL skills. Check out my results below!

<p align="center">
<img src="https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/13ca52c7-c941-4853-ac39-7253612003bb)">
</p>

## Table of Contents
- [Problem Statement](#problem-statement)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Creating Tables](#creating-tables)
- [Solutions](#solutions)
- [Bonus Questions](#bonus-questions)
- [Conclusion](#conclusion)

## Problem Statement
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program — additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided you with a sample of his overall customer data due to privacy issues — but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

Danny has shared with you 3 key datasets for this case study:
- sales
- menu
- members

## Entity Relationship Diagram

<p align="center">
<img src="https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/508c7c77-b4aa-4bd1-86ed-91a61e936c8d">
</p>

## Creating Tables
To begin the case study, I created all three tables using pgAdmin 4.

````sql

CREATE TABLE SALES
(customer_id VARCHAR(1),
 order_date DATE,
 product_id INTEGER);
 
 INSERT INTO SALES
 (customer_id, order_date, product_id)
 VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');

CREATE TABLE menu
(product_id INTEGER,
product_name VARCHAR(5),
price INTEGER);

INSERT INTO menu
(product_id, product_name, price)
VALUES 
 ('1', 'sushi', '10'),
 ('2', 'curry', '15'),
 ('3', 'ramen', '12');
  
CREATE TABLE members
(customer_id VARCHAR(1),
join_date DATE);

INSERT INTO members
(customer_id, join_date)
VALUES
 ('A', '2021-01-07'),
  ('B', '2021-01-09')

````
## Solutions
With the tables created, I can now answer the case study questions. Below are the queries that led me to the solutions.

### 1. What is the total amount each customer spent at the restaurant?

````sql
SELECT customer_id, CONCAT('$', SUM(price)) as total_ammount
FROM menu m 
INNER JOIN sales s
ON (m.product_id = s.product_id)
GROUP BY customer_id
ORDER BY customer_id
````

![1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/adf8e8c6-84b1-43cd-a93a-809da71a356e)


### 2. How many days has each customer visited the restaurant?

````sql
SELECT customer_id, COUNT(DISTINCT order_date) AS days_visited
FROM sales
GROUP BY customer_id
ORDER BY customer_id
````

![2](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/0f2a01af-3efa-4ee9-862e-1a767a875afc)


### 3. What was the first item from the menu purchased by each customer?

````sql
WITH cte AS (
 (SELECT 
 customer_id, 
 order_date, 
 product_name,
 DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY order_date)
 from sales s
 inner join menu m
 ON s.product_id = m.product_id)
)
SELECT DISTINCT customer_id, order_date, product_name, dense_rank
FROM CTE
WHERE dense_rank = 1
````

![3](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/0c8ced86-72fa-4dff-8737-2cc9d30af544)


### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

To find the most purchased item, I wrote this query:

````sql
SELECT product_name, COUNT(product_name) as number_of_times_purchased
FROM (
  SELECT 
  customer_id, 
  s.product_id, 
  product_name
  FROM sales s
  INNER JOIN menu m 
  ON s.product_id = m.product_id
  )  AS combined_table
GROUP BY product_name
````

![4](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/ae820215-f61b-4223-ba98-b9de5d542741)


We know that ramen is the most popular item, purchased 8 times by all customers. Out of curiousity, I found out how many times each customer purchased ramen by writing the following query:


````sql
SELECT 
  customer_ID, 
  COUNT(customer_ID) as number_of_times_purchased, 
  product_name
FROM (
 SELECT 
 customer_id, 
 s.product_id, 
 product_name
 FROM sales s
 INNER JOIN menu m 
 ON s.product_id = m.product_id
 WHERE product_name LIKE 'ramen') as subq
GROUP BY customer_id, product_name
ORDER BY customer_id
````

![4 1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/a156d837-8f73-4c4a-b2c0-02fb9e53bd78)

### 5. Which item was the most popular for each customer?

````sql
SELECT 
 customer_id,
 product_name,
 number_of_times_purchased
FROM
(SELECT 
 customer_id,
 product_name,
 COUNT(product_name) AS number_of_times_purchased,
 DENSE_RANK() OVER (PARTITION BY customer_id order by COUNT(product_name) desc)
FROM sales s
INNER JOIN menu m
ON s.product_id = m.product_id
GROUP BY  customer_id, product_name
) as ranked_table
where dense_rank = 1
````

![5](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/993a6e56-0233-4450-8d64-938dfecb7499)

### 6. Which item was purchased first by the customer after they became a member?

````sql
SELECT 
 customer_id,
 order_date,
 product_name 
FROM (
 SELECT 
  s.customer_id,
  order_date,
  product_name,
 ROW_NUMBER () OVER(PARTITION BY s.customer_id ORDER BY order_date)
 FROM sales s
 INNER JOIN menu m
  ON m.product_id = s.product_id
 INNER JOIN members mem
  ON mem.customer_id = s.customer_id
 WHERE order_date >= join_date) as subquery
WHERE ROW_NUMBER = 1
````

![6](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/105bb085-4c22-4270-99fa-ac7914a6f243)


### 7. Which item was purchased just before the customer became a member?

````sql
SELECT 
 customer_id,
 order_date,
 product_name
FROM (
   SELECT 
    s.customer_id,
    order_date,
    product_name,
    ROW_NUMBER () OVER(PARTITION BY s.customer_id ORDER BY order_date)
   FROM sales s
   INNER JOIN menu m
    ON m.product_id = s.product_id
   INNER JOIN members mem
    ON mem.customer_id = s.customer_id
   WHERE order_date < join_date) as subq
WHERE ROW_NUMBER = 1
````

![7](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/b452e6bd-0313-4b43-ad76-45f8d3c3d618)

### 8. What is the total items and amount spent for each member before they became a member?

````sql
SELECT 
  s.customer_id, 
  COUNT(s.customer_id), 
  CONCAT('$', SUM(PRICE)) as amount_spent
FROM sales s
INNER JOIN menu m
   ON s.product_id = m.product_id
INNER JOIN  members mem
  ON mem.customer_id = s.customer_id
WHERE order_date < join_date
GROUP BY s.customer_id
ORDER BY customer_id
````

![8](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/51340622-a829-4c7c-b813-c69e6ff58e84)

### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier — how many points would each customer have?

````sql
SELECT customer_id, sum(points) 
FROM
 (SELECT s.customer_id,
 CASE
  WHEN s.product_id = 1 THEN price * 20
  WHEN s.product_id in (2,3) THEN price*10
  END as points
 FROM sales s
 INNER JOIN menu m
 ON s.product_id = m.product_id) as subq
GROUP BY customer_id
ORDER BY customer_id
````

![9](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/7214341c-4587-4869-8a5e-f37396cc44ad)


### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi — how many points do customer A and B have at the end of January?

````sql
SELECT customer_id, sum(points)
FROM (
 SELECT s.customer_id,
 CASE
  WHEN s.product_id = 1 THEN price * 20
  WHEN s.product_id in (2,3) and order_date not between join_date and join_date + 6 THEN price*10
  WHEN s.product_id in (2,3) and order_date between join_date and join_date + 6  then price*20
  END as points
 FROM sales s
 INNER JOIN menu m
 ON s.product_id = m.product_id
 INNER JOIN members mem
 ON mem.customer_id = s.customer_id
 WHERE order_date between '2021-01-01' and '2021-01-31') as subq
GROUP BY customer_id
ORDER BY customer_id
````


![10](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/d851302c-1f97-468f-aa8c-82ee0f5cdb74)


## Bonus Questions:

### 1. Recreate the following table.

![11](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/70779574-7e9f-4e86-b744-08763e7d1b0f)

My query:

````sql
WITH cte AS (
 SELECT 
  s.customer_id, 
  order_date, 
  product_name,
  price,
   join_date
 FROM sales s
 INNER JOIN menu m 
 ON s.product_id = m.product_id
 LEFT JOIN members mem
 ON s.customer_id = mem.customer_id
)
 SELECT 
  customer_id, 
 order_date, 
 product_name,
 price,
CASE 
 WHEN customer_id LIKE 'C' THEN 'N'
 WHEN order_date < join_date THEN 'N'
 ELSE 'Y' 
 END as member
FROM cte
ORDER by customer_id, order_date, product_name
```` 

### 2. Recreate the following table.

![12](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/99a25275-203a-46e6-b33e-d8cbe3f61ec6)

My query:

 ````sql
WITH cte AS (
 SELECT 
  s.customer_id, 
  order_date, 
  product_name,
  price,
   join_date
 FROM sales s
 INNER JOIN menu m 
 ON s.product_id = m.product_id
 LEFT JOIN members mem
 ON s.customer_id = mem.customer_id
)
 SELECT 
 customer_id, 
 order_date, 
 product_name,
 price,
CASE 
 WHEN customer_id LIKE 'C' THEN 'N'
 WHEN order_date < join_date THEN 'N'
 ELSE 'Y' 
 END as member,
CASE 
 WHEN customer_id LIKE 'C' THEN null
 WHEN order_date < join_date THEN null
 ELSE DENSE_RANK () OVER (PARTITION BY customer_id, order_date >= join_date ORDER BY order_date)
 END as ranking
FROM cte
ORDER by customer_id, order_date, product_name
````

## Conclusion
This case study tested my knowledge of writing window functions, common table expressions, and subqueries.Looking forward to the next case study!
