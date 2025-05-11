CREATE DATABASE OnlineStore;
USE OnlineStore;
CREATE TABLE Categories (CategoryID int PRIMARY KEY auto_increment, CategoryName varchar (100) NOT NULL);
CREATE TABLE Products (ProductID int PRIMARY KEY auto_increment, 
ProductName varchar (100) NOT NULL,
Price DECIMAL(10,2) NOT NULL,
CategoryID int,
FOREIGN KEY (CategoryID) REFERENCES Categories(CategoryID)
);
CREATE TABLE Customers (CustomerID INT PRIMARY KEY auto_increment,
CustomerName varchar(50) NOT NULL,
Email varchar(50)
);
CREATE TABLE Orders (OrderID INT PRIMARY KEY auto_increment,
CustomerID INT, 
OrderDate DATE,
FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);
CREATE TABLE OrderDetails (OrderDetailID INT PRIMARY KEY AUTO_INCREMENT,
OrderID INT,
ProductID INT,
Quantity INT,
FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
FOREIGN KEY (ProductID) REFERENCES Products(ProductID)
);
SHOW tables;
INSERT INTO Categories (CategoryName) VALUES 
('Electronics'), 
('Clothing'), 
('Books');
INSERT INTO Products (ProductName, Price, CategoryID) VALUES
('Smartphone', 699.99, 1),
('Laptop', 999.99, 1),
('T-Shirt', 19.99, 2),
('Novel', 14.99, 3);
INSERT INTO Customers (CustomerName, Email) VALUES
('John Doe', 'john@example.com'),
('Jane Smith', 'jane@example.com');
INSERT INTO Orders (CustomerID, OrderDate) VALUES
(1, '2025-04-25'),
(2, '2025-04-26');
INSERT INTO OrderDetails (OrderID, ProductID, Quantity) VALUES
(1, 1, 2),
(1, 4, 1),
(2, 3, 3);
SHOW TABLES;
SELECT * FROM Products;
SELECT * FROM Categories;

-- INNER JOIN                                      -- Retrieving all the products along with their category names

SELECT p.ProductID,
p.ProductName,
p.Price,
c.CategoryName
FROM Products p
INNER JOIN Categories c ON p.CategoryID = c.CategoryID;

-- LEFT JOIN                                      -- Get a list of all customers along with their order dates

SELECT * FROM Customers; 
SELECT * FROM Orders;
SELECT c.CustomerID, 
c.CustomerName, 
c.Email,
o.OrderDate
FROM Customers c
LEFT JOIN Orders O ON c.CustomerID = o.CustomerID;

-- RIGHT JOIN                                     -- Retrieving all the order details along with customers information

SELECT * FROM Orders;
SELECT * FROM Customers;
SELECT c.CustomerID,
c.CustomerName,
c.Email,
o.OrderID,
o.OrderDate
FROM Customers c
RIGHT JOIN Orders o ON o.CustomerID = c.CustomerID;

-- CROSS JOIN                                     -- Retrieving all the customers and pairing each one with every product

SELECT * FROM Customers;
SELECT * FROM Products;
SELECT c.CustomerName, p.ProductName
FROM Customers c
CROSS JOIN Products p;

-- VIEW                                          -- Listing all customers with their products and quantity using VIEW

SELECT * FROM Orders;
SELECT * FROM Products;
SELECT * FROM OrderDetails;
CREATE VIEW CustomerOrderDetails AS
SELECT c.CustomerName, 
o.OrderID, 
p.ProductName, 
od.Quantity
FROM Customers c
JOIN Orders o ON c.CustomerID = o.CustomerID
JOIN OrderDetails od ON o.OrderID = od.OrderID
JOIN Products p ON od.ProductID = p.ProductID;
SELECT * FROM CustomerOrderDetails;

-- SUBQUERY (SINGLE ROW)                         -- Finding the highest price product

SELECT ProductName, Price FROM Products
WHERE Price = (SELECT Max(Price) FROM Products);

SELECT ProductName, Price From Products         -- Finding the lowest price product
Where Price = (Select Min(Price) From Products);

SELECT CustomerName FROM Customers c              -- Customer Who Has Ordered the Most Expensive Product
WHERE CustomerID = (
SELECT o.CustomerID FROM Orders o
JOIN OrderDetails od ON o.OrderID = od.OrderID
Join Products p ON od.ProductID = p.ProductID
ORDER BY Price DESC
LIMIT 1
);

-- SUBQUERY (MULTI ROW)

SELECT ProductName, Price                       -- Finding higher price product than average price of all products
FROM Products
WHERE Price > (SELECT AVG(Price) FROM Products);

SELECT CustomerName                             -- Customers Who Have Ordered More Than One Product
FROM Customers              
WHERE CustomerID IN (
SELECT o.CustomerID
FROM Orders o
JOIN OrderDetails od ON o.OrderID = od.OrderID
GROUP BY o.CustomerID
HAVING COUNT(od.ProductID) > 1
);

SELECT ProductName                             -- Get All Products from the "Electronics" Category          
FROM Products
WHERE CategoryID IN (
SELECT CategoryID
FROM Categories
WHERE CategoryName = 'Electronics'
);

-- STORED PROCEDURE

DELIMITER //                                   -- Stored Procedure to Show All Customers
CREATE PROCEDURE ShowAllCustomers()
BEGIN
SELECT * FROM Customers;
END//
DELIMITER ;
CALL ShowAllCustomers();

DELIMITER //
CREATE PROCEDURE GetCustomerCount()            -- Get Customers count 
BEGIN
SELECT COUNT(*) AS TotalCustomers FROM Customers;
END //
DELIMITER ;
CALL GetCustomerCount();# mysql-project
