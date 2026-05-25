-- Create the database only if it does not exist
CREATE DATABASE IF NOT EXISTS ecommerce;

-- Select the database
DROP DATABASE ecommerce;

CREATE DATABASE ecommerce;

USE ecommerce;

---------------------------------------------------
-- Create customers table only if it does not exist
---------------------------------------------------
CREATE TABLE IF NOT EXISTS customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    address VARCHAR(255)
);


---------------------------------------------------
-- Create products table only if it does not exist
---------------------------------------------------
CREATE TABLE IF NOT EXISTS products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    description VARCHAR(255)
);

---------------------------------------------------
-- Create orders table only if it does not exist
---------------------------------------------------
CREATE TABLE IF NOT EXISTS orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10,2),
    
    FOREIGN KEY (customer_id)
    REFERENCES customers(id)
);

---------------------------------------------------
-- Insert sample data into customers table
---------------------------------------------------
INSERT ignore INTO customers (name, email, address)
VALUES
('John Doe', 'john@example.com', 'Chennai'),
('Alice Smith', 'alice@example.com', 'Bangalore'),
('Robert Brown', 'robert@example.com', 'Hyderabad');

---------------------------------------------------
-- Insert sample data into products table
---------------------------------------------------
INSERT INTO products (name, price, description)
VALUES
('Product A', 25.00, 'Electronic Item'),
('Product B', 35.00, 'Home Appliance'),
('Product C', 40.00, 'Office Product'),
('Product D', 60.00, 'Gaming Accessory');

---------------------------------------------------
-- Insert sample data into orders table
---------------------------------------------------
INSERT INTO orders (customer_id, order_date, total_amount)
VALUES
(1, '2026-05-01', 120.00),
(2, '2026-05-10', 200.00),
(1, '2026-05-15', 80.00),
(3, '2026-04-20', 170.00);

---------------------------------------------------
-- 1. Retrieve customers who placed orders 
--    in the last 30 days
---------------------------------------------------

SELECT DISTINCT c.*
FROM customers c
JOIN orders o
ON c.id = o.customer_id
WHERE o.order_date >= CURDATE() - INTERVAL 30 DAY;

---------------------------------------------------
-- 2. Get total amount of orders placed by each customer
---------------------------------------------------
SELECT c.name,
       SUM(o.total_amount) AS total_spent
FROM customers c
JOIN orders o
ON c.id = o.customer_id
GROUP BY c.name;

---------------------------------------------------
-- 3. Update the price of Product C to 45.00
---------------------------------------------------
SET SQL_SAFE_UPDATES = 0;

UPDATE products
SET price = 45.00
WHERE name = 'Product C';

SET SQL_SAFE_UPDATES = 1;
---------------------------------------------------
-- 4. Add a new column discount to products table
---------------------------------------------------
ALTER TABLE products
ADD discount DECIMAL(5,2) DEFAULT 0.00;

---------------------------------------------------
-- 5. Retrieve top 3 most expensive products
---------------------------------------------------
SELECT *
FROM products
ORDER BY price DESC
LIMIT 3;

---------------------------------------------------
-- 6. Create order_items table for normalization
---------------------------------------------------
CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    item_price DECIMAL(10,2),

    FOREIGN KEY (order_id)
    REFERENCES orders(id),

    FOREIGN KEY (product_id)
    REFERENCES products(id)
);


---------------------------------------------------
-- Insert sample data into order_items table
---------------------------------------------------
INSERT INTO order_items (order_id, product_id, quantity, item_price)
VALUES
(1, 1, 2, 25.00),
(1, 2, 2, 35.00),
(2, 1, 4, 25.00),
(3, 3, 1, 45.00),
(4, 4, 2, 60.00);

---------------------------------------------------
-- 7. Get names of customers who ordered Product A
---------------------------------------------------
SELECT DISTINCT c.name
FROM customers c
JOIN orders o
ON c.id = o.customer_id
JOIN order_items oi
ON o.id = oi.order_id
JOIN products p
ON oi.product_id = p.id
WHERE p.name = 'Product A';

---------------------------------------------------
-- 8. Join orders and customers tables to show
--    customer name and order date
---------------------------------------------------
SELECT c.name,
       o.order_date
FROM customers c
JOIN orders o
ON c.id = o.customer_id;

---------------------------------------------------
-- 9. Retrieve orders with total amount > 150
---------------------------------------------------
SELECT *
FROM orders
WHERE total_amount > 150.00;

---------------------------------------------------
-- 10. Retrieve average total of all orders
---------------------------------------------------
SELECT AVG(total_amount) AS average_order_total
FROM orders;







