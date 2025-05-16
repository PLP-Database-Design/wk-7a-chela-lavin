# 📝 Assignment: Database Design and Normalization

## 🎯 **Learning Objectives**
* 🛠️ **Understand the principles of good database design** and **normalization**.
* 💡 **Apply normalization techniques** to improve database structure and efficiency.
* 🔍 **Learn First, Second, and Third Normal Forms** (1NF, 2NF, 3NF) to eliminate redundancy and optimize data storage.

---

## 📋 **What You'll Need**
* 💻 A computer with internet access.
* ✍️ A code editor (e.g., Visual Studio Code).
* 🖥️ MySQL Workbench or another SQL database environment.

---


## 📝 Submission Instructions  
📂 Write all your SQL queries in the **answers.sql** file.  
✍️ Answer each question concisely and make sure your queries are clear and correct.  
🗣️ Structure your responses clearly, and use comments if necessary to explain your approach.

--- 

## 📚 Assignment Questions

---

### Question 1 Achieving 1NF (First Normal Form) 🛠️
Task:
- You are given the following table **ProductDetail**:

| OrderID | CustomerName  | Products                        |
|---------|---------------|---------------------------------|
| 101     | John Doe      | Laptop, Mouse                   |
| 102     | Jane Smith    | Tablet, Keyboard, Mouse         |
| 103     | Emily Clark   | Phone                           |


- In the table above, the **Products column** contains multiple values, which violates **1NF**.
- **Write an SQL query** to transform this table into **1NF**, ensuring that each row represents a single product for an order

- -- Create a new table to store the 1NF data.  This avoids modifying the original table.
CREATE TABLE ProductDetail_1NF (
    OrderID INT,
    CustomerName VARCHAR(255),
    Product VARCHAR(255)
);

-- Populate the new table by splitting the 'Products' string into individual rows.
-- This approach works in many SQL dialects, including MySQL, PostgreSQL, and SQL Server.
INSERT INTO ProductDetail_1NF (OrderID, CustomerName, Product)
SELECT
    OrderID,
    CustomerName,
    TRIM(value) AS Product -- Trim to remove leading/trailing spaces
FROM ProductDetail
CROSS APPLY STRING_SPLIT(Products, ',');

-- Alternative using a Common Table Expression (CTE) for clarity (more standard SQL):
-- WITH SplitProducts AS (
--     SELECT
--         OrderID,
--         CustomerName,
--         TRIM(value) AS Product
--     FROM ProductDetail
--     CROSS APPLY STRING_SPLIT(Products, ',')
-- )
-- INSERT INTO ProductDetail_1NF (OrderID, CustomerName, Product)
-- SELECT OrderID, CustomerName, Product FROM SplitProducts;

-- Query the new table to see the result.
SELECT * FROM ProductDetail_1NF;

--DROP TABLE ProductDetail_1NF; -- uncomment this line if you want to clean up.


--- 

### Question 2 Achieving 2NF (Second Normal Form) 🧩

- You are given the following table **OrderDetails**, which is already in **1NF** but still contains partial dependencies:

| OrderID | CustomerName  | Product      | Quantity |
|---------|---------------|--------------|----------|
| 101     | John Doe      | Laptop       | 2        |
| 101     | John Doe      | Mouse        | 1        |
| 102     | Jane Smith    | Tablet       | 3        |
| 102     | Jane Smith    | Keyboard     | 1        |
| 102     | Jane Smith    | Mouse        | 2        |
| 103     | Emily Clark   | Phone        | 1        |

- In the table above, the **CustomerName** column depends on **OrderID** (a partial dependency), which violates **2NF**. 

- Write an SQL query to transform this table into **2NF** by removing partial dependencies. Ensure that each non-key column fully depends on the entire primary key.-- Create a new table for Orders (OrderID, CustomerName) - no partial dependency
CREATE TABLE Orders2NF (
    OrderID INT PRIMARY KEY,
    CustomerName VARCHAR(255)
);

-- Populate the Orders table
INSERT INTO Orders2NF (OrderID, CustomerName)
SELECT DISTINCT OrderID, CustomerName
FROM OrderDetails;

-- Create a new table for OrderDetails (OrderID, Product, Quantity)
CREATE TABLE OrderDetails2NF (
    OrderID INT,
    Product VARCHAR(255),
    Quantity INT,
    PRIMARY KEY (OrderID, Product),  -- Composite primary key
    FOREIGN KEY (OrderID) REFERENCES Orders2NF(OrderID) -- Foreign key referencing Orders table
);

-- Populate the OrderDetails table
INSERT INTO OrderDetails2NF (OrderID, Product, Quantity)
SELECT OrderID, Product, Quantity
FROM OrderDetails;

-- Query the new tables to see the result.
SELECT * FROM Orders2NF;
SELECT * FROM OrderDetails2NF;

--DROP TABLE Orders2NF;
--DROP TABLE OrderDetails2NF;

- 

---
Good luck 🚀
