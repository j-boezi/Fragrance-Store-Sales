# Fragrance-Store-Sales
To view the associated Tableau Dashboard, click here:<img width="1857" height="797" alt="image" src="https://github.com/user-attachments/assets/d065abf8-b0b9-49b7-b849-fbd627f44f89" />
 https://public.tableau.com/views/FragranceStore/FragranceStoreSalesData-January2025?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link

# Purpose

***What drives customers to buy a seasonal candle over a year-round perfume and how does discounting impact profits?
These are the kinds of real-world business questions that inspired this project.***

To practice end-to-end data analysis, I created a fictional dataset simulating January sales at a small online fragrance store. Every sale is categorized by product type (candle, soap, or perfume) and subcategorized as either seasonal or year-round.

This project allowed me to analyze how discount rates affect quantity sold and total profit, compare profit margins across categories and product types, practice writing SQL queries for business insights, and build an interactive Tableau dashboard to communicate findings clearly.

By building the dataset myself and analyzing it with SQL and Tableau, I aimed to mimic a realistic business case—using data to uncover trends, inform strategy, and tell a clear story.

# Database Setup

**Create "sales" table in PostgreSQL:**
```sql
CREATE TABLE sales (
	order_id serial PRIMARY KEY,
	order_date date,
	category varchar(10),
	subcategory varchar(15),
	sales integer,
	quantity integer,
	discount decimal(1,1),
	profit integer
);
```
**After table creation, import "sales.csv" into PostgreSQL.**

# Data Exploration & Analysis

**What are the categories and subcategories?**
```sql
SELECT category, subcategory
FROM sales
GROUP BY category, subcategory
ORDER by category ASC, subcategory ASC;
```
<img width="302" height="187" alt="image" src="https://github.com/user-attachments/assets/b105a0e4-2921-4050-8b75-036692f7ced8" />


**What quantity did each product sell?**
```sql
SELECT category, subcategory, SUM(quantity) AS products_sold
FROM sales
GROUP BY category, subcategory
ORDER BY products_sold DESC;
```
<img width="410" height="191" alt="image" src="https://github.com/user-attachments/assets/4d6f89af-d3e4-4b07-98b0-5ed3d5c4801d" />


**How much profit did each product make?**
```sql
SELECT category, subcategory, SUM(profit) AS total_profit
FROM sales
GROUP BY category, subcategory
ORDER BY total_profit DESC;
```
<img width="390" height="187" alt="image" src="https://github.com/user-attachments/assets/f4a73669-8eed-48dc-a34f-13627dd6c5f3" />


**Did the Year-Round or Seasonal subcategory sell more products?**
```sql
SELECT subcategory, SUM(quantity) AS products_sold
FROM sales
GROUP BY subcategory
ORDER BY products_sold DESC;
```
<img width="260" height="91" alt="image" src="https://github.com/user-attachments/assets/a8138075-d194-43f8-9d67-cbc2259a657d" />


**Did the Year-Round or Seasonal subcategory make more profit?**
```sql
SELECT subcategory, SUM(profit) AS subcategory_profit
FROM sales
GROUP BY subcategory
ORDER BY subcategory_profit DESC;
```
<img width="283" height="88" alt="image" src="https://github.com/user-attachments/assets/928acdd4-d64e-4c8e-b582-8f067fc39459" />


**How much more profit did Year-Round products make than Seasonal products?**
```sql
SELECT SUM(profit) -
	(
	SELECT SUM(profit)
	FROM sales
	WHERE subcategory = 'Seasonal'
	)
AS difference_in_profit
FROM sales
WHERE subcategory = 'Year-Round';
```
<img width="137" height="60" alt="image" src="https://github.com/user-attachments/assets/68155e3b-afbe-45c1-8756-81190b7fda30" />


**Rank the categories from high to lowest profit.**
```sql
SELECT category, SUM(profit) AS total_profit, RANK() OVER(
	ORDER BY SUM(profit) DESC
	) AS category_profit_rank
FROM sales
GROUP BY category;
```
<img width="383" height="113" alt="image" src="https://github.com/user-attachments/assets/7be6a7fa-c69d-4468-93e4-f21a37383127" />


**Which product sold the highest quantity?**
```sql
SELECT category, subcategory, SUM(quantity) as products_sold
FROM sales
GROUP BY category, subcategory
ORDER BY products_sold DESC
LIMIT 1;
```
<img width="411" height="64" alt="image" src="https://github.com/user-attachments/assets/6057e4de-faa3-4107-8c59-8461eb605331" />


**Which products had days where they made less than $0 in profit and how many instances were there per product?**
```sql
SELECT category, subcategory, COUNT(profit) AS instances_of_negative_profit
FROM sales
WHERE profit < 0
GROUP BY category, subcategory
ORDER BY instances_of_negative_profit DESC;
```
<img width="486" height="86" alt="image" src="https://github.com/user-attachments/assets/707c78c5-93c6-4c7f-b4c3-5f5d3b33b57d" />


**How much money did Seasonal Perfume net on the days it made less than $0?**
```sql
SELECT SUM(profit) as negative_profit
FROM sales
WHERE category = 'Perfume'
AND subcategory = 'Seasonal'
AND profit < 0;
```
<img width="113" height="63" alt="image" src="https://github.com/user-attachments/assets/9e9f6bf3-1fcb-47dc-82d3-64dc8dd7300d" />


**How did each product perform on a scale from poor to exceptional?**
```sql
SELECT category, subcategory, SUM(profit) AS total_profit,
CASE
	WHEN SUM(profit) < 0 THEN 'poor'
	WHEN SUM(profit) BETWEEN 0 AND 4999 THEN 'low'
	WHEN SUM(profit) BETWEEN 5000 AND 9999 THEN 'moderate'
	WHEN SUM(profit) BETWEEN 10000 AND 19999 THEN 'great'
	ELSE 'exceptional'
	END AS product_performance
FROM sales
GROUP BY category, subcategory
ORDER BY total_profit DESC;
```
<img width="542" height="190" alt="image" src="https://github.com/user-attachments/assets/51eb25fc-3ddc-49ba-80b4-f24e2b85c398" />


**How much profit did each discount type make?**
```sql
SELECT category, subcategory, SUM(quantity) AS items_sold
FROM sales
GROUP BY category, subcategory
ORDER BY items_sold DESC;
```
<img width="391" height="187" alt="image" src="https://github.com/user-attachments/assets/6b7dc60d-e3cb-4dd7-9d54-ceb2693780ff" />


**Did a higher discount translate to a higher quantity sold?**
```sql
SELECT discount*100 || '%' AS discount, SUM(quantity) AS product_sold
FROM sales
GROUP BY discount;
```
<img width="177" height="86" alt="image" src="https://github.com/user-attachments/assets/55cc7e0d-fefa-4368-9f12-f5c074418b41" />


# Conclusions

The analysis revealed several key insights about the fragrance store’s sales strategy. Year-round products consistently outperformed seasonal ones in both quantity sold and profitability. Perfume, especially the seasonal variety, was the weakest category, contributing significantly to overall losses. Additionally, higher discount rates were associated with lower profit margins and even reduced sales volume, suggesting that excessive discounting may hurt more than help.

Based on the data, I recommend eliminating seasonal perfume from the product lineup and closely monitoring the profitability of seasonal soap. A more conservative discount strategy may also drive stronger overall performance.

This project demonstrates how data-driven decisions can guide product and pricing strategies to improve business outcomes.
