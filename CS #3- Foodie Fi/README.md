# 🥑 CS# 3: Foodie-Fi

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/4ce0b466-f437-448f-98e1-3aed99eb2978" />

## 📂 Table of Contents

- [Business Task](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#business-task)
- [Entity Relationship Diagram](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#entity-relationship-diagram)
- [Questions and Solutions](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#questions-and-solutions)

All information regarding the case study has been sourced [here](https://8weeksqlchallenge.com/case-study-3/).
  
## Business Task

Danny launched his new startup Foodie-Fi, a streaming service that only has food related content, and started selling monthly and annual subscriptions, giving customers on-demand access to exclusive food videos from around the world.

This case study focuses on using subscription style digital data to answer important business questions regarding customer journey, payments, and business performance.

## Entity Relationship Diagram

<img width="698" height="290" alt="image" src="https://github.com/user-attachments/assets/bb83f8d0-ed9d-4c76-85e0-27165706d2df" />

## Questions and Solutions

- [A. Customer Journey](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#a-customer-journey)
- [B. Data Analysis Questions](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#b-data-analysis-questions)

### A. Customer Journey

Based off the 8 sample customers provided in the sample from the subscription table, write a brief description about each customer's onboarding journey.

**Answer:**

```sql
SELECT
  subscriptions.customer_id,
  plans.plan_id, 
  plans.plan_name,  
  subscriptions.start_date
FROM foodie_fi.subscriptions 
INNER JOIN foodie_fi.plans
  ON subscriptions.plan_id = plans.plan_id
WHERE subscriptions.customer_id IN (1,2,11,13,15,16,18,19)
ORDER BY subscriptions.customer_id, plans.plan_id;
```

| customer_id | plan_id | plan_name     | start_date |
| ----------- | ------- | ------------- | ---------- |
| 1           | 0       | trial         | 2020-08-01 |
| 1           | 1       | basic monthly | 2020-08-08 |
| 2           | 0       | trial         | 2020-09-20 |
| 2           | 3       | pro annual    | 2020-09-27 |
| 11          | 0       | trial         | 2020-11-19 |
| 11          | 4       | churn         | 2020-11-26 |
| 13          | 0       | trial         | 2020-12-15 |
| 13          | 1       | basic monthly | 2020-12-22 |
| 13          | 2       | pro monthly   | 2021-03-29 |
| 15          | 0       | trial         | 2020-03-17 |
| 15          | 2       | pro monthly   | 2020-03-24 |
| 15          | 4       | churn         | 2020-04-29 |
| 16          | 0       | trial         | 2020-05-31 |
| 16          | 1       | basic monthly | 2020-06-07 |
| 16          | 3       | pro annual    | 2020-10-21 |
| 18          | 0       | trial         | 2020-07-06 |
| 18          | 2       | pro monthly   | 2020-07-13 |
| 19          | 0       | trial         | 2020-06-22 |
| 19          | 2       | pro monthly   | 2020-06-29 |
| 19          | 3       | pro annual    | 2020-08-29 |

Based on the results, I will focus on three customers and their onboarding journeys.

- Customer 15 started their free trial on 17 March 2020. After the trial ended, they upgraded to the pro monthly plan on 24 March 2020. However, after a month on 29 April 2020, they decided to cancel their membership and churn until the subscription ended.

| customer_id | plan_id | plan_name     | start_date |
| ----------- | ------- | ------------- | ---------- |
| 15          | 0       | trial         | 2020-03-17 |
| 15          | 2       | pro monthly   | 2020-03-24 |
| 15          | 4       | churn         | 2020-04-29 |

- Customer 18 started the free trial on 06 July 2020. After the trial ended, Customer 18 upgraded to the pro monthly subscription on 17 July 2020.

| customer_id | plan_id | plan_name     | start_date |
| ----------- | ------- | ------------- | ---------- |
| 18          | 0       | trial         | 2020-07-06 |
| 18          | 2       | pro monthly   | 2020-07-13 |

- Customer 19 started the free trial on 22 June 2020. After the trial ended, Customer 19 upgraded to the pro monthly subscription on 29 June 2020. After two months of having the pro monthly subscription, Customer 19 upgraded to the pro annual subscription. 

| customer_id | plan_id | plan_name     | start_date |
| ----------- | ------- | ------------- | ---------- |
| 19          | 0       | trial         | 2020-06-22 |
| 19          | 2       | pro monthly   | 2020-06-29 |
| 19          | 3       | pro annual    | 2020-08-29 |

### B. Data Analysis Questions

**1. How many customers has Foodie-Fi ever had?**

```sql
SELECT COUNT(DISTINCT customer_id) AS number_of_customers
FROM foodie_fi.subscriptions; 
```
**Answer:** 

|number_of_customers|
|-------------------|
| 1000              |

- Foodie-Fi has had 1000 unique customers.

**2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value**

```sql
SELECT
  DATE_PART('month', start_date) AS month_date, 
  COUNT(*) AS trial_plans
FROM foodie_fi.subscriptions AS s
JOIN foodie_fi.plans p
  ON s.plan_id = p.plan_id
WHERE s.plan_id = 0 
GROUP BY month_date
ORDER BY month_date;
```

**Answer:** 

| month_date | trial_plans |
| ---------- | ----------- |
| 1          | 88          |
| 2          | 68          |
| 3          | 94          |
| 4          | 81          |
| 5          | 88          |
| 6          | 79          |
| 7          | 89          |
| 8          | 88          |
| 9          | 87          |
| 10         | 79          |
| 11         | 75          |
| 12         | 84          |

- Based on the results, March had the highest number of trial plans, and February had the lowest.

**3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name**

```sql
SELECT
	p.plan_id,
    p.plan_name,
    COUNT(s.customer_id) AS number_of_events
FROM foodie_fi.subscriptions AS s
INNER JOIN foodie_fi.plans AS p
	ON s.plan_id = p.plan_id
WHERE s.start_date >= '2021-01-01'
GROUP BY p.plan_id, p.plan_name
ORDER BY p.plan_id;
```

**Answer:**

| plan_id | plan_name     | number_of_events |
| ------- | ------------- | ---------------- |
| 1       | basic monthly | 8                |
| 2       | pro monthly   | 60               |
| 3       | pro annual    | 63               |
| 4       | churn         | 71               |

**4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?**

```sql
SELECT
	COUNT(DISTINCT s.customer_id) AS churned_accounts,
    ROUND(100.0 * COUNT(s.customer_id)
    / (SELECT Count(DISTINCT customer_id)
       FROM foodie_fi.subscriptions)
    , 1) AS churn_percentage
FROM foodie_fi.subscriptions AS s
WHERE s.plan_id = 4;
```

**Answer:**

| churned_accounts | churn_percentage | 
| ---------------- | ---------------- | 
| 307              | 30.7             |                 

- 307 customers have churned their accounts. This makes up 30.7% of the overall customer count.
  
**5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?**

```sql
WITH ranks_cte AS (
  SELECT 
  	s.customer_id,
  	p.plan_id,
  	p.plan_name,
  	ROW_NUMBER() OVER(
    PARTITION BY s.customer_id
    ORDER BY s.start_date) AS row_num
  FROM foodie_fi.subscriptions AS s
  INNER JOIN foodie_fi.plans AS p
  	ON s.plan_id = p.plan_id
)

SELECT
	COUNT(CASE
          WHEN row_num = 2 AND plan_name = 'churn' THEN 1
          ELSE 0 END) AS churned_accounts,
    ROUND(100.0 * COUNT(CASE
                        WHEN row_num = 2 AND plan_name = 'churn' THEN 1
                        ELSE 0 END)
          / (SELECT COUNT(DISTINCT customer_id)
             FROM foodie_fi.subscriptions)
    ,1 ) AS churn_percentage
FROM ranks_cte
WHERE plan_id = 4
	AND row_num = 2;
```
 
**Answer:**

| churned_accounts | churn_percentage | 
| ---------------- | ---------------- | 
| 92               | 9.2              |  

- There were 92 customers who churned their accounts after the free trial making up 9.2% of overall customer count.

**6. What is the number and percentage of customer plans after their initial free trial?**

```sql
WITH conversions AS (
  SELECT 
  	customer_id,
  	plan_id,
  	LEAD(plan_id) OVER(
      PARTITION BY customer_id
      ORDER BY plan_id) AS conversion_id 
  FROM foodie_fi.subscriptions
  )
 
 SELECT
  	conversion_id AS plan_id,
    COUNT(customer_id) AS converted_customers,
    ROUND(100.0 *
          COUNT(customer_id)::NUMERIC
          / (SELECT COUNT(DISTINCT customer_id)
             FROM foodie_fi.subscriptions)
    , 1) AS conversion_percentage
 FROM conversions
 WHERE conversion_id IS NOT NULL
 	AND plan_id = 0
 GROUP BY conversion_id
 ORDER BY conversion_id;
```

**Answer:**

| plan_id | converted_customers | conversion_percentage |
| ------- | ------------------- | --------------------- |
| 1       | 546                 | 54.6                  |
| 2       | 325                 | 32.5                  |
| 3       | 37                  | 3.7                   |
| 4       | 92                  | 9.2                   |

- After the trial, 90.8% of customers switch to a paid plan with most opting for a basic monthly or pro monthly plan.
  
**7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?**

```sql
WITH next_date_cte AS (
  SELECT
  	s.customer_id,
  	p.plan_id,
    p.plan_name,
  	s.start_date,
  	LEAD (start_date) OVER (
      PARTITION BY customer_id
      ORDER BY start_date
    ) AS next_date
  FROM foodie_fi.subscriptions AS s
  JOIN foodie_fi.plans AS p
  	ON s.plan_id = p.plan_id
  WHERE start_date <= '2020-12-31'
 )
 
 SELECT
	plan_id,
    plan_name,
    COUNT(DISTINCT customer_id) AS customers,
    ROUND(100.0 *
          COUNT(DISTINCT customer_id)
          / (SELECT COUNT(DISTINCT customer_id)
             FROM foodie_fi.subscriptions)
          ,1) AS percentage
FROM next_date_cte
WHERE next_date IS NULL
GROUP BY plan_id, plan_name;
```

**Answer:**

| plan_id | plan_name     | customers | percentage |
| ------- | ------------- | --------- | ---------- |
| 0       | trial         | 19        | 1.9        |
| 1       | basic monthly | 224       | 22.4       |
| 2       | pro monthly   | 326       | 32.6       |
| 3       | pro annual    | 195       | 19.5       |
| 4       | churn         | 236       | 23.6       |

**8. How many customers have upgraded to an annual plan in 2020?**

```sql
SELECT COUNT(DISTINCT customer_id) AS number_of_annual_plans
FROM foodie_fi.subscriptions
WHERE start_date <= '2020-12-31'
	AND plan_id = 3;
```

**Answer:**

|number_of_annual_plans|
|----------------------|
| 195                  |

**9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?**

```sql
WITH trial_plan AS (
  SELECT
  	customer_id,
  	start_date AS trial_date
  FROM foodie_fi.subscriptions
  WHERE plan_id = 0
), annual_plan AS (
  SELECT
  	customer_id,
  	start_date AS annual_date
  FROM foodie_fi.subscriptions
  WHERE plan_id = 3
)

SELECT 
	ROUND(
      AVG(
        a.annual_date - t.trial_date)
    ,0) AS avg_days_to_upgrade
FROM annual_plan AS a
INNER JOIN trial_plan AS t
	ON a.customer_id = t.customer_id;
```

**Answer:**

| avg_days_to_upgrade |
| ------------------- |
| 105                 |

- Customers take an average of 100 days to switch to the annual plan.
  
**10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)**

```sql
WITH trial_plan AS (
  SELECT
  	customer_id,
  	start_date AS trial_date
  FROM foodie_fi.subscriptions
  WHERE plan_id = 0
), annual_plan AS (
  SELECT
  	customer_id,
  	start_date AS annual_date
  FROM foodie_fi.subscriptions
  WHERE plan_id = 3
), bins AS (
  SELECT
  	WIDTH_BUCKET(a.annual_date - t.trial_date, 0, 365, 12) AS avg_days_to_upgrade
  FROM trial_plan AS t
  INNER JOIN annual_plan AS a
  	ON t.customer_id = a.customer_id
)

SELECT 
	((avg_days_to_upgrade - 1) * 30 || ' - ' || avg_days_to_upgrade * 30 || ' days') AS bucket,
    COUNT (*) AS num_of_customers
FROM bins
GROUP BY avg_days_to_upgrade
ORDER BY avg_days_to_upgrade;
```

**Answer:**

| bucket         | num_of_customers |
| -------------- | ---------------- |
| 0 - 30 days    | 49               |
| 30 - 60 days   | 24               |
| 60 - 90 days   | 35               |
| 90 - 120 days  | 35               |
| 120 - 150 days | 43               |
| 150 - 180 days | 37               |
| 180 - 210 days | 24               |
| 210 - 240 days | 4                |
| 240 - 270 days | 4                |
| 270 - 300 days | 1                |
| 300 - 330 days | 1                |
| 330 - 360 days | 1                |

**11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?**

```sql
WITH ranked_cte AS (
  SELECT
  	s.customer_id,
  	p.plan_id,
  	p.plan_name,
  		LEAD(p.plan_id) OVER(
          PARTITION BY s.customer_id
          ORDER BY s.start_date) AS next_plan_id
  FROM foodie_fi.subscriptions AS s
  INNER JOIN foodie_fi.plans AS p
  	ON s.plan_id = p.plan_id
  WHERE DATE_PART('year', start_date) = 2020
)

SELECT
	COUNT(customer_id) AS churned_customers
FROM ranked_cte
WHERE plan_id = 2
	AND next_plan_id = 1;
```

**Answer:**

| churned_customers |
| ----------------- |
| 0                 |

- There were no customers that downgraded from a pro monthly plan to a basic monthly plan in 2020.
