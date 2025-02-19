# SMIS Database (Store Management & Information System) 🏪📊

## Real-Life Problem Statement  
In many retail stores, inventory management is still handled manually, leading to inefficiencies such as stock shortages, overstocking, and inaccurate tracking of products. Store managers often rely on outdated records, making it difficult to maintain an optimal stock level. Additionally, customers struggle to make informed purchasing decisions due to a lack of real-time product availability and detailed specifications.

The **SMIS Database (Store Management & Information System)** aims to solve these issues by providing a **digital solution** that ensures seamless inventory tracking, automated stock updates, and enhanced customer assistance, ultimately improving the store's operational efficiency and shopping experience.In many retail stores, managing inventory manually leads to inefficiencies, stock shortages, and inaccurate tracking of products. Store managers often struggle with outdated records, leading to overstocking or understocking issues. Additionally, customers face difficulties in making purchasing decisions due to a lack of real-time product availability and feature details. This project aims to solve these challenges by providing a digital solution for seamless inventory management and enhanced customer experience.

## Overview  
SMIS Database is designed to efficiently manage store inventory and assist customers in making informed purchasing decisions. This system ensures accurate stock tracking and provides product availability details to enhance the shopping experience. 🛒📦

## Features  
- 🏷 **Inventory Management:** Track and manage stock levels of all products.  
- 📄 **Product Information:** Store product details, including features and availability.  
- 🤝 **Customer Assistance:** Help customers make better decisions based on stock status and product specifications.  

## Advantages Over Existing Applications 🌟
- ⚡ **Real-time Stock Tracking:** Ensures up-to-date inventory information.  
- 🔍 **Enhanced Search & Filtering:** Customers can easily find desired products.  
- 🔄 **Automated Inventory Updates:** Reduces manual errors and improves efficiency.  
- 📊 **Data-Driven Insights:** Provides analytics for better business decision-making.  
 
## Technologies Used  
This application is built using **Java Full-Stack Web Development**.  
- 🗄 **Database:** MySQL ✅ [Completed]  
- ⚙ **Backend:** Spring Boot (Java-based) 🚧 [Development Phase]  
- 🎨 **Frontend:** React 🚧 [Development Phase]  

## Installation  
1. 🛠 Set up a database using the provided SQL schema.  
2. 🔗 Configure backend connections to interact with the database.  
3. 💻 (Optional) Deploy a frontend for user interaction.  

## Usage  
- 📊 Store managers can add, update, and track product stock.  
- 🔍 Customers can check product availability and specifications before purchasing.  

## Future Enhancements  
- 📈 Implement analytics for sales trends and stock predictions.  
- 🔐 Add role-based access control for security.  
- 📲 Integrate barcode scanning for inventory updates.  




# Business Queries for SMIS Database 📊

The **Store Management & Information System (SMIS)** database allows for a variety of business queries that help in managing inventory, tracking sales, monitoring purchases, and understanding customer behavior. Below are some key queries that can be executed:

## 📦 Inventory & Stock Management Queries  

### 1. Check Available Stock for Each Product  
```sql
SELECT stock_id, model_id, quantity FROM stock WHERE quantity > 0;
```

### 2. List of Out-of-Stock Products  
```sql
SELECT stock_id, model_id FROM stock WHERE quantity = 0;
```

### 3. Total Stock Value by Product  
```sql
SELECT p.model_id, p.brand, SUM(s.quantity * p.price) AS total_value  
FROM stock s  
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id  
GROUP BY p.model_id, p.brand;
```

## 🛍 Sales & Customer Analysis Queries  

### 4. Total Sales Amount for a Given Period  
```sql
SELECT SUM(amountPaid) AS total_sales  
FROM sales  
WHERE sales_date BETWEEN '2025-01-01' AND '2025-01-31';
```

### 5. Top 5 Customers by Purchase Amount  
```sql
SELECT c.name, SUM(s.amountPaid) AS total_spent  
FROM sales s  
JOIN customer c ON s.phone = c.phone  
GROUP BY c.name  
ORDER BY total_spent DESC  
LIMIT 5;
```

### 6. Products Purchased by a Specific Customer  
```sql
SELECT s.phone, p.brand, p.model_id, s.amountPaid, s.sales_date  
FROM sales s  
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id  
WHERE s.phone = '9876543210';
```

## 🛒 Purchase & Supplier Queries  

### 7. Total Purchases from a Specific Supplier  
```sql
SELECT agentFullName, SUM(amountPaid) AS total_purchases  
FROM purchase  
WHERE agentFullName = 'Supplier A'  
GROUP BY agentFullName;
```

### 8. Recent Purchases (Last 30 Days)  
```sql
SELECT * FROM purchase  
WHERE purchaseDate >= CURDATE() - INTERVAL 30 DAY;
```

### 9. Purchase History for a Specific Product  
```sql
SELECT * FROM purchase  
WHERE model_id = 'M12345';
```

## 📈 Business Insights Queries  

### 10. Best-Selling Products  
```sql
SELECT p.brand, p.model_id, COUNT(s.sales_id) AS total_sales  
FROM sales s  
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id  
GROUP BY p.brand, p.model_id  
ORDER BY total_sales DESC  
LIMIT 5;
```

### 11. Monthly Revenue Trend  
```sql
SELECT DATE_FORMAT(sales_date, '%Y-%m') AS month, SUM(amountPaid) AS total_revenue  
FROM sales  
GROUP BY month  
ORDER BY month;
```

### 12. Customers Who Have Made Multiple Purchases  
```sql
SELECT phone, COUNT(sales_id) AS total_purchases  
FROM sales  
GROUP BY phone  
HAVING total_purchases > 1;
```


### 13.Check available stock for a specific model:**
  ```sql
  SELECT * FROM stock WHERE model_id = 'M12345';
  ```

These queries provide insights into inventory management, sales trends, customer purchasing behavior, and supplier transactions. They can be used for **business decision-making**, **performance tracking**, and **optimizing store operations**. 🚀📊




 
