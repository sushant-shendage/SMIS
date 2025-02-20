# SMIS Database Documentation

## 1. Database Overview
This document provides the structured SQL queries to create and manage the **Store Management & Information System (SMIS) Database**. The system is designed to track inventory, purchases, and sales transactions efficiently.

---

## 2. Table Creation Queries

### **Stock Table**
```sql
CREATE TABLE stock (
  stock_id VARCHAR(40) NOT NULL,
  model_id VARCHAR(40) NOT NULL,
  quantity INT CHECK (quantity >= 0),
  PRIMARY KEY (stock_id, model_id)
);
```
- Stores available inventory categorized by **stock_id**.
- Ensures quantity cannot be negative.
- Composite **PRIMARY KEY** ensures each product model has a unique stock entry.

---

### **Product Table**
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
- Holds product details such as **brand, specifications, and price**.
- Enforces **foreign key constraints** linking products to the stock table.

---

### **Purchase Table**
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
- Stores **purchase transactions**.
- Ensures **unique transaction IDs** and maintains referential integrity.

---

### **Customer Table**
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
- Stores **customer details** with phone number as the unique identifier.

---

### **Sales Table**
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
- Records **sales transactions**.
- Ensures all purchases are linked to registered customers.

---

## 3. Data Insertion Queries

### **Insert Data into Purchase Table**
```sql
INSERT INTO purchase (purchaseDate, agentFullName, stock_id, model_id, amountPaid, purchaseTransactionId, purchaseInfoId) VALUES
('2025-02-01', 'John Doe', 'LAPTOP', 'MDL001', '75000', 'TRX001', 'PUR001'),
('2025-02-02', 'Alice Smith', 'LAPTOP', 'MDL002', '70000', 'TRX002', 'PUR002'),
('2025-02-03', 'Robert Brown', 'LAPTOP', 'MDL007', '66000', 'TRX003', 'PUR003'),
('2025-02-04', 'Emily Davis', 'SMARTPHONE', 'MDL003', '53000', 'TRX004', 'PUR004'),
('2025-02-05', 'Michael Wilson', 'SMARTPHONE', 'MDL004', '118000', 'TRX005', 'PUR005'),
('2025-02-06', 'Sophia White', 'SMARTPHONE', 'MDL008', '43000', 'TRX006', 'PUR006'),
('2025-02-07', 'Daniel Green', 'SMARTPHONE', 'MDL010', '31000', 'TRX007', 'PUR007'),
('2025-02-08', 'Olivia Harris', 'TABLET', 'MDL005', '39000', 'TRX008', 'PUR008'),
('2025-02-09', 'Ethan Wright', 'TABLET', 'MDL006', '82000', 'TRX009', 'PUR009'),
('2025-02-10', 'Sophia White', 'TABLET', 'MDL009', '58000', 'TRX010', 'PUR010');
```

### **Insert Data into Customer Table**
```sql
INSERT INTO customer (name, phone, area, city, state, address) VALUES
('Rahul Sharma', '9876543210', 'MG Road', 'Bangalore', 'Karnataka', '123, MG Road, Bangalore'),
('Priya Verma', '8765432109', 'Sector 10', 'Delhi', 'Delhi', 'A-10, Sector 10, Delhi'),
('Amit Patil', '7654321098', 'Shivaji Nagar', 'Pune', 'Maharashtra', '45, Shivaji Nagar, Pune'),
('Sneha Reddy', '6543210987', 'Banjara Hills', 'Hyderabad', 'Telangana', '67, Banjara Hills, Hyderabad'),
('Vikas Gupta', '5432109876', 'Salt Lake', 'Kolkata', 'West Bengal', '21, Salt Lake, Kolkata'),
('Rohan Mehta', '4321098765', 'Park Street', 'Mumbai', 'Maharashtra', '11, Park Street, Mumbai'),
('Neha Jain', '3210987654', 'Whitefield', 'Bangalore', 'Karnataka', '22, Whitefield, Bangalore'),
('Suresh Yadav', '2109876543', 'Old City', 'Hyderabad', 'Telangana', '33, Old City, Hyderabad');
```

---

## 4. Business Queries

### **Calculate Net Profit/Loss for 2025**
```sql
SELECT 
    ( 
        (SELECT COALESCE(SUM(CAST(amountPaid AS DECIMAL(10,2))), 0) FROM sales WHERE YEAR(sales_date) = 2025) 
        - 
        (SELECT COALESCE(SUM(CAST(amountPaid AS DECIMAL(10,2))), 0) FROM purchase WHERE YEAR(purchaseDate) = 2025) 
    ) AS net_profit_loss;
```
- Computes the difference between **total sales and purchase costs**.
- If the result is **positive**, it's a **profit**, otherwise, it's a **loss**.

### **Most Sold Product in 2025**
```sql
SELECT p.brand
FROM sales s
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id
WHERE YEAR(s.sales_date) = 2025
GROUP BY p.brand
ORDER BY COUNT(*) DESC
LIMIT 1;
```
- Identifies the most frequently sold product brand.

---
This document provides structured SQL queries for database creation, data insertion, and business analytics. 🚀
