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
