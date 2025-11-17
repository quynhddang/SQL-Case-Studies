# Solutions - A. Pizza Metrics 

**1. How many pizzas were ordered?**

```sql
SELECT COUNT(order_id) AS pizza_ordered
FROM customer_orders_fix;
```

**Answer:**

|pizza_ordered|
|---|
|14|

- 14 pizzas were ordered.
   
**2. How many unique customer orders were made?**

```sql
SELECT COUNT (DISTINCT order_id) AS unique_orders
FROM customer_orders_fix;
```

**Answer:**

|unique_orders|
|---|
|10|

- There were 10 unique customer orders.
  
**3. How many successful orders were delivered by each runner?**

```sql
SELECT
   runner_id,
   COUNT(order_id) AS successful_orders
FROM runner_orders_fix
WHERE pickup_time <> ''
GROUP BY runner_id;
```

**Answer:**

| runner_id | successful_orders |
| --------- | ----------------- |
| 3         | 2                 |
| 2         | 4                 |
| 1         | 4                 |

- Runner 3 has 2 successful deliveries.
- Runner 2 and Runner 1 had 4 successful deliveries each.

**4. How many of each type of pizza was delivered?**

**5. How many Vegetarian and Meatlovers were ordered by each customer?**

**6. What was the maximum number of pizzas delivered in a single order?**

**7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**

**8. How many pizzas were delivered that had both exclusions and extras?**

**9. What was the total volume of pizzas ordered for each hour of the day?**

**10. What was the volume of orders for each day of the week?
