# SQL Case Study 1: Tiny Shop Sales

View original link [here](https://d-i-motion.com/lessons/customer-orders-analysis/) by Kedeisha Bryan.

![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/d48d0b83-1864-491c-95d5-7e279f9b3d9e)

<details><summary>Creating databases and Tables:</summary>

```sql
CREATE TABLE customers (
    customer_id integer PRIMARY KEY,
    first_name varchar(100),
    last_name varchar(100),
    email varchar(100)
);

CREATE TABLE products (
    product_id integer PRIMARY KEY,
    product_name varchar(100),
    price decimal
);

CREATE TABLE orders (
    order_id integer PRIMARY KEY,
    customer_id integer,
    order_date date
);

CREATE TABLE order_items (
    order_id integer,
    product_id integer,
    quantity integer
);

INSERT INTO customers (customer_id, first_name, last_name, email) VALUES
(1, 'John', 'Doe', 'johndoe@email.com'),
(2, 'Jane', 'Smith', 'janesmith@email.com'),
(3, 'Bob', 'Johnson', 'bobjohnson@email.com'),
(4, 'Alice', 'Brown', 'alicebrown@email.com'),
(5, 'Charlie', 'Davis', 'charliedavis@email.com'),
(6, 'Eva', 'Fisher', 'evafisher@email.com'),
(7, 'George', 'Harris', 'georgeharris@email.com'),
(8, 'Ivy', 'Jones', 'ivyjones@email.com'),
(9, 'Kevin', 'Miller', 'kevinmiller@email.com'),
(10, 'Lily', 'Nelson', 'lilynelson@email.com'),
(11, 'Oliver', 'Patterson', 'oliverpatterson@email.com'),
(12, 'Quinn', 'Roberts', 'quinnroberts@email.com'),
(13, 'Sophia', 'Thomas', 'sophiathomas@email.com');

INSERT INTO products (product_id, product_name, price) VALUES
(1, 'Product A', 10.00),
(2, 'Product B', 15.00),
(3, 'Product C', 20.00),
(4, 'Product D', 25.00),
(5, 'Product E', 30.00),
(6, 'Product F', 35.00),
(7, 'Product G', 40.00),
(8, 'Product H', 45.00),
(9, 'Product I', 50.00),
(10, 'Product J', 55.00),
(11, 'Product K', 60.00),
(12, 'Product L', 65.00),
(13, 'Product M', 70.00);

INSERT INTO orders (order_id, customer_id, order_date) VALUES
(1, 1, '2023-05-01'),
(2, 2, '2023-05-02'),
(3, 3, '2023-05-03'),
(4, 1, '2023-05-04'),
(5, 2, '2023-05-05'),
(6, 3, '2023-05-06'),
(7, 4, '2023-05-07'),
(8, 5, '2023-05-08'),
(9, 6, '2023-05-09'),
(10, 7, '2023-05-10'),
(11, 8, '2023-05-11'),
(12, 9, '2023-05-12'),
(13, 10, '2023-05-13'),
(14, 11, '2023-05-14'),
(15, 12, '2023-05-15'),
(16, 13, '2023-05-16');

INSERT INTO order_items (order_id, product_id, quantity) VALUES
(1, 1, 2),
(1, 2, 1),
(2, 2, 1),
(2, 3, 3),
(3, 1, 1),
(3, 3, 2),
(4, 2, 4),
(4, 3, 1),
(5, 1, 1),
(5, 3, 2),
(6, 2, 3),
(6, 1, 1),
(7, 4, 1),
(7, 5, 2),
(8, 6, 3),
(8, 7, 1),
(9, 8, 2),
(9, 9, 1),
(10, 10, 3),
(10, 11, 2),
(11, 12, 1),
(11, 13, 3),
(12, 4, 2),
(12, 5, 1),
(13, 6, 3),
(13, 7, 2),
(14, 8, 1),
(14, 9, 2),
(15, 10, 3),
(15, 11, 1),
(16, 12, 2),
(16, 13, 3);
```

</details>


SQL interactive playground >> [View on DB Fiddle](https://www.db-fiddle.com/f/5NT4w4rBa1cvFayg2CxUjr/4)

---
### Question 1: Which product has the highest price? Only return a single row.
<details><summary>Solution:</summary>
  
  ````sql
  SELECT *
  FROM products
  ORDER BY 3 DESC
  LIMIT 1;
  ````

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/fa4b2eda-6ca8-430e-b5e5-ae762ff7875a)

</details>

### Question 2: Which customer has made the most orders?
<details><summary>Solution:</summary>
  
  ````sql
  SELECT customer_id, first_name, last_name, order_count
  FROM (
      SELECT c.customer_id, c.first_name, c.last_name, COUNT(o.order_id) AS order_count, DENSE_RANK() OVER (ORDER BY COUNT(o.order_id) DESC) AS rank
      FROM customers c
      JOIN orders o ON c.customer_id = o.customer_id
      GROUP BY c.customer_id, c.first_name, c.last_name
  ) AS subquery
  WHERE rank = 1;
  ````

</details>

<details><summary>Output:</summary>
  
  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/7ae175f7-5a15-4e62-8994-5bdfd5a4925e)

</details>

### Question 3: What’s the total revenue per product?
<details><summary>Solution:</summary>
  
  ````sql
  SELECT p.product_id, p.product_name, SUM(quantity*price) as revenue 
  FROM order_items o
  INNER JOIN products p ON o.product_id = p.product_id
  GROUP BY 1, 2
  ORDER BY 2;
  ````

</details>

<details><summary>Alternate Solution (using CTE):</summary>

  ```sql  
  WITH product_revenue AS (
      SELECT o.product_id, SUM(o.quantity * p.price) AS revenue
      FROM order_items o
      INNER JOIN products p ON o.product_id = p.product_id
      GROUP BY o.product_id
  )
  SELECT p.product_id, p.product_name, revenue
  FROM products p
  INNER JOIN product_revenue pr ON p.product_id = pr.product_id;
  ```
</details>

<details><summary>Output:</summary>
  
  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/d5fbe469-4984-4b9a-9562-c46702b69585)

</details>


### Question 4: Find the day with the highest revenue.
<details><summary>Solution:</summary>

  ```sql
  SELECT o.order_date, SUM(quantity*price) as revenue
  FROM order_items oi
  INNER JOIN products p ON oi.product_id = p.product_id
  INNER JOIN orders o ON oi.order_id = o.order_id
  GROUP BY 1
  ORDER BY 2 DESC
  LIMIT 1;
  ```

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/cddb6582-4ed9-4757-81cc-058e8bad6a5b)
  
</details>




### Question 5: Find the first order (by date) for each customer.
<details><summary>Solution:</summary>

  ```sql
  SELECT c.customer_id, c.first_name, c.last_name, MIN(o.order_date) AS first_order_date
  FROM customers c
  JOIN orders o ON c.customer_id = o.customer_id
  GROUP BY 1,2,3
  ORDER BY first_order_date;
  ```

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/69a17895-f2d6-4637-95e8-942380f82c1b)

</details>

### Question 6: Find the top 3 customers who have ordered the most distinct products
<details><summary>Solution:</summary>

  ```sql
  SELECT orders.customer_id, c.first_name, c.last_name, COUNT(DISTINCT order_items.product_id) AS distinct_product_nos
  FROM orders
  INNER JOIN order_items ON orders.order_id = order_items.order_id
  INNER JOIN customers c ON orders.customer_id = c.customer_id
  GROUP BY 1,2,3
  ORDER BY distinct_product_nos DESC
  LIMIT 3
  ```

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/ff417736-43e0-423e-b1e0-2837f3bf85b6)

</details>


### Question 7: Which product has been bought the least in terms of quantity?
<details><summary>Solution:</summary>

  ```sql
  SELECT product_id, product_name, total_qty
  FROM (
  	SELECT p.product_id, p.product_name, SUM(oi.quantity) as total_qty, DENSE_RANK() OVER (ORDER BY SUM(oi.quantity) ASC) AS rank
  	FROM products p
  	INNER JOIN order_items oi ON p.product_id = oi.product_id
  	GROUP BY 1,2) product_qty_byrank
  WHERE rank = 1
  ORDER BY 1
  ```

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/1d4e870c-6f84-4670-b9ba-a01fdc5f968e)

</details>


### Question 8: What is the median order total?
<details><summary>Solution (using CTE and WITHIN_GORUP()):</summary>

  ```sql
  WITH order_totals AS (
    SELECT o.order_id, SUM(p.price * oi.quantity) AS total_order_amount
    FROM orders o
    JOIN order_items oi ON  o.order_id = oi.order_id
    JOIN products p ON oi.product_id = p.product_id
    GROUP BY o.order_id
  )
  SELECT
    percentile_disc(0.5) WITHIN GROUP (ORDER BY total_order_amount) AS total_median_order
  FROM order_totals;
  ```

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/a3502819-88a7-404d-90ef-67ec68d4bd7a)

</details>


### Question 9: For each order, determine if it was ‘Expensive’ (total over 300), ‘Affordable’ (total over 100), or ‘Cheap’.
<details><summary>Solution (using CTE):</summary>

  ```sql
  WITH order_totals AS (
    SELECT o.order_id, SUM(p.price * oi.quantity) AS total_order_amount
    FROM orders o
    JOIN order_items oi ON o.order_id = oi.order_id
    JOIN products p ON oi.product_id = p.product_id
    GROUP BY o.order_id
  )
  
  SELECT order_id, total_order_amount,
    CASE
      WHEN total_order_amount > 300 THEN 'Expensive'
      WHEN total_order_amount > 100 THEN 'Affordable'
      ELSE 'Cheap'
    END AS category
  FROM order_totals
  ORDER BY 1
  ```

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/1ab186d2-6607-43f3-a3d8-275999b7301c)

</details>


### Question 10: Find customers who have ordered the product with the highest price.
<details><summary>Solution (using CTE):</summary>

  ```sql
  WITH product_highestprice AS (
  SELECT *
  FROM products
  ORDER BY price DESC
  LIMIT 1
  )
  
  SELECT c.customer_id, c.first_name, c.last_name
  FROM customers c
  INNER JOIN orders o ON c.customer_id = o.customer_id
  INNER JOIN order_items oi ON o.order_id = oi.order_id
  INNER JOIN product_highestprice php ON oi.product_id = php.product_id
  ```
</details>

<details><summary>Alternate Solution:</summary>

  ```sql
  SELECT c.customer_id, c.first_name, c.last_name
  FROM customers c
    JOIN orders o ON c.customer_id = o.customer_id
    JOIN order_items oi ON o.order_id = oi.order_id
    JOIN products p ON oi.product_id = p.product_id
  WHERE p.price = (
  	SELECT MAX(price)
      FROM products
  	);
  ```
</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Tiny_Shop_Sales-SQL/assets/114795923/c20c8004-74a3-4fb2-b786-cde0dd4d70fb)

</details>
