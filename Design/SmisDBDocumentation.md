-- ## SMIS Database Documentation
-- 
-- ### Database: smis_db
USE smis_db;

-- ### 1. Stock Table
-- This table maintains stock information, ensuring each stock entry is uniquely identified by stock_id and model_id.
-- Constraints:
-- - Primary Key: (stock_id, model_id) ensures unique stock entries.
-- - Quantity must be >= 0 (CHECK constraint).
CREATE TABLE stock (
  stock_id VARCHAR(40) NOT NULL,
  model_id VARCHAR(40) NOT NULL,
  quantity INT CHECK (quantity >= 0),
  PRIMARY KEY (stock_id, model_id)
);

-- ### 2. Product Table
-- Stores detailed product specifications.
-- Foreign Key Constraints:
-- - (stock_id, model_id) references stock table to ensure valid product entries.
CREATE TABLE product (
  model_id VARCHAR(70),
  stock_id VARCHAR(70),
  brand VARCHAR(70) NOT NULL,
  price VARCHAR(70),
  ram INT,
  rom INT,
  front_camera INT CHECK (front_camera > 0),
  rare_camera INT CHECK (rare_camera > 0),
  os VARCHAR(70),
  battery_backup INT CHECK (battery_backup > 0),
  processor VARCHAR(70),
  refresh_rate INT CHECK (refresh_rate > 0),
  brightness INT CHECK (brightness > 0),
  body VARCHAR(70),
  CONSTRAINT fk_constraint01 FOREIGN KEY (stock_id, model_id) 
  REFERENCES stock(stock_id, model_id)
);

-- ### 3. Purchase Table
-- Keeps track of product purchases.
-- Foreign Key Constraints:
-- - (stock_id, model_id) references product table to ensure valid purchases.
CREATE TABLE purchase (
  purchaseDate DATE NOT NULL,
  agentFullName VARCHAR(100) NOT NULL,
  stock_id VARCHAR(70) NOT NULL,
  model_id VARCHAR(70) NOT NULL,
  amountPaid VARCHAR(100) NOT NULL,
  purchaseTransactionId VARCHAR(50) UNIQUE NOT NULL,
  purchaseInfoId VARCHAR(50) PRIMARY KEY,
  CONSTRAINT fk_purchase_product FOREIGN KEY (stock_id, model_id) 
  REFERENCES product(stock_id, model_id)
);

-- ### 4. Customer Table
-- Stores customer details, ensuring phone numbers are unique.
CREATE TABLE customer (
  name VARCHAR(100) NOT NULL,
  phone VARCHAR(15) PRIMARY KEY,
  area VARCHAR(100) NOT NULL,
  city VARCHAR(100) NOT NULL,
  state VARCHAR(100) NOT NULL,
  address VARCHAR(100) NOT NULL
);

-- ### 5. Sales Table
-- Tracks product sales, linking them to customers via phone numbers.
-- Foreign Key Constraints:
-- - phone references customer(phone) ensuring valid sales transactions.
CREATE TABLE sales (
  sales_id VARCHAR(50) PRIMARY KEY,
  phone VARCHAR(15),
  stock_id VARCHAR(70),
  model_id VARCHAR(70),
  amountPaid VARCHAR(100) NOT NULL,
  sales_date DATE NOT NULL,
  CONSTRAINT fk_sales_customer FOREIGN KEY (phone) 
  REFERENCES customer(phone)
);

-- ### Data Insertion Queries
INSERT INTO stock VALUES ('SMARTPHONE', 'M12345', 50);
INSERT INTO stock VALUES ('LAPTOP', 'L67890', 30);
INSERT INTO stock VALUES ('TABLET', 'T11223', 20);

INSERT INTO product VALUES ('M12345', 'SMARTPHONE', 'Samsung', '48000', 8, 128, 32, 64, 'Android', 5000, 'Snapdragon', 120, 800, 'Aluminium');
INSERT INTO product VALUES ('L67890', 'LAPTOP', 'Dell', '75000', 16, 512, NULL, NULL, 'Windows', 6000, 'Intel i7', 144, 1000, 'Metal');
INSERT INTO product VALUES ('T11223', 'TABLET', 'Apple', '30000', 4, 64, 12, 16, 'iOS', 4500, 'A14 Bionic', 90, 700, 'Glass');

-- ### Business Queries & Use Cases

-- #### Total Sales Amount for 2025
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_sales_2025
FROM sales
WHERE YEAR(sales_date) = 2025;

-- #### Total Purchase Amount for 2025
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_purchase_2025
FROM purchase
WHERE YEAR(purchaseDate) = 2025;

-- #### Net Profit/Loss Calculation for 2025
SELECT 
    ( 
        (SELECT COALESCE(SUM(CAST(amountPaid AS DECIMAL(10,2))), 0) 
         FROM sales 
         WHERE YEAR(sales_date) = 2025) 
        - 
        (SELECT COALESCE(SUM(CAST(amountPaid AS DECIMAL(10,2))), 0) 
         FROM purchase 
         WHERE YEAR(purchaseDate) = 2025) 
    ) AS net_profit_loss;

-- #### Stock-wise Total Sales & Total Purchase Amount
SELECT 
    p.stock_id, 
    SUM(CAST(s.amountPaid AS DECIMAL(10,2))) AS total_sales_amount,
    SUM(CAST(p.amountPaid AS DECIMAL(10,2))) AS total_purchase_amount
FROM purchase p
LEFT JOIN sales s ON p.stock_id = s.stock_id
GROUP BY p.stock_id;

-- #### Most Sold Product Model ID in 2025
SELECT p.brand
FROM sales s
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id
WHERE YEAR(s.sales_date) = 2025
GROUP BY p.brand
ORDER BY COUNT(*) DESC
LIMIT 1;

-- #### Least Sold Product for 2025
SELECT p.stock_id  
FROM sales s  
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id  
WHERE YEAR(s.sales_date) = 2025  
GROUP BY p.stock_id  
ORDER BY COUNT(*) ASC  
LIMIT 1;

-- #### Available Smartphones with 8GB RAM & Price < 50K
SELECT *  
FROM product  
WHERE stock_id = 'SMARTPHONE'  
AND ram = 8  
AND price < 50000;

-- #### Stock ID-wise Initial and Remaining Stock in 2025
SELECT 
    s.stock_id,  
    SUM(s.quantity) AS initial_quantity_2025,  
    SUM(s.quantity) - IFNULL(SUM(sa.sold_quantity), 0) AS remaining_quantity  
FROM stock s  
LEFT JOIN (  
    SELECT stock_id, SUM(quantity) AS sold_quantity  
    FROM sales  
    WHERE YEAR(sales_date) = 2025  
    GROUP BY stock_id  
) sa  
ON s.stock_id = sa.stock_id  
GROUP BY s.stock_id  
ORDER BY remaining_quantity DESC;
