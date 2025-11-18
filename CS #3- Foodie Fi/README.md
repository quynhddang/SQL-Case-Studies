# 🥑 CS# 3: Foodie-Fi

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/4ce0b466-f437-448f-98e1-3aed99eb2978" />

## 📂 Table of Contents

- [Business Task](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#business-task)
- [Entity Relationship Diagram](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#entity-relationship-diagram)
- [Questions and Solutions](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#questions-and-solutions)
  
## Business Task

Danny launched his new startup Foodie-Fi, a streaming service that only has food related content, and started selling monthly and annual subscriptions, giving customers on-demand access to exclusive food videos from around the world.

This case study focuses on using subscription style digital data to answer important business questions regarding customer journey, payments, and business performance.

## Entity Relationship Diagram

<img width="698" height="290" alt="image" src="https://github.com/user-attachments/assets/bb83f8d0-ed9d-4c76-85e0-27165706d2df" />

## Questions and Solutions

- [A. Customer Journey](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#a-customer-journey)
- [B. Data Analysis Questions](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#b-data-analysis-questions)
- [C. Challenge Payment Question](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#c-challenge-payment-questions)
- [D. Outside The Box Questions](https://github.com/quynhddang/8-Week-SQL/edit/main/CS%20%233-%20Foodie%20Fi/README.md#d-outside-the-box-questions)

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

Based on the results, I will focus on three customers and their onbaording journeys.

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

**5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?**

**6. What is the number and percentage of customer plans after their initial free trial?**

**7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?**

**8. How many customers have upgraded to an annual plan in 2020?**

**9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?**

**10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)**

**11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?**

### C. Challenge Payment Questions
### D. Outside The Box Questions

