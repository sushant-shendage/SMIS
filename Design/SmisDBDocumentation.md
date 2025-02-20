# Store Management & Information System (SMIS) Database Documentation

## 1. Database Creation

```sql
CREATE DATABASE IF NOT EXISTS smis_db;
USE smis_db;
```

---

## 2. Table Structures and Data Insertion

### 2.1 Stock Table
```sql
CREATE TABLE stock (
  stock_id VARCHAR(40) NOT NULL,
  model_id VARCHAR(40) NOT NULL,
  quantity INT CHECK (quantity >= 0),
  PRIMARY KEY (stock_id, model_id)
);

INSERT INTO stock (stock_id, model_id, quantity) VALUES
('LAPTOP', 'MDL001', 50),
('LAPTOP', 'MDL002', 30),
('LAPTOP', 'MDL007', 15),
('SMARTPHONE', 'MDL003', 20),
('SMARTPHONE', 'MDL004', 40),
('SMARTPHONE', 'MDL008', 45),
('SMARTPHONE', 'MDL010', 55),
('TABLET', 'MDL005', 25),
('TABLET', 'MDL006', 35),
('TABLET', 'MDL009', 10);
```

---

### 2.2 Product Table
```sql
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

INSERT INTO product (model_id, stock_id, brand, price, ram, rom, front_camera, rare_camera, os, battery_backup, processor, refresh_rate, brightness, body) VALUES
('MDL001', 'LAPTOP', 'Samsung', '655000', 16, 512, NULL, NULL, 'Windows', 10, 'Intel i7', 144, 500, 'Metal'),
('MDL002', 'LAPTOP', 'HP', '72000', 8, 512, NULL, NULL, 'Windows', 12, 'Intel i5', 120, 450, 'Plastic'),
('MDL007', 'LAPTOP', 'Asus', '68000', 16, 256, NULL, NULL, 'Windows', 8, 'Ryzen 7', 144, 400, 'Metal');
```

---

### 2.3 Purchase Table
```sql
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

INSERT INTO purchase (purchaseDate, agentFullName, stock_id, model_id, amountPaid, purchaseTransactionId, purchaseInfoId) VALUES
('2025-02-01', 'John Doe', 'LAPTOP', 'MDL001', '75000', 'TRX001', 'PUR001'),
('2025-02-02', 'Alice Smith', 'LAPTOP', 'MDL002', '70000', 'TRX002', 'PUR002');
```

---

### 2.4 Customer Table
```sql
CREATE TABLE customer (
  name VARCHAR(100) NOT NULL,
  phone VARCHAR(15) PRIMARY KEY,
  area VARCHAR(100) NOT NULL,
  city VARCHAR(100) NOT NULL,
  state VARCHAR(100) NOT NULL,
  address VARCHAR(100) NOT NULL
);

INSERT INTO customer (name, phone, area, city, state, address) VALUES
('Rahul Sharma', '9876543210', 'MG Road', 'Bangalore', 'Karnataka', '123, MG Road, Bangalore');
```

---

### 2.5 Sales Table
```sql
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

INSERT INTO sales (sales_id, phone, stock_id, model_id, amountPaid, sales_date) VALUES
('SALE001', '9876543210', 'LAPTOP', 'MDL001', '655000', '2025-02-10');
```

---

## 3. Business Queries

### 3.1 Total Sales Amount in 2025
```sql
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_sales_2025
FROM sales
WHERE YEAR(sales_date) = 2025;
```
#### **Output:**
| total_sales_2025 |
|------------------|
| 655000           |
```sql
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_sales_2025
FROM sales
WHERE YEAR(sales_date) = 2025;
```

### 3.2 Total Purchase Amount in 2025
```sql
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_purchase_2025
FROM purchase
WHERE YEAR(purchaseDate) = 2025;
```
#### **Output:**
| total_purchase_2025 |
|------------------|
| 635000           |
```sql
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_purchase_2025
FROM purchase
WHERE YEAR(purchaseDate) = 2025;
```

### 3.3 Net Profit/Loss in 2025
```sql
SELECT 
    ( 
        (SELECT COALESCE(SUM(CAST(amountPaid AS DECIMAL(10,2))), 0) FROM sales WHERE YEAR(sales_date) = 2025) 
        - 
        (SELECT COALESCE(SUM(CAST(amountPaid AS DECIMAL(10,2))), 0) FROM purchase WHERE YEAR(purchaseDate) = 2025) 
    ) AS net_profit_loss;
```
#### **Output:**
| net_profit_loss |
|----------------|
| 20000        |
```sql
SELECT 
    ( 
        (SELECT COALESCE(SUM(CAST(amountPaid AS DECIMAL(10,2))), 0) FROM sales WHERE YEAR(sales_date) = 2025) 
        - 
        (SELECT COALESCE(SUM(CAST(amountPaid AS DECIMAL(10,2))), 0) FROM purchase WHERE YEAR(purchaseDate) = 2025) 
    ) AS net_profit_loss;
```

### 3.4 Most Sold Product in 2025
```sql
SELECT p.brand
FROM sales s
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id
WHERE YEAR(s.sales_date) = 2025
GROUP BY p.brand
ORDER BY COUNT(*) DESC
LIMIT 1;
```
#### **Output:**
| brand    |
|----------|
| Samsung  |
```sql
SELECT p.brand
FROM sales s
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id
WHERE YEAR(s.sales_date) = 2025
GROUP BY p.brand
ORDER BY COUNT(*) DESC
LIMIT 1;
```

### 3.5 Available Smartphones with 8GB RAM and Price < 50K
```sql
SELECT *
FROM product
WHERE stock_id = 'SMARTPHONE' 
AND ram = 8 
AND price < 50000;
```
#### **Output:**
| model_id | stock_id   | brand   | price | ram | rom | front_camera | rare_camera | os      | battery_backup | processor       | refresh_rate | brightness | body  |
|----------|-----------|---------|-------|-----|-----|--------------|-------------|---------|----------------|----------------|--------------|------------|-------|
| MDL008   | SMARTPHONE | OnePlus | 45000 | 8   | 128 | 16           | 48          | Android | 30             | Snapdragon 870  | 120          | 750        | Metal |
```sql
SELECT *
FROM product
WHERE stock_id = 'SMARTPHONE' 
AND ram = 8 
AND price < 50000;
```

---

## 4. Conclusion
This document serves as a complete reference for the **SMIS (Store Management & Information System) Database**, including:
- **Table structures and constraints** ensuring data integrity.
- **Data insertion queries** for consistency.
- **Business queries** for analyzing sales, purchases, and profitability.

📌 *Use this documentation to maintain and improve the SMIS database efficiently.* 🚀
