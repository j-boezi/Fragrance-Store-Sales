# Fragrance-Store-Sales

```
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

What are the categories and subcategories of the products?
```
SELECT category, subcategory
FROM sales
GROUP BY category, subcategory
ORDER by category ASC, subcategory ASC;
```
<img width="302" height="187" alt="image" src="https://github.com/user-attachments/assets/b105a0e4-2921-4050-8b75-036692f7ced8" />

What quantity did each product sell?
```
SELECT category, subcategory, SUM(quantity) AS products_sold
FROM sales
GROUP BY category, subcategory
ORDER BY products_sold DESC;
```
<img width="410" height="191" alt="image" src="https://github.com/user-attachments/assets/4d6f89af-d3e4-4b07-98b0-5ed3d5c4801d" />

How much profit did each product make?
```
SELECT category, subcategory, SUM(profit) AS total_profit
FROM sales
GROUP BY category, subcategory
ORDER BY total_profit DESC;
```
<img width="390" height="187" alt="image" src="https://github.com/user-attachments/assets/f4a73669-8eed-48dc-a34f-13627dd6c5f3" />

Did the Year-Round or Seasonal subcategory sell more products?
```
SELECT subcategory, SUM(quantity) AS products_sold
FROM sales
GROUP BY subcategory
ORDER BY products_sold DESC;
```
<img width="260" height="91" alt="image" src="https://github.com/user-attachments/assets/a8138075-d194-43f8-9d67-cbc2259a657d" />

Did the Year-Round or Seasonal subcategory make more profit?
```
SELECT subcategory, SUM(profit) AS subcategory_profit
FROM sales
GROUP BY subcategory
ORDER BY subcategory_profit DESC;
```
<img width="283" height="88" alt="image" src="https://github.com/user-attachments/assets/928acdd4-d64e-4c8e-b582-8f067fc39459" />

How much more profit did Year-Round products make than Seasonal products?
```
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

Rank the categories from high to lowest profit.
```
SELECT category, SUM(profit) AS total_profit, RANK() OVER
	(
	ORDER BY SUM(profit) DESC
	)
	AS category_profit_rank
FROM sales
GROUP BY category;
```
<img width="383" height="113" alt="image" src="https://github.com/user-attachments/assets/7be6a7fa-c69d-4468-93e4-f21a37383127" />

Which product sold the highest quantity?
```
SELECT category, subcategory, SUM(quantity) as products_sold
FROM sales
GROUP BY category, subcategory
ORDER BY products_sold DESC
LIMIT 1;
```
<img width="411" height="64" alt="image" src="https://github.com/user-attachments/assets/6057e4de-faa3-4107-8c59-8461eb605331" />

Which products had days where they made less than $0 in profit and how many instances were there per product?
```
SELECT category, subcategory, COUNT(profit) AS instances_of_negative_profit
FROM sales
WHERE profit < 0
GROUP BY category, subcategory
ORDER BY instances_of_negative_profit DESC;
```
<img width="486" height="86" alt="image" src="https://github.com/user-attachments/assets/707c78c5-93c6-4c7f-b4c3-5f5d3b33b57d" />

How much money did Seasonal Perfume net on the days it made less than $0?
```
SELECT SUM(profit) as negative_profit
FROM sales
WHERE category = 'Perfume'
AND subcategory = 'Seasonal'
AND profit < 0;
```
<img width="113" height="63" alt="image" src="https://github.com/user-attachments/assets/9e9f6bf3-1fcb-47dc-82d3-64dc8dd7300d" />

How did each product perform on a scale from poor to exceptional?
```
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

How much profit did each discount type make?
```
SELECT category, subcategory, SUM(quantity) AS items_sold
FROM sales
GROUP BY category, subcategory
ORDER BY items_sold DESC;
```
<img width="391" height="187" alt="image" src="https://github.com/user-attachments/assets/6b7dc60d-e3cb-4dd7-9d54-ceb2693780ff" />

Did a higher discount translate to a higher quantity sold?
```
SELECT discount*100 || '%' AS discount, SUM(quantity) AS product_sold
FROM sales
GROUP BY discount;
```
<img width="177" height="86" alt="image" src="https://github.com/user-attachments/assets/55cc7e0d-fefa-4368-9f12-f5c074418b41" />

Discussion
After analyzing the Fragrance Store dataset, it became clear that Year-Round products sold better than their Seasonal counterparts, perfume was the worst performer out of all categories by far, seasonal perfume accounted for hundreds of dollars in negative profit, and a higher discount rate created lower profit margins and smaller quantity sold. Based upon this data, I believe lower discount rates lead to better profit and sales. Additionally, I recommend that we no longer continue to sell seasonal perfume and keep an eye on the profit margin of year-round perfume.
