# Case Study #2, Pizza Runner

<p align="center">
<img src="https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/16ac8aa4-ba14-4d20-8e3a-399d45473694">
</p>

## Table of Contents
- [Problem Statement](#problem-statement)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Creating Tables](#creating-tables)
- [Cleaning Tables](#cleaning-tables)
- [A. Pizza Metrics](#a-pizzametrics)
- [B. Runner and Customer Experience](#b-runner-and-customer-experience)
- [C. Ingredient Optimisation](#c-ingredient-optimisation)
- [D. Pricing and Ratings](#d-pricing-and-ratings)
- [Conclusion](#conclusion)

## Problem Statement
Danny is starting a pizza from his house and has hired runners. He has already created tables containing information about the menu and deliveries. He needs your help to clean and analyze the data to gain insights about his business.

## Entity Relationship Diagram
<p align="center">
<<img src="https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/07211c4f-f533-4304-874e-1123d8455757">
</p>

## Creating Tables
To begin the case study, I created all tables using pgAdmin 4.


````sql
CREATE SCHEMA pizza_runner;
SET search_path = pizza_runner;

DROP TABLE IF EXISTS runners;
CREATE TABLE runners (
  "runner_id" INTEGER,
  "registration_date" DATE
);
INSERT INTO runners
  ("runner_id", "registration_date")
VALUES
  (1, '2021-01-01'),
  (2, '2021-01-03'),
  (3, '2021-01-08'),
  (4, '2021-01-15');

DROP TABLE IF EXISTS customer_orders;
CREATE TABLE customer_orders (
  "order_id" INTEGER,
  "customer_id" INTEGER,
  "pizza_id" INTEGER,
  "exclusions" VARCHAR(4),
  "extras" VARCHAR(4),
  "order_time" TIMESTAMP
);

INSERT INTO customer_orders
  ("order_id", "customer_id", "pizza_id", "exclusions", "extras", "order_time")
VALUES
  ('1', '101', '1', '', '', '2020-01-01 18:05:02'),
  ('2', '101', '1', '', '', '2020-01-01 19:00:52'),
  ('3', '102', '1', '', '', '2020-01-02 23:51:23'),
  ('3', '102', '2', '', NULL, '2020-01-02 23:51:23'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '2', '4', '', '2020-01-04 13:23:46'),
  ('5', '104', '1', 'null', '1', '2020-01-08 21:00:29'),
  ('6', '101', '2', 'null', 'null', '2020-01-08 21:03:13'),
  ('7', '105', '2', 'null', '1', '2020-01-08 21:20:29'),
  ('8', '102', '1', 'null', 'null', '2020-01-09 23:54:33'),
  ('9', '103', '1', '4', '1, 5', '2020-01-10 11:22:59'),
  ('10', '104', '1', 'null', 'null', '2020-01-11 18:34:49'),
  ('10', '104', '1', '2, 6', '1, 4', '2020-01-11 18:34:49');

DROP TABLE IF EXISTS runner_orders;
CREATE TABLE runner_orders (
  "order_id" INTEGER,
  "runner_id" INTEGER,
  "pickup_time" VARCHAR(19),
  "distance" VARCHAR(7),
  "duration" VARCHAR(10),
  "cancellation" VARCHAR(23)
);

INSERT INTO runner_orders
  ("order_id", "runner_id", "pickup_time", "distance", "duration", "cancellation")
VALUES
  ('1', '1', '2020-01-01 18:15:34', '20km', '32 minutes', ''),
  ('2', '1', '2020-01-01 19:10:54', '20km', '27 minutes', ''),
  ('3', '1', '2020-01-03 00:12:37', '13.4km', '20 mins', NULL),
  ('4', '2', '2020-01-04 13:53:03', '23.4', '40', NULL),
  ('5', '3', '2020-01-08 21:10:57', '10', '15', NULL),
  ('6', '3', 'null', 'null', 'null', 'Restaurant Cancellation'),
  ('7', '2', '2020-01-08 21:30:45', '25km', '25mins', 'null'),
  ('8', '2', '2020-01-10 00:15:02', '23.4 km', '15 minute', 'null'),
  ('9', '2', 'null', 'null', 'null', 'Customer Cancellation'),
  ('10', '1', '2020-01-11 18:50:20', '10km', '10minutes', 'null');

DROP TABLE IF EXISTS pizza_names;
CREATE TABLE pizza_names (
  "pizza_id" INTEGER,
  "pizza_name" TEXT
);
INSERT INTO pizza_names
  ("pizza_id", "pizza_name")
VALUES
  (1, 'Meatlovers'),
  (2, 'Vegetarian');

DROP TABLE IF EXISTS pizza_recipes;
CREATE TABLE pizza_recipes (
  "pizza_id" INTEGER,
  "toppings" TEXT
);
INSERT INTO pizza_recipes
  ("pizza_id", "toppings")
VALUES
  (1, '1, 2, 3, 4, 5, 6, 8, 10'),
  (2, '4, 6, 7, 9, 11, 12');

DROP TABLE IF EXISTS pizza_toppings;
CREATE TABLE pizza_toppings (
  "topping_id" INTEGER,
  "topping_name" TEXT
);
INSERT INTO pizza_toppings
  ("topping_id", "topping_name")
VALUES
  (1, 'Bacon'),
  (2, 'BBQ Sauce'),
  (3, 'Beef'),
  (4, 'Cheese'),
  (5, 'Chicken'),
  (6, 'Mushrooms'),
  (7, 'Onions'),
  (8, 'Pepperoni'),
  (9, 'Peppers'),
  (10, 'Salami'),
  (11, 'Tomatoes'),
  (12, 'Tomato Sauce');
````



## Cleaning Tables
Next, I cleaned the tables.

First I changed the 'null' and '' values from the customer_orders table to NULL.



````sql
UPDATE customer_orders 
SET exclusions = NULL
WHERE exclusions LIKE 'null';

UPDATE customer_orders 
SET exclusions = NULL
WHERE exclusions LIKE '';

UPDATE customer_orders 
SET extras = NULL
WHERE extras LIKE '';
````


I repeated this process again for the runner_orders table.


````sql
UPDATE runner_orders
SET pickup_time = NULL
WHERE pickup_time LIKE 'null';

UPDATE runner_orders
SET distance = NULL
WHERE distance LIKE 'null';

UPDATE runner_orders
SET duration = NULL
WHERE duration LIKE 'null';

UPDATE runner_orders
SET cancellation = NULL
WHERE cancellation LIKE 'null';

UPDATE runner_orders
SET cancellation = NULL
WHERE cancellation LIKE '';
````


I then removed  extraneous information from the distance and duration columns of the runners_orders table. 


````sql
UPDATE runner_orders
SET distance = REPLACE(REPLACE(distance, 'km', ''), ' km', '')  

UPDATE runner_orders
SET duration = REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(duration, ' mins', ''), 'mins', ''), ' minute', ''), 'minutes', ''), ' minutes', ''), 's', '')
````


Finally, I converted the columns from the runner_orders table to the appropriate data type.


````sql
ALTER TABLE runner_orders
ALTER COLUMN pickup_time TYPE TIMESTAMP
USING pickup_time::timestamp without time zone;

ALTER TABLE runner_orders
ALTER COLUMN distance TYPE FLOAT
USING distance::FLOAT;

ALTER TABLE runner_orders
ALTER COLUMN duration TYPE INTEGER
USING duration::integer
````


With the data cleaned, I could finally answer the case study questions. 


## A. Pizza Metrics


### 1. How many pizzas were ordered?


````sql
SELECT COUNT(order_id) as total_ordered
FROM customer_orders
````


  <img src="https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/5fe6f464-df41-48f7-835c-0f05f40c8158">


### 2. How many unique customer orders were made?


````sql
SELECT  COUNT(DISTINCT order_id) as unique_orders
FROM customer_orders
````


![2](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/ad37d0c5-738d-4ef9-b0ca-3ac352579a36)


### 3. How many successful orders were delivered by each runner?


````sql
SELECT COUNT(order_id) - COUNT(cancellation) as successful_orders
FROM runner_orders
````


![3](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/f9bf6e7d-3768-42ab-934c-0fe52d6ad699)


### 4. How many of each type of pizza was delivered?


````sql
SELECT c.pizza_id, 
 p.pizza_name,
 COUNT (c.pizza_id)
FROM customer_orders c
INNER JOIN pizza_names p
on c.pizza_id = p.pizza_id
WHERE order_id not in (6, 9)
GROUP BY c.pizza_id, p.pizza_name
ORDER BY c.pizza_id
````


![4](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/f1498365-ec52-4904-ad46-942988cbe457)


### 5.  How many Vegetarian and Meatlovers were ordered by each customer?


````sql
WITH CTE AS (
 SELECT customer_id, p.pizza_id, p.pizza_name 
 FROM customer_orders c
 INNER JOIN pizza_names p
 ON c.pizza_id = p.pizza_id)
SELECT customer_id, pizza_name, COUNT (customer_id)
FROM CTE
GROUP BY customer_id, pizza_name
ORDER by customer_id
````


![5](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/830efebb-685a-4768-9198-f7cfcecbd177)



### 6.  What was the maximum number of pizzas delivered in a single order?


````sql
SELECT order_id, COUNT(pizza_id) as max_ordered
 FROM customer_orders
 GROUP by order_id
 ORDER by max_ordered des
 LIMIT 1
````


![6](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/7f12124d-08db-4064-be40-e17209097698)



### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?


````sql
SELECT order_id, COUNT(order_id) as orders_with_at_least_1_change
FROM customer_orders
WHERE exclusions IS NOT NULL or EXTRAS IS NOT NULL
AND order_id NOT IN (6, 9)
GROUP BY order_id
ORDER BY order_id
````


![7](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/5371703a-3737-4a1c-b3dc-58b0f4615b87)



````sql
SELECT order_id, COUNT(order_id) AS orders_with_no_changes
FROM customer_orders
WHERE  exclusions IS NULL AND extras IS NULL
AND order_id NOT IN (6, 9)
GROUP BY order_id
ORDER BY order_id;
````


![7 1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/33dd6640-9772-41ef-ad5f-ea3405c07b90)



### 8. How many pizzas were delivered that had both exclusions and extras?

````sql
SELECT order_id, COUNT(order_id) as orders_with_exclu_and_extras
FROM customer_orders
WHERE exclusions IS NOT NULL AND EXTRAS IS NOT NULL
AND order_id NOT IN (6, 9)
GROUP BY order_id
ORDER BY order_id
````


![8](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/e5a6ab12-0b31-44a2-a386-5466c8bb22f9)


### 9. What was the total volume of pizzas ordered for each hour of the day?


````sql
SELECT  EXTRACT(HOUR FROM order_time) AS hour, COUNT(pizza_ID) as number_of_pizzas
FROM customer_orders
GROUP BY hour
ORDER BY hour
````


![9](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/8d62c1fe-686d-4d95-a377-37d9223d2fc9)



### 10. What was the volume of orders for each day of the week?


````sql
SELECT  TO_CHAR(order_time, 'Day') AS day, COUNT(pizza_ID) as number_of_pizzas
FROM customer_orders
GROUP BY day
ORDER by  number_of_pizzas
````


![10](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/d71e650e-12be-4fec-93c7-01408e4426c5)


## B. Runner and Customer Experience

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

 ````sql
SELECT TO_CHAR(registration_date, 'W') AS week,
 COUNT(runner_id)
FROM runners
GROUP BY week
ORDER BY week
 ````

![B1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/e225d52c-9bd5-4e51-8c30-958e7c6093d4)

### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
 
 ````sql
WITH CTE AS(
 SELECT 
  runner_id,
  c.order_id,
     (EXTRACT(EPOCH FROM (pickup_time - order_time)/60)) AS time_diff
 FROM runner_orders r
 INNER JOIN customer_orders c
 ON r.order_id = c.order_id
 WHERE c.order_id NOT IN (6,9))
SELECT runner_id, ROUND(AVG(time_diff), 2) AS avg_time_in_minutes
FROM CTE
GROUP BY runner_id
ORDER BY runner_id
````

![B2](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/3479848c-221d-4344-af80-51c1c4aaac39)

### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

 ````sql
WITH CTE AS(
 SELECT 
     c.order_id,
     pizza_id,
     EXTRACT(EPOCH FROM(pickup_time - order_time))/60 AS time_diff
 FROM runner_orders r
 INNER JOIN customer_orders c
 ON r.order_id = c.order_id
 WHERE c.order_id NOT IN (6,9))
SELECT order_id, COUNT(pizza_id) as num_of_pizzas, ROUND(AVG(time_diff), 2) AS avg_time_in_minutes
FROM CTE
GROUP BY order_id
ORDER BY num_of_pizzas
````

![B3](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/57b2719e-0175-445e-a92e-5b53f01e9ff4)

With the exception of order 8, the higher the number of pizzas in the order, the longer it takes to prepare.

 ````sql
WITH CTE2 AS(
WITH CTE AS(
 SELECT 
     c.order_id,
     pizza_id,
     EXTRACT(EPOCH FROM(pickup_time - order_time))/60 AS time_diff
 FROM runner_orders r
 INNER JOIN customer_orders c
 ON r.order_id = c.order_id
 WHERE c.order_id NOT IN (6,9))
SELECT order_id, COUNT(pizza_id) as num_of_pizzas, AVG(time_diff) AS time_diff
FROM CTE
GROUP BY order_id
ORDER BY num_of_pizzas)
SELECT num_of_pizzas, ROUND(avg(time_diff),2) AS avg_time_in_min
FROM CTE2
GROUP BY num_of_pizzas
````
By using the query above, we find out how long it takes to prepare an order based on the number of pizzas.

![B3 1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/d5f86888-6fb1-4840-836b-c18b65748202)


### 4. What was the average distance travelled for each customer?

 ````sql
SELECT 
  customer_id,
 ROUND(AVG(distance)::numeric, 2) as avg_distance_travelled
 FROM runner_orders r
 INNER JOIN customer_orders c
 ON r.order_id = c.order_id
 WHERE c.order_id NOT IN (6,9)
GROUP BY customer_id
ORDER BY customer_id
 ````
![B4](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/a3e2edf5-4e94-4743-be59-f97c31dfae12)


### 5. What was the difference between the longest and shortest delivery times for all orders?

````sql
SELECT MAX(duration) - MIN (duration) as difference
FROM runner_orders
 ````
![B5](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/1c4cf2eb-795b-42fb-826a-aad83689c4cb)


### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

````sql
SELECT 
  runner_id,  
  order_id, 
  ROUND((distance / (duration / 60.0))::numeric, 2) as km_per_hour
FROM runner_orders
WHERE order_id NOT IN (6, 9)
ORDER BY runner_id, order_id
````
![B6](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/3bbfa02f-a421-49c9-8890-6a59669f234e)


From the results of the query, we find out that
- runner 1 has an average speed between 37.5 and 60 km per hour,
- runner 2 has an average speed between 35.1 and 93.6 km per hour,
- and runner 3 has an average speed of 40 km per hour.


### 7. What is the successful delivery percentage for each runner?

````sql
SELECT runner_id,
 CAST(COUNT(duration) as FLOAT)/CAST(COUNT(order_id) as FLOAT)*100 AS percent_sucess
FROM runner_orders
GROUP BY runner_id
ORDER BY runner_id
````

![B7](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/b2ddb242-3e85-42fe-8999-3f9c65cea4e7)


## C. Ingredient Optimisation


### 1. What are the standard ingredients for each pizza?

To answer this question, I created a new table pizza_recipes_2 that reformatted the original data from pizza_recipes. The original table listed all the pizza toppings for each pizza as a singular text block. The new table however splits up each topping and categorizes them as an integer.

````sql
CREATE TABLE pizza_recipes_2
(pizza_id int, toppings int);
INSERT INTO pizza_recipes_2
(pizza_id, toppings)
VALUES
(1,1),
(1,2),
(1,3),
(1,4),
(1,5),
(1,6),
(1,8),
(1,10),
(2,4),
(2,6),
(2,7),
(2,9),
(2,11),
(2,12);
````

![C1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/a18540d5-2497-4455-8411-9142d114cb0d)


I then found the ingredients for each pizza!

````sql
SELECT pizza_name, string_agg(topping_name, ', ') as toppings
FROM pizza_names n
INNER JOIN pizza_recipes_2 r
ON n.pizza_id = r.pizza_id
INNER JOIN pizza_toppings t
ON t.topping_id = r.toppings
GROUP BY pizza_name
````

![C1 2](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/384955cf-6a19-4a9a-ac93-6fb0b91258e4)


### 2. What was the most commonly added extra?

To answer this question, I first inserted four columns that listed each exclusion and extra separately as an integer.

````sql
ALTER TABLE customer_orders
ADD COLUMN exclusions_1 INT NULL,
ADD COLUMN  exclusions_2 INT NULL, 
ADD COLUMN extras_1 INT NULL,
ADD COLUMN  extras_2 INT NULL;

UPDATE customer_orders
SET  exclusions_1 = 4, extras_1 = 1, extras_2 = 5
WHERE order_id = 9;

UPDATE customer_orders
SET  exclusions_1 = 2, exclusions_2  = 6, extras_1  = 1, extras_2  = 4
WHERE order_id = 10 AND exclusions LIKE '2, 6';

UPDATE customer_orders
SET  extras_1 = 1
WHERE order_id = 5;

UPDATE customer_orders
SET  extras_1 = 1
WHERE customer_id = 105;

UPDATE customer_orders
SET   exclusions_1 = 4
WHERE order_id = 4;
````

![c2](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/bcc2a392-a0f3-4830-b39e-2c3c7f08e948)


Then I wrote a query!

````sql
WITH CTE AS(
SELECT  extras_1 as topping_id,  COUNT(extras_1) as times_ordered
FROM customer_orders
WHERE extras_1 IS NOT NULL
GROUP BY  extras_1

UNION

SELECT extras_2, COUNT(extras_2)
FROM customer_orders
WHERE extras_2 IS NOT NULL
GROUP BY  extras_2 )

SELECT t.topping_id, topping_name, times_ordered
FROM CTE 
INNER JOIN pizza_toppings t
ON t.topping_id = CTE.topping_id
````

![c2 1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/232f48c7-9d7f-47f9-8bb6-1dd2c5896e87)


### 3. What was the most common exclusion?

````sql
WITH CTE AS(
SELECT  exclusions_1 as topping_id,  COUNT(exclusions_1) as times_ordered
FROM customer_orders
WHERE exclusions_1 IS NOT NULL
GROUP BY  exclusions_1

UNION

SELECT exclusions_2, COUNT(exclusions_2)
FROM customer_orders
WHERE exclusions_2 IS NOT NULL
GROUP BY  exclusions_2 )

SELECT t.topping_id, topping_name, times_ordered
FROM CTE 
INNER JOIN pizza_toppings t
ON t.topping_id = CTE.topping_id
````

![c3](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/4e70b72a-1972-4aff-a606-b6a715417475)


### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

To do this I first created a table that listed the names of all the exclusions and extras.

````sql
CREATE TABLE customer_orders_2 AS  (
SELECT c.order_id, customer_id, c.pizza_id, exclusions, extras, order_time, exclusions_1, exclusions_2, extras_1, extras_2, pizza_name, 
 t.topping_name as exclusions_1_topping,
 t2.topping_name as exclusions_2_topping,
 t3.topping_name as extras_1_topping,
 t4.topping_name as extras_2_topping
FROM customer_orders c
INNER JOIN pizza_names n
ON c.pizza_id = n.pizza_id
LEFT JOIN pizza_toppings t
ON   c.exclusions_1 = t.topping_id 
LEFT JOIN pizza_toppings t2
ON   c.exclusions_2 = t2.topping_id  
LEFT JOIN pizza_toppings t3
ON   c.extras_1 = t3.topping_id  
LEFT JOIN pizza_toppings t4
ON   c.extras_2 = t4.topping_id)
````

![c4](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/c096b441-5abb-470b-8b50-8024ab92f8e6)


Then I wrote this query!

````sql
SELECT order_id,
CASE
 WHEN COALESCE (exclusions_1_topping, exclusions_2_topping, extras_1_topping, extras_2_topping) IS NULL THEN pizza_name
 WHEN COALESCE (exclusions_2_topping, extras_1_topping, extras_2_topping) IS NULL THEN CONCAT(pizza_name, ' - Exclude ', exclusions_1_topping) 
    WHEN COALESCE (exclusions_1_topping, exclusions_2_topping, extras_2_topping) IS NULL THEN CONCAT(pizza_name, ' - Extra ', extras_1_topping)
 WHEN COALESCE (exclusions_1_topping, exclusions_2_topping, extras_1_topping, extras_2_topping) IS NOT NULL AND COALESCE (exclusions_2_topping) IS NULL THEN CONCAT(pizza_name, ' - Exclude ', exclusions_1_topping, ' - Extra ', extras_1_topping, ', ', extras_2_topping)
 WHEN COALESCE (exclusions_1_topping, exclusions_2_topping, extras_1_topping, extras_2_topping) IS NOT NULL THEN CONCAT(pizza_name, ' - Exclude ', exclusions_1_topping, ', ', exclusions_2_topping, ' - Extra ', extras_1_topping, ', ', extras_2_topping)
 END as order
FROM customer_orders_2
ORDER BY order_id
````

![c4 1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/90ab947c-87b8-4eae-b8f9-6d0913ecb945)


### 5. Generate an alphabetically ordered comma-separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients

For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"

I first created a table that aggregated the topping names and listed the exclusions and extras.

````sql
CREATE TABLE customer_orders_3 AS(
 
WITH CTE AS(
SELECT pizza_name, string_agg(topping_name, ', ') as toppings
FROM pizza_names n
INNER JOIN pizza_recipes_2 r
ON n.pizza_id = r.pizza_id
INNER JOIN pizza_toppings t
ON t.topping_id = r.toppings
GROUP BY pizza_name)
 
SELECT order_id, c.pizza_name, toppings, exclusions_1_topping,  exclusions_2_topping, extras_1_topping, extras_2_topping
FROM CTE c
INNER JOIN customer_orders_2 co
ON c.pizza_name = co.pizza_name
ORDER BY order_id)
````

![c5](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/7b69bad2-ac34-47f3-b6b9-9717f5b9f37a)

Then I wrote the query that placed '2x' in front of extra ingredients and deleted exclusions.


````sql
WITH CTE AS (
 SELECT order_id, CONCAT(pizza_name, ' : ' ,
  CASE 
   WHEN POSITION(exclusions_1_topping in toppings) > 0 AND POSITION(exclusions_2_topping in toppings) IS NULL 
  AND POSITION(extras_1_topping in toppings) IS NULL AND POSITION(extras_2_topping in toppings) IS NULL 
  THEN REPLACE(toppings, exclusions_1_topping, ' ') -- order 4
   WHEN POSITION(exclusions_1_topping in toppings) IS NULL AND POSITION(exclusions_2_topping in toppings) IS NULL 
  AND POSITION(extras_1_topping in toppings) > 0 AND POSITION(extras_2_topping in toppings) IS NULL 
  THEN REPLACE(toppings, extras_1_topping, CONCAT('2X', extras_1_topping)) -- order 5
  WHEN POSITION(exclusions_1_topping in toppings) IS NULL AND POSITION(exclusions_2_topping in toppings) IS NULL 
  AND POSITION(extras_1_topping in toppings) = 0 AND POSITION(extras_2_topping in toppings) IS NULL 
  THEN CONCAT (extras_1_topping, ', ', toppings) --order 7
    WHEN POSITION(exclusions_1_topping in toppings) > 0 AND POSITION(exclusions_2_topping in toppings) IS NULL 
  AND POSITION(extras_1_topping in toppings) > 0 AND POSITION(extras_2_topping in toppings) > 0 
  THEN REPLACE(REPLACE(REPLACE(toppings, exclusions_1_topping, ''), extras_1_topping, CONCAT('2x', extras_1_topping)), extras_2_topping, CONCAT('2x', extras_2_topping)) -- order 9
   WHEN POSITION(exclusions_1_topping in toppings) > 0 AND POSITION(exclusions_2_topping in toppings) > 0
  AND position(extras_1_topping in toppings) > 0 AND position(extras_2_topping in toppings) > 0 
    THEN REPLACE(REPLACE(REPLACE(REPLACE(toppings, exclusions_1_topping, ''), exclusions_2_topping, ''), extras_1_topping, CONCAT('2x', extras_1_topping)), extras_2_topping, CONCAT('2x', extras_2_topping)) -- order 10 (first one)
   ELSE toppings
   END)
 FROM customer_orders_3)
 
SELECT order_id, REPLACE (concat, ' ,', '') AS topping
FROM cte
````

![c5 1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/3635226b-22ba-4f2a-b047-ddcdb99d21f1)


### 6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

It took many queries to answer this question. First I created three tables that listed the ingredient count of regular pizzas, exclusions, and extras. I excluded orders 6 and 9 from all queries since those orders were canceled and never delivered.

````sql
--Regular pizza ingredient count

CREATE TEMP TABLE toppings_final AS (

WITH CTE AS (
SELECT order_id, c.pizza_id, toppings
FROM customer_orders_2 c
INNER JOIN pizza_recipes_2 pr
ON c.pizza_id = pr.pizza_id
WHERE order_id NOT IN (6, 9)
ORDER BY order_id, pizza_id, toppings)
 
SELECT toppings, count (toppings) as count_toppings  
FROM CTE
GROUP BY toppings
ORDER BY toppings)
````
![c regular_pizza_toppings](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/9f331532-019f-4c89-8b91-133b8dc78b94)

````sql
--Exclusions ingredient count

CREATE TEMP TABLE exclusions_final AS (
 
WITH exclusions as (
SELECT exclusions_1 as exclusions
FROM customer_orders_2
WHERE exclusions_1 is not null
AND order_id NOT IN (6, 9)

UNION ALL

SELECT exclusions_2
FROM customer_orders_2
WHERE exclusions_2 is not null
AND order_id NOT IN (6, 9))

SELECT exclusions as excluded_toppings, count (exclusions) as count_exclusions
FROM exclusions
GROUP BY exclusions
ORDER BY exclusions)
````

![c exclusions](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/d3ebd9f8-f479-4320-a72e-df245fbe8e34)

````sql
--Extras ingredient count

CREATE TEMP TABLE extras_final AS (

WITH extras as (
SELECT extras_1 as extras
FROM customer_orders_2
WHERE extras_1 is not null
AND order_id NOT IN (6, 9)

UNION ALL

SELECT extras_2
FROM customer_orders_2
WHERE extras_2 is not null
AND order_id NOT IN (6, 9))

SELECT extras as extra_toppings, count (extras) as count_extras
FROM extras
GROUP BY extras
ORDER BY extras)
````
![c extras](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/e78283a6-cc89-427a-b0aa-bf13054d6e1b)


Then I created another table where I found the total count of all toppings by joining the three previously created tables and adding and subtracting the appropriate columns.

````sql
CREATE TEMP TABLE final_table AS (
SELECT toppings, count_toppings - COALESCE(count_exclusions, 0)  + COALESCE(count_extras, 0) as count_toppings
FROM toppings_list_final t
LEFT JOIN exclusions_final exc
ON t.toppings = exc.excluded_toppings 
LEFT JOIN extras_final ext
ON t.toppings = ext.extra_toppings)
````

![c combined_tables](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/0dc02ca6-d1df-446d-b191-07230ac5a053)


Finally, I joined the pizza_toppings table to get the topping names and sorted the count_toppings column to place the most frequently used ingredients on top.

````sql
SELECT topping_name, count_toppings
FROM pizza_toppings p
INNER JOIN final_table f
ON f.toppings = p.topping_id
ORDER BY count_toppings desc
````

![c final_table](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/56071db9-edf6-429c-a9b2-ba7034209c24)

## D. Pricing and Ratings

### 1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?

````sql
SELECT 
 SUM (CASE WHEN pizza_id = 1 THEN 12
 ELSE 10
 END)
FROM customer_orders
WHERE ORDER_ID NOT IN (6, 9)
````

![d1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/a1e0c52c-5d0b-4161-909c-c542ff4dfb33)


### 2. What if there was an additional $1 charge for any pizza extras?

````sql
SELECT COUNT(extras_1) + COUNT(extras_2) +
 SUM (CASE WHEN pizza_id = 1 THEN 12
 ELSE 10
 END) as total
FROM customer_orders_2
WHERE ORDER_ID NOT IN (6, 9)
````

![d2](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/4c8b7161-1b7a-4712-980f-71e05e9ca89f)


###  3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.

I'm designing a rating system based on the speed (km per minute) of the runners. Slower runners receive lower scores while faster ones receive higher.

````sql
CREATE TABLE ratings ("rating" INT, "km_per_min" string);
INSERT INTO ratings
 ("rating", "km_per_min")
VALUES 
  (1, 0 - .5),
  (2, .51 - .75,
  (3, .76 - 1),
  (4, 1.1 - 1.25),
  (5, 1.26+)
````

![d3_ratings](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/9a3b4d62-ffab-403c-9f0a-ea19d3c66f3f)


Then I wrote a query that rated all the runners based on the table above.

````sql
WITH CTE as (
SELECT order_id, runner_id, distance, duration, ROUND(distance::numeric/duration::numeric, 2) as km_per_min
FROM runner_orders
order by km_per_min)
SELECT *, 
  CASE
  WHEN km_per_min BETWEEN 0 and .5 THEN 1
  WHEN km_per_min BETWEEN .51 and .75 THEN 2
  WHEN km_per_min BETWEEN .76 and 1 THEN 3
  WHEN km_per_min BETWEEN 1.1 and 1.25 THEN 4
  WHEN km_per_min > 1.26 THEN 5
  WHEN km_per_min is NULL THEN NULL
  END as rating
FROM CTE
````

![d3_runners_rated](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/7f67e94e-68c6-4192-97a3-e37ed4c2230a)


### 4. Using your newly generated table - can you join all of the information together to form a table that has the following information for successful deliveries?
   
- customer_id
- order_id
- runner_id
- rating
- order_time
- pickup_time
- Time between order and pickup
- Delivery duration
- Average speed
- Total number of pizzas

First, I created a temporary table with similar information to the table in the last question.

````sql
CREATE TEMP TABLE runner_orders_final AS (
WITH CTE as (
SELECT order_id, runner_id, pickup_time, distance, duration, ROUND(distance::numeric/duration::numeric, 2) as km_per_min
FROM runner_orders
order by km_per_min)
SELECT *, 
  CASE
  WHEN km_per_min BETWEEN 0 and .5 THEN 1
  WHEN km_per_min BETWEEN .51 and .75 THEN 2
  WHEN km_per_min BETWEEN .76 and 1 THEN 3
  WHEN km_per_min BETWEEN 1.1 and 1.25 THEN 4
  WHEN km_per_min > 1.26 THEN 5
  WHEN km_per_min is NULL THEN NULL
  END as rating
FROM CTE)
````

Then I joined the customer_orders table and extract relevant information.

````sql
SELECT c.order_id, runner_id, rating, order_time, pickup_time, pickup_time-order_time as time_diff, duration, km_per_min, COUNT(pizza_id) as total_pizzas
FROM customer_orders c
INNER JOIN runner_orders_final r
ON c.order_id = r.order_id
WHERE c.order_id NOT IN (6, 9)
GROUP BY c.order_id, customer_id, runner_id, rating, order_time, pickup_time, time_diff, duration, km_per_min
ORDER BY order_id, customer_id
````

![d4](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/103cfd6d-d988-45b4-bf7a-1d540144562f)

###  5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?


````sql
SELECT 
 (SELECT SUM(CASE WHEN pizza_id = 1 THEN 12 ELSE 10 END)  FROM customer_orders WHERE order_id NOT IN (6,9)) as pizza_revenue,
 (SELECT(ROUND(SUM(CAST(distance AS NUMERIC) * .3), 2)) FROM runner_orders) as runner_fees,
 (SELECT SUM(CASE WHEN pizza_id = 1 THEN 12 ELSE 10 END)  FROM customer_orders WHERE order_id NOT IN (6,9)) -
 (SELECT (SUM(distance)*.3) FROM runner_orders) AS Profit
FROM customer_orders c
INNER JOIN runner_orders r
ON r.order_id = c.order_id
LIMIT 1
````

![d5](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/1635085e-5fc3-43de-aca4-7b5bb657b8b0)


## E. Bonus Questions

If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?
I first wrote a query to alter the 'pizza _names' table.

````sql
INSERT INTO pizza_names(pizza_id, pizza_name)
VALUES (3, 'Supreme')
````
![E1](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/4e0a1ecd-12dc-47a0-babf-57961f6939f9)

Then I wrote more queries to alter the 'pizza_recipes' and 'pizza_recipes_2' tables

````sql
INSERT INTO pizza_recipes (pizza_id, toppings)
VALUES (3, '1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12')
````

![E2](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/074688c3-60d4-4de2-8794-170e0c854e5f)

````sql
INSERT INTO pizza_recipes_2 (pizza_id, toppings)
VALUES 
(3, 1),
(3, 2),
(3, 3),
(3, 4),
(3, 5),
(3, 6),
(3, 7),
(3, 8),
(3, 9),
(3, 10),
(3, 11),
(3, 12);
````
![E3](https://github.com/kenneth-dizon/8_Week_SQL_Challenge/assets/141383645/9f1de47e-9183-4ffa-8eb7-c57a491b4cc6)

## Conclusion
This case study was tough! It developed my skills in using aggregate functions, subqueries, temporary tables, joins, and CASE statements. See you at the next one!

