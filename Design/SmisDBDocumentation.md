# SMIS Database Documentation

## 1. Introduction
The **Store Management & Information System (SMIS)** database is designed to track and manage stock, sales, purchases, and customer details. This document provides:
- **Table structures with constraints**
- **Data insertion queries**
- **Business queries** for insights
- **Expected outputs** for validation

---
## 2. Database Selection
```sql
USE smis_db;
```

---
## 3. Table Structures

### 3.1 Stock Table
```sql
CREATE TABLE stock (
  stock_id VARCHAR(40) NOT NULL,
  model_id VARCHAR(40) NOT NULL,
  quantity INT CHECK (quantity >= 0),
  PRIMARY KEY (stock_id, model_id)
);
```
**Explanation:**
- `stock_id`: Represents product category (**SMARTPHONE, TABLET, LAPTOP**)
- `model_id`: Unique model identifier
- `quantity`: Ensures stock cannot be negative

### 3.2 Product Table
```sql
CREATE TABLE product (
  model_id VARCHAR(70),
  stock_id VARCHAR(70),
  brand VARCHAR(70) NOT NULL,          
  price DECIMAL(10,2),     
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
  CONSTRAINT fk_product_stock FOREIGN KEY (stock_id, model_id) 
  REFERENCES stock(stock_id, model_id)
);
```
**Explanation:**
- Defines various product attributes like **RAM, storage, OS, battery, camera**.
- **Foreign key** ensures every product entry has a valid stock reference.

### 3.3 Purchase Table
```sql
CREATE TABLE purchase (
  purchaseDate DATE NOT NULL,
  agentFullName VARCHAR(100) NOT NULL,
  stock_id VARCHAR(70) NOT NULL,
  model_id VARCHAR(70) NOT NULL,
  amountPaid DECIMAL(10,2) NOT NULL,
  purchaseTransactionId VARCHAR(50) UNIQUE NOT NULL,
  purchaseInfoId VARCHAR(50) PRIMARY KEY,
  CONSTRAINT fk_purchase_product FOREIGN KEY (stock_id, model_id) 
  REFERENCES product(stock_id, model_id)
);
```
**Explanation:**
- Records **purchase transactions**, tracking suppliers, date, and amount paid.

### 3.4 Sales Table
```sql
CREATE TABLE sales (
  sales_id VARCHAR(50) PRIMARY KEY,
  phone VARCHAR(15),
  stock_id VARCHAR(70),
  model_id VARCHAR(70),
  amountPaid DECIMAL(10,2) NOT NULL,
  sales_date DATE NOT NULL,
  CONSTRAINT fk_sales_customer FOREIGN KEY (phone) 
  REFERENCES customer(phone)
);
```
**Explanation:**
- Stores **sales transactions** for products sold to customers.
- Links to `customer.phone` ensuring valid customers.

### 3.5 Customer Table
```sql
CREATE TABLE customer (
  name VARCHAR(100) NOT NULL,
  phone VARCHAR(15) PRIMARY KEY,
  area VARCHAR(100) NOT NULL,
  city VARCHAR(100) NOT NULL,
  state VARCHAR(100) NOT NULL,
  address VARCHAR(100) NOT NULL
);
```
**Explanation:**
- Stores **customer details** with a unique phone number.

---
## 4. Data Insertion Queries

### 4.1 Insert Data into Stock Table
```sql
INSERT INTO stock VALUES
  ('SMARTPHONE', 'S12345', 100),
  ('LAPTOP', 'L54321', 50),
  ('TABLET', 'T98765', 75);
```

### 4.2 Insert Data into Product Table
```sql
INSERT INTO product VALUES
  ('S12345', 'SMARTPHONE', 'Samsung', 45000, 8, 128, 32, 64, 'Android', 5000, 'Snapdragon 888', 120, 700, 'Metal'),
  ('L54321', 'LAPTOP', 'Dell', 85000, 16, 512, NULL, NULL, 'Windows', 7000, 'Intel i7', 144, 400, 'Aluminum'),
  ('T98765', 'TABLET', 'Apple', 60000, 8, 256, 12, 12, 'iOS', 10000, 'A15 Bionic', 60, 600, 'Glass');
```

### 4.3 Insert Data into Sales Table
```sql
INSERT INTO sales VALUES
  ('SALE001', '9876543210', 'SMARTPHONE', 'S12345', 45000, '2025-02-10'),
  ('SALE002', '8765432109', 'LAPTOP', 'L54321', 85000, '2025-02-12'),
  ('SALE003', '7654321098', 'TABLET', 'T98765', 60000, '2025-02-18');
```

---
## 5. Business Queries

### 5.1 Total Sales Amount in 2025
```sql
SELECT SUM(amountPaid) AS total_sales_2025
FROM sales
WHERE YEAR(sales_date) = 2025;
```

### 5.2 Total Purchase Amount in 2025
```sql
SELECT SUM(amountPaid) AS total_purchase_2025
FROM purchase
WHERE YEAR(purchaseDate) = 2025;
```

### 5.3 Net Profit/Loss in 2025
```sql
SELECT 
    ( 
        (SELECT COALESCE(SUM(amountPaid), 0) FROM sales WHERE YEAR(sales_date) = 2025) 
        - 
        (SELECT COALESCE(SUM(amountPaid), 0) FROM purchase WHERE YEAR(purchaseDate) = 2025) 
    ) AS net_profit_loss;
```

### 5.4 Most Sold Product in 2025
```sql
SELECT p.brand
FROM sales s
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id
WHERE YEAR(s.sales_date) = 2025
GROUP BY p.brand
ORDER BY COUNT(*) DESC
LIMIT 1;
```

### 5.5 Stock-wise Remaining Quantity
```sql
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
```

---
## 6. Conclusion
This database and queries provide insights into **sales performance, stock tracking, and profit/loss** to help manage inventory efficiently. 🚀
