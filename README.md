# The-Look-E-Commerce-SQL-Project
The Look E-Commerce is a worldwide online shop platform that requires a new marketing strategy for the next season. To assist their digital marketing team, we analyzed data to identify potential markets, products, and customer behaviors that could help drive sales.

To begin, we determined the platform's most promising products, also known as "winning products," by analyzing their profitability. Our goal is to assist the Digital Marketing team in creating the most effective marketing strategy to increase sales. This information will help them identify the most promising products to advertise and determine the optimal locations for advertising, such as identifying which countries have the greatest potential for promoting these products.
I used the SQL syntax below to find the solution:

```
--QUESTION 1
-- What are the top 5 profitable products to advertise

--To find out this, I joined two tables, order_items and products
CREATE TABLE profitable_products AS
SELECT 
 order_items.product_id,
 order_items.order_id,
 order_items.status,
 order_items.sale_price,
 products.id, 
 products.name,
 products.brand,
 products.category,
 products.retail_price,
 products.cost
FROM order_items
LEFT JOIN products
ON order_items.product_id = products.id;

SELECT* FROM profitable_products;

--Let's make another new table for just all trx with the complete status called complete_trx

CREATE TABLE complete_trx AS
SELECT
 order_id,
 product_id,
 name,
 brand,
 sale_price-cost AS profit
FROM profitable_products
WHERE status='Complete';

SELECT* FROM complete_trx;

SELECT 
 product_id,
 name,
 brand,
 SUM(profit) AS product_revenue
FROM complete_trx 
GROUP BY product_id, name, brand ORDER BY product_revenue DESC LIMIT 5;


--QUESTION 2
--To find out the country with the highest sales for each products, I'll create another table, this time with Right Join

CREATE TABLE country_sales AS SELECT 
 users.id,
 users.first_name,
 users.last_name,
 users.email,
 users.age,
 users.gender,
 users.country,
 order_items.order_id,
 order_items.product_id,
 order_items.status
FROM users RIGHT JOIN order_items ON users.id=order_items.user_id;

--I named it as country_sales, now let's see this new table,

SELECT* FROM country_sales;

SELECT country,COUNT(7) AS first_winning_product FROM country_sales WHERE product_id='8429' GROUP BY country ORDER BY first_winning_product DESC;

--Now the 2nd winning product, 22927 AIR JORDAN DOMINATE SHORTS MENS 465071-100 from Jordan with total revenue $2,243.95 USD
SELECT country,COUNT(7) AS second_winning_product FROM country_sales WHERE product_id='22927' GROUP BY country ORDER BY second_winning_product DESC;

--Now the 3rd winning product, 23546 Alpha Industries Rip Stop Short from Alpha Industries with total revenue $2,065.93 USD
SELECT country,COUNT(7) AS third_winning_product FROM country_sales WHERE product_id='23546' GROUP BY country ORDER BY third_winning_product DESC;
--The prospective market for the third winning product is China again, China is in the 2nd place for the 2nd winning product as well.

--Now the 4th winning product, 23654 The North Face Apex Bionic Soft Shell Jacket-Men's from The North Face with total revenue $1,619.98 USD
SELECT country,COUNT(7) AS fourth_winning_product FROM country_sales WHERE product_id='23654' GROUP BY country ORDER BY fourth_winning_product DESC;
--The most prospective market for the fourth winning product is China again

--Lastly, the 5th winning product, 8300 Canada Goose Women's Expedition Parka from Canada Goose with total revenue $1,596.36 USD
--Judging from the name, is this a Canadian product? This time my guess for the prospective market will be this Maple country or maybe China again???
SELECT country,COUNT(7) AS fifth_winning_product FROM country_sales WHERE product_id='8300' GROUP BY country ORDER BY fifth_winning_product DESC;
-- Hmm...the answer is Spain, way too far from my guess, both Canada and China are not even in the list.
```

The results of the top 5 winning products are:
1. The North Face Women's S-XL Oso Jacket - Here are the countries with the largest markets for this product: China, USA, South Korea, and Brazil.
2. Air Jordan Dominate Shorts Mens - Here are the countries with the largest markets for this product: Spain, China, Brazil, Germany and USA.
3. Alpha Industries Rip Stop Short - Here are the countries with the largest markets for this product: China, Brazil, and Spain.
4. The North Face Apex Bionic Soft Shell Jacket - Men's -Here are the countries with the largest markets for this product: China, USA, Brazil, France and Spain.
5. Canada Goose Women's Expedition Parka - Here are the countries with the largest markets for this product: Spain, Belgium, Brazil, Japan, and South Korea.

With the information in hand, the team has created an email campaign featuring the top-performing products. Now, they're gearing up to launch their email marketing campaign and are eager to reach the right audience of users. To do so, they've enlisted the help of the Data Analyst to segment users who have made purchases in the last 90 days.

Below is the SQL syntax that was used to identify the targeted users as requested:

```
--First, I created a table with the columns needed for the observation.

SELECT 
  order_items.order_id,
  order_items.user_id,
  users.first_name,
  users.email,
  order_items.product_id,
  products.category,
  order_items.created_at
FROM order_items
LEFT JOIN users ON users.id=order_items.user_id
LEFT JOIN products ON products.id=order_items.product_id;

CREATE TABLE digital_marketing AS SELECT 
  order_items.order_id,
  order_items.user_id,
  users.first_name,
  users.email,
  order_items.product_id,
  products.category,
  order_items.created_at
FROM order_items
LEFT JOIN users ON users.id=order_items.user_id
LEFT JOIN products ON products.id=order_items.product_id;

--Then, I extract the date to calculate how long was their last transaction from now(I did this on October 8th,2022).

select user_id,first_name,email,category,DATE(created_at) AS last_purchase_date, AGE (to_date('20221008','yyyymmdd'),DATE(created_at)) AS age_transaksi_terakhir FROM digital_marketing;

--It turned out gave me the months and days instead of the total number of days just like I want, so I changed it into this,

select user_id,first_name,email,category,DATE(created_at) AS last_purchase_date, (to_date('20221008','yyyymmdd')- DATE(created_at)) AS age_transaksi_terakhir FROM digital_marketing;

--Next, create another table,

CREATE TABLE final_digital_marketing AS select user_id,first_name,email,category,DATE(created_at) AS last_purchase_date, (to_date('20221008','yyyymmdd')- DATE(created_at)) AS age_transaksi_terakhir FROM digital_marketing;

SELECT* FROM final_digital_marketing;

--Finally,filter to show only those who purchased the past 90 days,

SELECT 
user_id,
first_name,
email,
category,
last_purchase_date,
age_transaksi_terakhir
FROM final_digital_marketing 
WHERE age_transaksi_terakhir<=90
ORDER BY user_id ASC;

--In the syntax above, I used ASC for the user_id to find out how many customers purchased more than one products.

--I found there are several customers purchased more than once in the past 90 days, so I guess the next step is to rank it.

SELECT x.user_id,x.first_name,x.email,x.category,x.age_transaksi_terakhir
FROM(
	SELECT a.*,
	RANK() OVER(PARTITION BY user_id ORDER BY age_transaksi_terakhir ASC)
	transaction_seq
	FROM final_digital_marketing a
	
) x
WHERE transaction_seq=1 AND age_transaksi_terakhir<=90;

--I think the syntax above is worked, yay! And of course there are some customers who purchased a few different products on the same day.
```

In addition to increasing sales, the team also wants to focus on growing the brand and enhancing customer satisfaction. One critical aspect of customer satisfaction is shipping and delivery times, and the team is committed to improving these metrics by enhancing the performance of the operations team.

```
SELECT
 to_char(created_at,'YYYY-MM') AS ordered_month,
 AVG(Extract(epoch from(SELECT(shipped_at-created_at)))/3600::int) AS ordered_to_shipped_time
FROM order_items 
Where shipped_at is NOT NULL
GROUP BY ordered_month
ORDER BY ordered_month DESC;

-- After I run the query, I still curious about the validity of the data, so I checked again with the query below,

SELECT
 to_char(created_at,'YYYY-MM') AS ordered_month,
 created_at,
 shipped_at,
 Extract(epoch from(SELECT(shipped_at-created_at)))/3600::int AS ordered_to_shipped_time
FROM order_items
WHERE shipped_at is NOT NULL;

--After I run the query above, I still found invalid data where the orders has been shipped before receiving the orders.
-- I don't think it is make sense that the date when the orders are shipped is earlier than the orders are created, except the staff has sixth sense that can read the customers' mind.

-- So, I added another filter as below:

SELECT
 to_char(created_at,'YYYY-MM') AS ordered_month,
 created_at,
 shipped_at,
 Extract(epoch from(SELECT(shipped_at-created_at)))/3600::int AS ordered_to_shipped_time
FROM order_items
WHERE shipped_at is NOT NULL AND shipped_at>created_at;

--Now the data looks more make sense, finally I copied the first query and added that filter.

SELECT
 to_char(created_at,'YYYY-MM') AS ordered_month,
 AVG(Extract(epoch from(SELECT(shipped_at-created_at)))/3600::int) AS ordered_to_shipped_time
FROM order_items 
Where shipped_at is NOT NULL AND shipped_at>created_at
GROUP BY ordered_month
ORDER BY ordered_month DESC;
```
