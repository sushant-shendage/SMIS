# Store Management & Information System (SMIS) Database Documentation

## Table of Contents
- [Database Overview](#database-overview)
- [Table Structures](#table-structures)
  - [Stock Table](#stock-table)
  - [Product Table](#product-table)
  - [Purchase Table](#purchase-table)
  - [Customer Table](#customer-table)
  - [Sales Table](#sales-table)
- [Data Insertion Queries](#data-insertion-queries)
- [Business Queries](#business-queries)

---

## Database Overview
The **Store Management & Information System (SMIS)** database is designed to track and manage stock items in a shop. It helps customers with decision-making based on product availability and features.

---

## Table Structures

### 1. Stock Table
```sql
CREATE TABLE stock (
  stock_id VARCHAR(40) NOT NULL,
  model_id VARCHAR(40) NOT NULL,
  quantity INT CHECK (quantity >= 0),
  PRIMARY KEY (stock_id, model_id)
);
```
#### **Constraints:**
- `stock_id` and `model_id` form a composite primary key.
- `quantity` must be zero or a positive integer.

---

### 2. Product Table
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
```

---

### 3. Purchase Table
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
```

---

### 4. Customer Table
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

---

### 5. Sales Table
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
```

---

## Data Insertion Queries

### Insert Data into `stock` Table
```sql
INSERT INTO stock (stock_id, model_id, quantity) VALUES
('LAPTOP', 'MDL001', 50),
('LAPTOP', 'MDL002', 30),
('SMARTPHONE', 'MDL003', 100),
('SMARTPHONE', 'MDL004', 80),
('TABLET', 'MDL005', 60);
```

### Insert Data into `product` Table
```sql
INSERT INTO product (model_id, stock_id, brand, price, ram, rom, os, processor) VALUES
('MDL001', 'LAPTOP', 'Dell', '75000', 16, 512, 'Windows', 'Intel i7'),
('MDL002', 'LAPTOP', 'HP', '70000', 16, 256, 'Windows', 'Intel i5'),
('MDL003', 'SMARTPHONE', 'Samsung', '53000', 8, 128, 'Android', 'Snapdragon 888'),
('MDL004', 'SMARTPHONE', 'Apple', '118000', 6, 256, 'iOS', 'A15 Bionic'),
('MDL005', 'TABLET', 'Lenovo', '39000', 6, 128, 'Android', 'MediaTek P22');
```

### Insert Data into `purchase` Table
```sql
INSERT INTO purchase (purchaseDate, agentFullName, stock_id, model_id, amountPaid, purchaseTransactionId, purchaseInfoId) VALUES
('2025-02-01', 'John Doe', 'LAPTOP', 'MDL001', '75000', 'TRX001', 'PUR001'),
('2025-02-02', 'Alice Smith', 'LAPTOP', 'MDL002', '70000', 'TRX002', 'PUR002');
```

### Insert Data into `customer` Table
```sql
INSERT INTO customer (name, phone, area, city, state, address) VALUES
('Rahul Sharma', '9876543210', 'MG Road', 'Bangalore', 'Karnataka', '123, MG Road, Bangalore'),
('Priya Verma', '8765432109', 'Sector 10', 'Delhi', 'Delhi', 'A-10, Sector 10, Delhi');
```

### Insert Data into `sales` Table
```sql
INSERT INTO sales (sales_id, phone, stock_id, model_id, amountPaid, sales_date) VALUES
('SAL001', '9876543210', 'LAPTOP', 'MDL001', '78000', '2025-03-01'),
('SAL002', '8765432109', 'SMARTPHONE', 'MDL003', '55000', '2025-03-05');
```

---

## Business Queries

### Total Sales Amount for 2025
```sql
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_sales_2025
FROM sales
WHERE YEAR(sales_date) = 2025;
```

### Total Purchase Amount for 2025
```sql
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_purchase_2025
FROM purchase
WHERE YEAR(purchaseDate) = 2025;
```

### Net Profit/Loss Calculation
```sql
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
```

---

## Conclusion
This documentation provides a detailed overview of **SMIS Database**, including table structures, constraints, data insertion, and key business queries.
