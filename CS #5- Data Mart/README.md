# 🏪🌿 CS# 5: Data Mart

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/e411c63c-24dc-4bdc-b7c9-3ca34e39e6f2" />

## 📂 Table of Contents

- [Business Task](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%235-%20Data%20Mart/README.md#business-task)
- [Entity Relationship Diagram](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%235-%20Data%20Mart/README.md#entity-relationship-diagram)
- [Questions and Solutions](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%235-%20Data%20Mart/README.md#questions-and-solutions)

All information regarding the case study has been sourced [here](https://8weeksqlchallenge.com/case-study-5/).

## Business Task

Data Mart is an international online supermarket that specializes in fresh produce.

In June 2020, large scale supply changes were made at Data Mart. All Data Mart products now use sustainable packaging methods in every single step from the farm to the customer.

Danny needs help to quantify the impact of this change on the sales performance for Data Mart and it's separate business areas.

## Entity Relationship Diagram

<img width="336" height="400" alt="image" src="https://github.com/user-attachments/assets/88e1adc5-4714-431b-99b5-f2abb15432ff" />

Further details about the dataset:

- Data Mart has international operations using a multi-`region` strategy.
- Data Mart has both, a retail and online `platform` in the form of a Shopify store front to serve their customers.
- Customer `segment` and `customer_type` data relates to personal age and demographics information that is shared with Data Mart.
- `transactions` is the count of unique purchases made through Data Mart and `sales` is the actual dollar amount of purchases.

Each record in the dataset is related to a specific aggregated slice of the underlying sales data rolled up into a `week_date` value which represents the start of the sales week.

## Questions and Solutions

- [A. Data Cleaning](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%235-%20Data%20Mart/README.md#a-data-cleaning)
- [B. Data Exploration](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%235-%20Data%20Mart/README.md#b-data-exploration)
- [C. Before & After Analysis](https://github.com/quynhddang/SQL-Case-Studies/edit/main/CS%20%235-%20Data%20Mart/README.md#c-before--after-analysis)

### A. Data Cleaning

In a single query, perform the following operations and generate a new table in the `data_mart` schema named `clean_weekly_sales`:

- Convert the `week_date` to a `DATE` format
- Add a `week_number` as the second column for each `week_date` value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc
- Add a `month_number` with the calendar month for each `week_date` value as the 3rd column
- Add a `calendar_year` column as the 4th column containing either 2018, 2019 or 2020 values
- Add a new column called `age_band` after the original `segment` column using the following mapping on the number inside the `segment` value

| segment |	age_band    |
| ------- | ----------- |
| 1	      | Young Adults|
| 2	      | Middle Aged |
| 3 or 4	| Retirees    |

- Add a new `demographic` column using the following mapping for the first letter in the `segment` values:

| segment | demographic |
| ------- | ----------- |
| C	      | Couples     | 
| F	      | Families    |

- Ensure all `null` string values with an "unknown" string value in the original `segment` column as well as the new `age_band` and `demographic` columns
- Generate a new `avg_transaction` column as the `sales` value divided by `transactions` rounded to 2 decimal places for each record

**Answer:**

```sql
DROP TABLE IF EXISTS clean_weekly_sales;
CREATE TEMP TABLE clean_weekly_sales AS (
  SELECT
  	TO_DATE(week_Date, 'DD/MM/YY') AS week_date,
  	DATE_PART('week', TO_DATE(week_date, 'DD/MM/YY')) AS week_number,
  	DATE_PART('month', TO_DATE(week_date, 'DD/MM/YY')) AS month_number,
  	DATE_PART('year', TO_DATE(week_date, 'DD/MM/YY')) AS calendar_year,
  	region,
  	platform,
  	segment,
  	CASE
  		WHEN RIGHT(segment, 1) = '1' THEN 'Young Adults'
  		WHEN RIGHT(segment, 1) = '2' THEN 'Middle Aged'
  		WHEN RIGHT(segment, 1) in ('3', '4') THEN 'Retirees'
  		ELSE 'unknown' END AS age_band,
  	CASE
  		WHEN LEFT(segment, 1) = 'C' THEN 'Couples'
  		WHEN LEFT(segment, 1) = 'F' THEN 'Families'
  		ELSE 'unknown' END AS demographic,
  	transactions,
  	ROUND((sales::NUMERIC/transactions),2) AS avg_transaction,
  	sales
  FROM data_mart.weekly_sales
  );
```
| week_date  | week_number | month_number | calendar_year | region        | platform | segment | age_band     | demographic | transactions | avg_transaction | sales    |
| ---------- | ----------- | ------------ | ------------- | ------------- | -------- | ------- | ------------ | ----------- | ------------ | --------------- | -------- |
| 2020-08-31 | 36          | 8            | 2020          | ASIA          | Retail   | C3      | Retirees     | Couples     | 120631       | 30.31           | 3656163  |
| 2020-08-31 | 36          | 8            | 2020          | ASIA          | Retail   | F1      | Young Adults | Families    | 31574        | 31.56           | 996575   |
| 2020-08-31 | 36          | 8            | 2020          | USA           | Retail   | null    | unknown      | unknown     | 529151       | 31.20           | 16509610 |
| 2020-08-31 | 36          | 8            | 2020          | EUROPE        | Retail   | C1      | Young Adults | Couples     | 4517         | 31.42           | 141942   |
| 2020-08-31 | 36          | 8            | 2020          | AFRICA        | Retail   | C2      | Middle Aged  | Couples     | 58046        | 30.29           | 1758388  |
| 2020-08-31 | 36          | 8            | 2020          | CANADA        | Shopify  | F2      | Middle Aged  | Families    | 1336         | 182.54          | 243878   |
| 2020-08-31 | 36          | 8            | 2020          | AFRICA        | Shopify  | F3      | Retirees     | Families    | 2514         | 206.64          | 519502   |

- This is how the new table should look like.

### B. Data Exploration

**1. What day of the week is used for each week_date value?**

```sql
SELECT DISTINCT(TO_CHAR(week_date, 'day')) AS week_day
FROM clean_weekly_sales;
```

**Answer:**

| week_day |
| -------- |
| monday   |

- Monday is used for each week_date value.

**2. What range of week numbers are missing from the dataset?**

```sql
SELECT DISTINCT(week_number) 
FROM clean_weekly_sales
ORDER BY week_number ASC;
```

**Answer:**

| week_number |
| ----------- |
| 13          |
| 14          |
| 15          |
| 16          |
| 17          |
| 18          |

- Only the first 6 rows of the results are posted here, but the results showed weeks 13 - 36 which means weeks 1 - 12 and 37 - 52 are missing from the dataset.
  
**3. How many total transactions were there for each year in the dataset?**

```sql
SELECT
	calendar_year,
    SUM(transactions) AS total_transactions
FROM clean_weekly_sales
GROUP BY calendar_year
ORDER BY calendar_year;
```

**Answer:**

| calendar_year | total_transactions |
| ------------- | ------------------ |
| 2018          | 346406460          |
| 2019          | 365639285          |
| 2020          | 375813651          |

**4. What is the total sales for each region for each month?**

```sql
SELECT
	month_number,
    region,
    SUM(sales) AS total_sales
FROM clean_weekly_sales
GROUP BY month_number, region
ORDER BY month_number, region;
```

**Answer:**

| month_number | region        | total_sales |
| ------------ | ------------- | ----------- |
| 9            | AFRICA        | 276320987   |
| 9            | ASIA          | 252836807   |
| 9            | CANADA        | 69067959    |
| 9            | EUROPE        | 18877433    |
| 9            | OCEANIA       | 372465518   |
| 9            | SOUTH AMERICA | 34175583    |
| 9            | USA           | 110532368   |

- Only the total sales for September are posted.
  
**5. What is the total count of transactions for each platform**

```sql
SELECT
	platform,
    SUM(transactions) AS total_transactions
FROM clean_weekly_sales
GROUP BY platform
ORDER BY platform;
```

**Answer**:

| platform | total_transactions |
| -------- | ------------------ |
| Retail   | 1081934227         |
| Shopify  | 5925169            |

**6. What is the percentage of sales for Retail vs Shopify for each month?**

```sql
WITH monthly_sales AS (
  SELECT
  	calendar_year, 
  	month_number,
  	platform,
  	SUM(sales) AS monthly_sales
  FROM clean_weekly_sales
  GROUP BY calendar_year, month_number, platform
)

SELECT
	calendar_year,
    month_number,
    ROUND(100* MAX
          (CASE
           WHEN platform = 'Retail' THEN monthly_sales ELSE NULL END)
          / SUM(monthly_sales),2) AS retail_percentage,
    ROUND(100 * MAX
          (CASE
           WHEN platform = 'Shopify' THEN monthly_sales ELSE NULL END)
          / SUM(monthly_sales),2) AS shopify_percentage
FROM monthly_sales
GROUP BY calendar_year, month_number
ORDER BY calendar_year, month_number;
```

**Answer:**

| calendar_year | month_number | retail_percentage | shopify_percentage |
| ------------- | ------------ | ----------------- | ------------------ |
| 2018          | 3            | 97.92             | 2.08               |
| 2018          | 4            | 97.93             | 2.07               |
| 2018          | 5            | 97.73             | 2.27               |
| 2018          | 6            | 97.76             | 2.24               |
| 2018          | 7            | 97.75             | 2.25               |
| 2018          | 8            | 97.71             | 2.29               |
| 2018          | 9            | 97.68             | 2.32               |

- Only results from 2018 are displayed. The results includes the percentage of sales for each month until 2020.

**7. What is the percentage of sales by demographic for each year in the dataset?**

```sql
WITH demographic_sales AS (
  SELECT
  	calendar_year, 
  	demographic,
  	SUM(sales) AS yearly_sales
  FROM clean_weekly_sales
  GROUP BY calendar_year, demographic
)

SELECT
	calendar_year,
    ROUND(100* MAX
          (CASE
           WHEN demographic = 'Couples' THEN yearly_sales ELSE NULL END)
          / SUM(yearly_sales),2) AS couples_percentage,
    ROUND(100 * MAX
          (CASE
           WHEN demographic = 'Families' THEN yearly_sales ELSE NULL END)
          / SUM(yearly_sales),2) AS families_percentage,
    ROUND(100 * MAX
          (CASE
           WHEN demographic = 'unknown' THEN yearly_sales ELSE NULL END)
          / SUM(yearly_sales),2) AS unknown_percentage
FROM demographic_sales
GROUP BY calendar_year
ORDER BY calendar_year;
```

**Answer:** 

| calendar_year | couples_percentage | families_percentage | unknown_percentage |
| ------------- | ------------------ | ------------------- | ------------------ |
| 2018          | 26.38              | 31.99               | 41.63              |
| 2019          | 27.28              | 32.47               | 40.25              |
| 2020          | 28.72              | 32.73               | 38.55              |


**8. Which age_band and demographic values contribute the most to Retail sales?**

```sql
SELECT 
  age_band, 
  demographic, 
  SUM(sales) AS retail_sales,
  ROUND(100 * 
    SUM(sales)::NUMERIC 
    / SUM(SUM(sales)) OVER (),
  1) AS contribution_percentage
FROM clean_weekly_sales
WHERE platform = 'Retail'
GROUP BY age_band, demographic
ORDER BY retail_sales DESC;
```

**Answer:**


| age_band     | demographic | retail_sales | contribution_percentage |
| ------------ | ----------- | ------------ | ----------------------- |
| unknown      | unknown     | 16067285533  | 40.5                    |
| Retirees     | Families    | 6634686916   | 16.7                    |
| Retirees     | Couples     | 6370580014   | 16.1                    |
| Middle Aged  | Families    | 4354091554   | 11.0                    |
| Young Adults | Couples     | 2602922797   | 6.6                     |
| Middle Aged  | Couples     | 1854160330   | 4.7                     |
| Young Adults | Families    | 1770889293   | 4.5                     |

- The unknown `age_band` and `demographic` values contributed the most to retail sales.

**9. Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?**

We can not use the `avg_transaction` column to find the average transactions size for each year because using an average to calculate another average leads to inaccurate and incorrect results. In order to find the average transaction size for each year, the total sum of sales must be divided by the total sum of transactions for each year and platform.

```sql
SELECT
	calendar_year,
    platform,
    ROUND(AVG(avg_transaction), 2) AS incorrect_avg,
    ROUND(SUM(sales)::NUMERIC/ SUM(transactions), 2) AS correct_avg
FROM clean_weekly_sales
GROUP BY calendar_year, platform
ORDER BY calendar_year, platform;
```

**Answer:**

| calendar_year | platform | incorrect_avg | correct_avg |
| ------------- | -------- | ------------- | ----------- |
| 2018          | Retail   | 42.91         | 36.56       |
| 2018          | Shopify  | 188.28        | 192.48      |
| 2019          | Retail   | 41.97         | 36.83       |
| 2019          | Shopify  | 177.56        | 183.36      |
| 2020          | Retail   | 40.64         | 36.56       |
| 2020          | Shopify  | 174.87        | 179.03      |

### C. Before & After Analysis

This technique is usually used when we inspect an important event and want to inspect the impact before and after a certain point in time.

Taking the `week_date` value of `2020-06-15` as the baseline week where the Data Mart sustainable packaging changes came into effect.

We would include all `week_date` values for `2020-06-15` as the start of the period after the change and the previous `week_date` values would be before

Using this analysis approach - answer the following questions:

**1. What is the total sales for the 4 weeks before and after `2020-06-15`? What is the growth or reduction rate in actual values and percentage of sales?**

First, we need to determine the week number matching to `2020-06-15`.

```sql
SELECT DISTINCT week_number
FROM clean_weekly_sales
WHERE week_date = '2020-06-15'
	AND calendar_year = '2020';
```

| week_number |
| ----------- |
| 25          |

Next, we determine the total sales for 4 weeks before and after `2020-06-15`.

```sql
WITH packaging_changes AS (
  SELECT
  	week_date,
  	week_number,
  	SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE (week_number BETWEEN 21 AND 28)
  	AND calendar_year = '2020'
  GROUP BY week_date, week_number
)

SELECT
	SUM(CASE
        WHEN week_number BETWEEN 21 AND 24 THEN total_sales END) AS before_packaging_sales,
    SUM(CASE
        WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_packaging_sales
FROM packaging_changes;
```

| before_packaging_sales | after_packaging_sales |
| ---------------------- | --------------------- |
| 2345878357             | 2318994169            |

Finally, we now determine the growth or reduction rate in actual values and percentage of sales.

```sql
WITH packaging_changes AS (
  SELECT
  	week_date,
  	week_number,
  	SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE (week_number BETWEEN 21 AND 28)
  	AND calendar_year = '2020'
  GROUP BY week_date, week_number
), before_after_sale AS (
  SELECT
	SUM(CASE
        WHEN week_number BETWEEN 21 AND 24 THEN total_sales END) AS before_packaging_sales,
    SUM(CASE
        WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_packaging_sales
  FROM packaging_changes
)

SELECT 
	after_packaging_sales - before_packaging_sales AS sale_changes,
    ROUND(100* (after_packaging_sales - before_packaging_sales)
          		/before_packaging_sales, 2) AS reduction_rate
FROM before_after_sale;
```
| sale_changes | reduction_rate |
| ------------ | -------------- |
| -26884188    | -1.15          |

Since the packaging changes, there has been a decrease in sales by $26,884,188 at a reduction rate of 1.15%. 

**2. What about the entire 12 weeks before and after?**

```sql
WITH packaging_changes AS (
  SELECT
  	week_date,
  	week_number,
  	SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE (week_number BETWEEN 13 AND 37)
  	AND calendar_year = '2020'
  GROUP BY week_date, week_number
), before_after_sale AS (
  SELECT
	SUM(CASE
        WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging_sales,
    SUM(CASE
        WHEN week_number BETWEEN 25 AND 37 THEN total_sales END) AS after_packaging_sales
  FROM packaging_changes
)

SELECT 
	after_packaging_sales - before_packaging_sales AS sale_changes,
    ROUND(100* (after_packaging_sales - before_packaging_sales)
          		/before_packaging_sales, 2) AS reduction_rate
FROM before_after_sale;
```
| sale_changes | reduction_rate |
| ------------ | -------------- |
| -152325394   | -2.14          |

Based on these results, there have been more losses in sales with an increased reduction rate of 2.14%. 

**3. How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?**

```sql
WITH packaging_changes AS (
  SELECT
  	calendar_year,
  	week_number,
  	SUM(sales) AS total_sales
  FROM clean_weekly_sales
  WHERE (week_number BETWEEN 13 AND 37)
  GROUP BY calendar_year, week_number
), before_after_sale AS (
  SELECT
  	calendar_year,
	SUM(CASE
        WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging_sales,
    SUM(CASE
        WHEN week_number BETWEEN 25 AND 37 THEN total_sales END) AS after_packaging_sales
  FROM packaging_changes
  GROUP BY calendar_year
)

SELECT 
	calendar_year,
	after_packaging_sales - before_packaging_sales AS sale_changes,
    ROUND(100* (after_packaging_sales - before_packaging_sales)
          		/before_packaging_sales, 2) AS variance_rate
FROM before_after_sale;
```

| calendar_year | sale_changes | variance_rate |
| ------------- | ------------ | -------------- |
| 2018          | 104256193    | 1.63           |
| 2019          | -20740294    | -0.30          |
| 2020          | -152325394   | -2.14          |

Based on these results, there are noticable changes in the growth and reduction rates in all three years. The most significant contrast can be seen when comparing sales in 2020 to 2018 where a 3.77% sales pecentage rate gap difference can be seen.
