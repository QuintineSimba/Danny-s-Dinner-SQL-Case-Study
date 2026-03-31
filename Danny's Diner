CREATE SCHEMA dannys_diner;
USE dannys_diner;

CREATE TABLE sales (
customer_id VARCHAR(1),
order_date DATE,
product_id INTEGER);

INSERT INTO sales
  (customer_id, order_date, product_id)
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');
  
CREATE TABLE menu (
product_id INTEGER,
product_name VARCHAR(5),
price INTEGER);

INSERT INTO menu
  (product_id, product_name, price)
VALUES
  ('1', 'sushi', '10'),
  ('2', 'curry', '15'),
  ('3', 'ramen', '12');

CREATE TABLE members (
  customer_id VARCHAR(1),
  join_date DATE);
  
INSERT INTO members
  (customer_id, join_date)
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
  
-- Row counts
SELECT COUNT(*) FROM sales;    -- expect 15
SELECT COUNT(*) FROM menu;     -- expect 3
SELECT COUNT(*) FROM members;  -- expect 2

-- Check for NULLs in key columns
SELECT * FROM sales WHERE customer_id IS NULL OR order_date IS NULL;

-- See all distinct customers
SELECT DISTINCT customer_id FROM sales;

#1 What is the total amount each customer spent at the restaurant?

SELECT s.customer_id, SUM(m.price) AS total_spent
FROM sales s
JOIN menu m ON s.product_id = m.product_id
GROUP BY s.customer_id;

#2 How many days has each customer visited the restaurant?

SELECT customer_id, COUNT(distinct order_date) AS Days_visited
FROM sales
GROUP BY customer_id;

#3 What was the first item from the menu purchased by each customer?

SELECT t.customer_id, t.order_date, m.product_name
FROM (
    SELECT 
        customer_id,
        product_id,
        order_date,
        RANK() OVER (
            PARTITION BY customer_id 
            ORDER BY order_date ASC
        ) AS purchase_rank
    FROM sales
) t
JOIN menu m ON t.product_id = m.product_id
WHERE purchase_rank = 1;

#4 What is the most purchased item on the menu and how many times was it purchased by all customers?

SELECT m.product_name, COUNT(s.order_date) AS Times_purchased
FROM menu m
JOIN sales s ON m.product_id = s.product_id
GROUP BY m.product_name
ORDER BY Times_purchased DESC
LIMIT 1;

 #5. Which item was the most popular for each customer?

SELECT customer_id, product_name, Times_purchased
FROM (
    SELECT 
        s.customer_id,
        m.product_name,
        COUNT(s.product_id) AS Times_purchased,
        RANK() OVER (
            PARTITION BY s.customer_id 
            ORDER BY COUNT(s.product_id) DESC
        ) AS purchase_rank
    FROM sales s
    JOIN menu m ON s.product_id = m.product_id
    GROUP BY s.customer_id, m.product_name
) t
WHERE purchase_rank = 1;

#6. Which item was purchased first by the customer after they became a member?

SELECT customer_id, product_name
FROM (
    SELECT 
        s.customer_id,
        m.product_name,
        RANK() OVER (
            PARTITION BY s.customer_id 
            ORDER BY s.order_date ASC
        ) AS rnk
    FROM sales s
    JOIN menu m ON s.product_id = m.product_id
    JOIN members mem ON s.customer_id = mem.customer_id
    WHERE s.order_date >= mem.join_date
) t
WHERE rnk = 1;

#7 Which item was purchased just before the customer became a member?

SELECT customer_id, product_name
FROM (
    SELECT 
        s.customer_id,
        m.product_name,
        RANK() OVER (
            PARTITION BY s.customer_id 
            ORDER BY s.order_date DESC
        ) AS rnk
    FROM sales s
    JOIN menu m ON s.product_id = m.product_id
    JOIN members mem ON s.customer_id = mem.customer_id
    WHERE s.order_date < mem.join_date
) t
WHERE rnk = 1;

#8 What is the total items and amount spent for each member before they became a member?

SELECT s.customer_id, COUNT(s.product_id) AS total_items,
SUM(m.price) AS total_spent
FROM sales s
JOIN menu m ON s.product_id = m.product_id
JOIN members mem ON s.customer_id = mem.customer_id
WHERE s.order_date < mem.join_date
GROUP BY s.customer_id;

#9 If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

SELECT s.customer_id,
SUM(
    CASE 
        WHEN m.product_name = 'sushi' THEN m.price * 20
        ELSE m.price * 10
    END
) AS total_points
FROM sales s
JOIN menu m ON s.product_id = m.product_id
GROUP BY s.customer_id;

#10 In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

SELECT s.customer_id,
SUM(
    CASE 
        WHEN s.order_date BETWEEN mem.join_date AND mem.join_date + INTERVAL 6 DAY THEN m.price * 20
        WHEN m.product_name = 'sushi' THEN m.price * 20
        ELSE m.price * 10
    END
) AS total_points
FROM sales s
JOIN menu m ON s.product_id = m.product_id
JOIN members mem ON s.customer_id = mem.customer_id
WHERE s.order_date <= '2021-01-31'
GROUP BY s.customer_id;













