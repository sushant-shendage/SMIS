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




## 1. Total Sales Amount for 2025
```sql
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_sales_2025
FROM sales
WHERE YEAR(sales_date) = 2025;
```

## 2. Total Purchase Amount for 2025
```sql
SELECT SUM(CAST(amountPaid AS DECIMAL)) AS total_purchase_2025
FROM purchase
WHERE YEAR(purchaseDate) = 2025;
```

## 3. Net Profit/Loss Calculation
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

## 4. Stock-wise Total Sales & Total Purchase Amount
```sql
SELECT 
    p.stock_id, 
    SUM(CAST(s.amountPaid AS DECIMAL(10,2))) AS total_sales_amount,
    SUM(CAST(p.amountPaid AS DECIMAL(10,2))) AS total_purchase_amount
FROM purchase p
LEFT JOIN sales s ON p.stock_id = s.stock_id
GROUP BY p.stock_id;
```

## 5. Most Sold Product Brand in 2025
```sql
SELECT p.brand
FROM sales s
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id
WHERE YEAR(s.sales_date) = 2025
GROUP BY p.brand
ORDER BY COUNT(*) DESC
LIMIT 1;
```

## 6. Least Sold Product Category in 2025
```sql
SELECT p.stock_id  
FROM sales s  
JOIN product p ON s.stock_id = p.stock_id AND s.model_id = p.model_id  
WHERE YEAR(s.sales_date) = 2025  
GROUP BY p.stock_id  
ORDER BY COUNT(*) ASC  
LIMIT 1;
```

## 7. Available Smartphones with 8GB RAM and Price Below 50,000
```sql
SELECT *  
FROM product  
WHERE stock_id = 'SMARTPHONE'  
AND ram = 8  
AND price < 50000;


# Conclusion  

This database project provides a **digital store management system** that enhances stock tracking, product management, and transaction recording. By digitizing store information, it enables **customers to make faster and easier decisions** based on product availability and features.  

The **Store Management & Information System (SMIS)** solves real-world business challenges by streamlining inventory management and improving customer experience. This project lays a strong foundation for future enhancements like analytics, reporting, and automation to optimize business operations.  





 
