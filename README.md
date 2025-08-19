# Bookstore_SQL_Project
Project Overview

This project is based on designing and querying a relational database for an Online Bookstore. The database is structured with three main entities: Books, Customers, and Orders. It allows storage, management, and retrieval of information about available books, customer details, and purchase transactions.
The SQL script demonstrates fundamental database operations such as:
\n1)Creating databases and tables with proper relationships.
2)Importing data from external CSV files.
3)Executing queries to retrieve, analyze, and summarize information.
4)The project also covers basic SQL queries (e.g., filtering, aggregation, joins) as well as advanced SQL queries (e.g., finding top-selling books, customer spending analysis, and stock management).
5)This simulation provides a strong foundation for understanding how SQL can be used in real-world business scenarios like e-commerce and retail systems.

Objective:
The main objective of this project is to create an Online Bookstore database and use SQL queries to manage books, customers, and orders, as well as to find useful information like best-selling books, customer details, total sales, and remaining stock.



CREATE TABLE Books (
    Book_ID SERIAL PRIMARY KEY,
    Title VARCHAR(100),
    Author VARCHAR(100),
    Genre VARCHAR(50),
    Published_Year INT,
    Price NUMERIC(10, 2),
    Stock INT
);

CREATE TABLE Customers (
    Customer_ID SERIAL PRIMARY KEY,
    Name VARCHAR(100),
    Email VARCHAR(100),
    Phone VARCHAR(15),
    City VARCHAR(50),
    Country VARCHAR(150)
);

CREATE TABLE Orders (
    Order_ID SERIAL PRIMARY KEY,
    Customer_ID INT REFERENCES Customers(Customer_ID),
    Book_ID INT REFERENCES Books(Book_ID),
    Order_Date DATE,
    Quantity INT,
    Total_Amount NUMERIC(10, 2)
);
SELECT *FROM books;
SELECT*FROM Customers;
SELECT*FROM Orders;

--Simple Questions
--1)Retrive All Books In The "Fiction"Genre:

SELECT*FROM books
WHERE genre='Fiction';

--2)Find Books Pulished After The Year 1950:
SELECT*FROM books
WHERE published_year>1950;

--3)List All Customers From The Canada:
SELECT*FROM customers
WHERE country='Canada';

--4)Show Orders Placed In November 2023:

SELECT*FROM orders
WHERE order_date BETWEEN '2023-11-01' AND '2023-11-30';

--5)Retrive The Total Stock Of Books Available:

SELECT SUM(stock) AS Total_Stock
FROM Books;

--6)Find The Details Of Most Expensive Book:

SELECT*FROM Books 
ORDER BY price DESC 
LIMIT 1;

--7)Show All Customers Who Ordered More Than 1 Quntity Of A Book:

SELECT*FROM orders
WHERE quantity>1;

--8)Retrive All Orders Where The Total Amount Exceefd $20:

SELECT*FROM orders
WHERE total_amount>20;

--9)List All Genres Availble In The Books Table:

SELECT DISTINCT genre FROM Books;

--10)Find The Books With The Lowest Stock:

SELECT*FROM books 
ORDER BY stock ASC 
LIMIT 1;

--11)Calculate The Total Revenue Generated From All Orders:

SELECT SUM(total_amount)AS Revenue
FROM orders;

--Advance Questions
--1)Retrive The Total Number Of Books Sold For Each Genre:

SELECT b.Genre,SUM(o.Quantity)AS Total_Books_Sold
FROM Orders o
JOIN Books b ON o.book_id=b.book_id
GROUP BY b.genre;

--2)Find The Average Price Of Books In The "Fantasy" genre:

SELECT AVG(price) AS Average_Price 
FROM Books
WHERE Genre='Fantasy';

--3)List Customers Who Have Placed At Least 2 Orders:

SELECT o.customer_id,c.name,COUNT(o.order_id)AS ORDER_COUNT
FROM orders o
JOIN customers c ON o.customer_id=c.customer_id
GROUP BY o.customer_id, c.name
HAVING COUNT(order_id)>=2;

--4)Find The Most Frequently Ordered Book:

SELECT Book_id,COUNT(order_id)AS ORDER_COUNT
FROM orders
GROUP BY Book_id
ORDER BY ORDER_COUNT DESC
LIMIT 4;

-- 5) Show The Top 3 Most Expensive Books Of 'Fantasy' Genre :

SELECT * FROM books
WHERE genre ='Fantasy'
ORDER BY price DESC LIMIT 3;

-- 6) Retrieve The Total Quantity Of Books Sold By Each Author:

SELECT b.author, SUM(o.quantity) AS Total_Books_Sold
FROM orders o
JOIN books b ON o.book_id=b.book_id
GROUP BY b.Author;

-- 7) List The Cities Where Customers Who Spent Over $30 Are Located:

SELECT DISTINCT c.city, total_amount
FROM orders o
JOIN customers c ON o.customer_id=c.customer_id
WHERE o.total_amount > 30;


-- 8) Find The Customer Who Spent The Most On Orders:
SELECT c.customer_id, c.name, SUM(o.total_amount) AS Total_Spent
FROM orders o
JOIN customers c ON o.customer_id=c.customer_id
GROUP BY c.customer_id, c.name
ORDER BY Total_spent Desc LIMIT 1;


--9) Calculate The Stock Remaining After Fulfilling All Orders:

SELECT b.book_id, b.title, b.stock, COALESCE(SUM(o.quantity),0) AS Order_quantity,  
	b.stock- COALESCE(SUM(o.quantity),0) AS Remaining_Quantity
FROM books b
LEFT JOIN orders o ON b.book_id=o.book_id
GROUP BY b.book_id ORDER BY b.book_id;

