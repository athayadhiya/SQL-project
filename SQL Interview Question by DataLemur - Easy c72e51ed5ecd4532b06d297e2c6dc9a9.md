# SQL Interview Question by DataLemur - Easy

# [Facebook - Page with No Likes](https://datalemur.com/questions/sql-page-with-no-likes)

**Question:**

Assume you're given two tables containing data about Facebook Pages and their respective likes (as in "Like a Facebook Page").

Write a query to return the IDs of the Facebook pages that have zero likes. The output should be sorted in ascending order based on the page IDs.

**Answer:**

```sql
SELECT page_id FROM pages
LEFT JOIN page_likes
  USING (page_id)
WHERE liked_date IS NULL
ORDER BY page_id ASC;
```

# [Tesla - Unfinished Parts](https://datalemur.com/questions/tesla-unfinished-parts)

**Question:**

Tesla is investigating production bottlenecks and they need your help to extract the relevant data.

Write a query that determines which parts with the assembly steps have initiated the assembly process but remain unfinished.

**Answer:**

```sql
SELECT part, assembly_step FROM parts_assembly
WHERE finish_date is NULL;
```

# [Twitter - Histogram of Tweet](https://datalemur.com/questions/sql-histogram-tweets)

**Question:** 

Assume you're given a table Twitter tweet data, write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet count per user as the bucket and the number of Twitter users who fall into that bucket.

In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

**Answer:**

```sql
WITH tweet_count AS (
  SELECT user_id, COUNT(tweet_id) AS tweet_count FROM tweets
  WHERE tweet_date BETWEEN '01-01-2022' AND '12-31-2022'
  GROUP BY user_id)

SELECT COUNT(user_id) as users_num, tweet_count AS tweet_bucket FROM tweet_count
GROUP BY tweet_count;
```

# [New York Times - Laptop vs Mobile Viewership](https://datalemur.com/questions/laptop-mobile-viewership)

**Question:**

Assume you're given the table on user viewership categorised by device type where the three types are laptop, tablet, and phone.

Write a query that calculates the total viewership for laptops and mobile devices where mobile is defined as the sum of tablet and phone viewership. Output the total viewership for laptops as `laptop_reviews` and the total viewership for mobile devices as `mobile_views`.

**Answer:**

```sql
SELECT 
  COUNT(*) FILTER (WHERE device_type = 'laptop') AS laptop_reviews,
  COUNT(*) FILTER (WHERE device_type IN ('tablet', 'phone')) AS mobile_reviews
FROM viewership;
```

# [LinkedIn - Data Science Skill](https://datalemur.com/questions/matching-skills)

**Question:**

Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL.

Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.

**Answer:**

```sql
SELECT candidate_id FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(skill) = 3
ORDER BY candidate_id ASC;
```

# [Facebook - Average Post Hiatus (Part 1)](https://datalemur.com/questions/sql-average-post-hiatus-1)

**Question:**

Given a table of Facebook posts, for each user who posted at least twice in 2021, write a query to find the number of days between each user’s first post of the year and last post of the year in the year 2021. Output the user and number of the days between each user's first and last post.

**Answer:**

```sql
SELECT 
  user_id,
  EXTRACT(DAY FROM MAX(post_date) - MIN(post_date)) AS days_between
FROM posts
WHERE post_date BETWEEN '01-01-2021' AND '12-31-2021'
GROUP BY user_id
HAVING EXTRACT(DAY FROM MAX(post_date) - MIN(post_date)) > 1;
```

# [Microsoft - Teams Power Users](https://datalemur.com/questions/teams-power-users)

**Question:**

Write a query to identify the top 2 Power Users who sent the highest number of messages on Microsoft Teams in August 2022. Display the IDs of these 2 users along with the total number of messages they sent. Output the results in descending order based on the count of the messages.

**Answer:**

```sql
SELECT sender_id, COUNT(message_id) AS message_count FROM messages
WHERE EXTRACT(MONTH FROM sent_date) = '8' AND EXTRACT(YEAR FROM sent_date) = '2022'
GROUP BY sender_id
ORDER BY message_count DESC
LIMIT 2;
```

# [LinkedIn - Duplicate Job Listings](https://datalemur.com/questions/duplicate-job-listings)

**Question:**

Assume you're given a table containing job postings from various companies on the LinkedIn platform. Write a query to retrieve the count of companies that have posted duplicate job listings.

**Answer:**

```sql
WITH job_count AS (
  SELECT company_id, title, description, COUNT(job_id) AS job_count FROM job_listings
  GROUP BY company_id, title, description)
  
SELECT COUNT(company_id) AS duplicate_companies FROM job_count
WHERE job_count > 1;
```

# [Robinhood - Cities with Completed Trades](https://datalemur.com/questions/completed-trades)

**Question:**

Assume you're given the tables containing completed trade orders and user details in a Robinhood trading system.

Write a query to retrieve the top three cities that have the highest number of completed trade orders listed in descending order. Output the city name and the corresponding number of completed trade orders.

**Answer:**

```sql
SELECT users.city AS city, COUNT(trades.order_id) AS total_orders FROM trades
INNER JOIN users 
  USING (user_id)
WHERE trades.status = 'Completed'
GROUP BY users.city
ORDER BY total_orders DESC
LIMIT 3;
```

# [Amazon - Average Review Ratings](https://datalemur.com/questions/sql-avg-review-ratings)

**Question:**

Given the reviews table, write a query to retrieve the average star rating for each product, grouped by month. The output should display the month as a numerical value, product ID, and average star rating rounded to two decimal places. Sort the output first by month and then by product ID.

**Answer:**

```sql
SELECT 
  product_id, 
  ROUND(AVG(stars), 2) AS avg_stars, 
  EXTRACT(MONTH FROM submit_date) AS mth
FROM reviews
GROUP BY mth, product_id
ORDER BY mth, product_id;
```

# [Facebook - App Clickthrough Rate](https://datalemur.com/questions/click-through-rate)

**Question:**

Assume you have an events table on Facebook app analytics. Write a query to calculate the click-through rate (CTR) for the app in 2022 and round the results to 2 decimal places.

**Answer:**

```sql
SELECT 
  app_id, 
  ROUND(100.0 *
    SUM(1) FILTER (WHERE event_type = 'click') /
    SUM(1) FILTER (WHERE event_type = 'impression'), 2) AS CTR
FROM events
WHERE DATE_PART('year', timestamp) = '2022'
GROUP BY app_id;
```

# [Tiktok - Second Day Confirmation](https://datalemur.com/questions/second-day-confirmation)

**Question:**

Assume you're given tables with information about TikTok user sign-ups and confirmations through email and text. New users on TikTok sign up using their email addresses, and upon sign-up, each user receives a text message confirmation to activate their account.

Write a query to display the user IDs of those who did not confirm their sign-up on the first day, but confirmed on the second day.

**Answer:**

```sql
SELECT user_id FROM emails
INNER JOIN texts 
  USING (email_id)
WHERE signup_action = 'Confirmed'
  AND DATE_PART('DAY', action_date - signup_date) = 1;
```

# JP Morgan Chase - Cards Issued Difference

**Question:**

Your team at JPMorgan Chase is soon launching a new credit card, and to gain some context, you are analyzing how many credit cards were issued each month.

Write a query that outputs the name of each credit card and the difference in issued amount between the month with the most cards issued, and the least cards issued. Order the results according to the biggest difference.

**Answer:**

```sql
SELECT 
  card_name,
  MAX(issued_amount) - MIN(issued_amount) AS difference
FROM monthly_cards_issued
GROUP BY card_name
ORDER BY difference DESC;
```

# [Alibaba - Compressed Mean](https://datalemur.com/questions/alibaba-compressed-mean)

**Question:**

You're trying to find the mean number of items per order on Alibaba, rounded to 1 decimal place using tables which includes information on the count of items in each order (`item_count` table) and the corresponding number of orders for each item count (`order_occurrences` table).

**Answer:**

```sql
SELECT ROUND(
  SUM(item_count::DECIMAL * order_occurrences) /
  SUM(order_occurrences), 1) AS mean
FROM items_per_order;
```

# [CVS Health - Pharmacy Analytics (Part 1)](https://datalemur.com/questions/top-profitable-drugs)

**Question:**

CVS Health is trying to better understand its pharmacy sales, and how well different products are selling. Each drug can only be produced by one manufacturer.

Write a query to find the top 3 most profitable drugs sold, and how much profit they made. Assume that there are no ties in the profits. Display the result from the highest to the lowest total profit.

**Answer:**

```sql
SELECT drug, total_sales - cogs AS total_profit
FROM pharmacy_sales
ORDER BY total_profit DESC
LIMIT 3;
```

# [CVS Health - Pharmacy Analytics (Part 2)](https://datalemur.com/questions/non-profitable-drugs)

**Question:**

CVS Health is analyzing its pharmacy sales data, and how well different products are selling in the market. Each drug is exclusively manufactured by a single manufacturer.

Write a query to identify the manufacturers associated with the drugs that resulted in losses for CVS Health and calculate the total amount of losses incurred.

Output the manufacturer's name, the number of drugs associated with losses, and the total losses in absolute value. Display the results sorted in descending order with the highest losses displayed at the top.

**Answer:**

```sql
SELECT 
  manufacturer,
  COUNT(drug) AS drug_count,
  SUM(cogs - total_sales) AS total_loss 
FROM pharmacy_sales
WHERE cogs > total_sales
GROUP BY manufacturer
ORDER BY total_loss DESC;
```

# [CVS Health - Pharmacy Analytics (Part 3)](https://datalemur.com/questions/total-drugs-sales)

**Question:**

CVS Health wants to gain a clearer understanding of its pharmacy sales and the performance of various products.

Write a query to calculate the total drug sales for each manufacturer. Round the answer to the nearest million and report your results in descending order of total sales. In case of any duplicates, sort them alphabetically by the manufacturer name.

Since this data will be displayed on a dashboard viewed by business stakeholders, please format your results as follows: "$36 million".

**Answer:**

```sql
SELECT 
  manufacturer,
  CONCAT('$', ROUND(SUM(total_sales) / 1000000), ' million') AS sale
FROM pharmacy_sales
GROUP BY manufacturer
ORDER BY SUM(total_sales) DESC, manufacturer ASC;
```

# [UnitedHealth - Patient Support Analysis (Part 1)](https://datalemur.com/questions/frequent-callers)

**Question:**

UnitedHealth has a program called Advocate4Me, which allows members to call an advocate and receive support for their health care needs – whether that's behavioural, clinical, well-being, health care financing, benefits, claims or pharmacy help.

Write a query to find how many UHG members made 3 or more calls. `case_id` column uniquely identifies each call made.

**Answer:**

```sql
WITH count_call AS (
  SELECT policy_holder_id, COUNT(case_id) AS count_call FROM callers
  GROUP BY policy_holder_id)

SELECT COUNT(DISTINCT policy_holder_id) FROM count_call
WHERE count_call >= 3;
```

# [UnitedHealth - Patient Support Analysis (Part 2)](https://datalemur.com/questions/uncategorized-calls-percentage)

**Question:**

UnitedHealth Group has a program called Advocate4Me, which allows members to call an advocate and receive support for their health care needs – whether that's behavioural, clinical, well-being, health care financing, benefits, claims or pharmacy help.

Calls to the Advocate4Me call centre are categorised, but sometimes they can't fit neatly into a category. These uncategorised calls are labelled “n/a”, or are just empty (when a support agent enters nothing into the category field).

Write a query to find the percentage of calls that cannot be categorised. Round your answer to 1 decimal place.

**Answer:**

```sql
SELECT ROUND(100 * 
  COUNT(*) FILTER (WHERE call_category IS NULL OR call_category = 'n/a') / 
  COUNT(call_received), 1) AS call_precentage 
FROM callers;
```