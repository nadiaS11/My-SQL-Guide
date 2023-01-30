# Data Manipulation In SQL: A Concise Overview

SQL, or Structured Query Language, is a powerful programming language used to manage and manipulate data stored in a relational database. As a novice, I’ve learned that understanding how to manipulate the data in SQL is crucial for working with databases and getting better your SQL skills.

Today, I’ll discuss the fundamentals of SQL data manipulation, like inserting, updating, and deleting data, as well as more advanced strategies for working with large data sets that I’ve used in the past two weeks.

I’ll be using the sales schema that comes with MySQL workbench.

## Inserting Data into a Table
Inserting data into a table is among the most fundamental SQL tasks. Using the INSERT INTO statement you can insert new rows into a table. For example, in ‘sales’ database to insert new row into ‘customes’ table with columns customer_code, custmer_name, customer_type, you would use the following syntax:

INSERT INTO customers(customer_code, custmer_name, customer_type)
values ('Cust039', 'Leader', 'Saas');
Remember while inserting, it’s crucial to check that the values you’re inserting into a table correspond to the data types of the column. For instance, you should insert an integer value rather than a string if a column is defined as an INT.


## Updating Data in a Table
Updating data that already exists in a table is another prolific SQL operation. We use the UPDATE statement to change already-existing rows in a table. For example, to change the name of a previously inserted customer from ‘Leader’ to ‘Canon’ in the “customers” table with the customer code “Cus039,” use the SQL statement:

UPDATE customers
SET custmer_name = 'Canon'
WHERE customer_code = 'Cust039';
Your UPDATE statement must contain a WHERE clause to say specifically which rows should be updated. The new values will be applied to all table rows if there is no WHERE clause.

If you don’t want to make changes to the original table, the best practice is to create a copy of it. I created a copy of the ‘customers’ table as ‘customes1’ using the SQL statement below.

drop table if exists customers1;
create table customers1 as
(select *
from customers);

## Deleting Data from a Table
You could also need to remove data from a table besides adding and updating data. This can be done by deleting existing rows from a table using the DELETE statement.

For the previous example, now that I have a duplicate of the “customers” table, I want to delete the row I initially inserted. I use the following SQL statement to do so.

DELETE FROM customers 
WHERE customer_code = 'Cust039';
Similar to the UPDATE statement, your DELETE statement must include a WHERE clause to specify which rows should be deleted because, of course, you don’t want to delete all of the rows in the table.


## Advanced Techniques for Working with Large Sets of Data
Working with large data sets can be challenging. However, by using some sophisticated data manipulation techniques, you can handle and manipulate your data accurately.


### The LIMIT clause
The LIMIT clause is one of the most potent methods for manipulating data in MySQL. When working with large tables, it enables you to specify the number of rows that should be returned. For example, the following SQL statement returns the first ten rows of the “customers” table:

SELECT * FROM customers 
LIMIT 10;

### Subqueries
With the help of subqueries, you can use SELECT statements to nest one inside another, retrieving data based on the output of another query. A subquery’s primary function is to retrieve data that will be employed in the main query. This enables you to carry out more intricate data manipulation and analysis as well as utilize your database resources more effectively.

Let’s take a look at the following example of subquery using the both “transactions” and “customers” tables from the same database.

SELECT *
FROM  sales.transactions
WHERE customer_code in (               
                SELECT customer_code 
                FROM  sales.customers 
                WHERE  customer_type='E-Commerce');
In this example, the subquery retrieves the customer code of the ‘E-Commerce’ customer type from the “customers” table. The result of the subquery is then used in the main query to filter the data from the “transactions” table to only include transactions made by that customer in the ‘E-Commerce’ sector.

### Creating And Using Stored Procedure
Stored procedures are pre-compiled SQL statements that can be executed repeatedly. With stored procedures, you can package up intricate logic into a single, reusable code block that can be run with just a single call.

Let’s examine how stored procedures can be utilized in the context of the “transactions” table that has the following columns: product_code, customer_code, market_code, order_date, sales_qty, sales_amount, and currency.

DELIMITER //
CREATE PROCEDURE GetSales (IN currency_new VARCHAR(45))
BEGIN
    SELECT SUM(sales_amount)
    FROM transactions
    WHERE currency = currency_new;
END //
DELIMITER ;
In this example, the stored procedure takes an input parameter currency_new and returns the total sales amount for each currency. The SUM function is used to calculate the total sales amount for each market

CALL GetSales('USD');
In this case, the stored procedure returns the total sales amount for transactions completed in USD. To obtain the total sales amount for various currencies, you can use a different currency when calling the stored procedure.

### Creating Index
Data structures called indexes can be used to speed up database query execution. It is quicker to retrieve data based on those values when there is an index that stores a mapping between the values in a given column and the corresponding row in a table. I’ll use the transactions table to show how to use indexes in MySQL.

CREATE INDEX idx_order_date ON transactions (order_date);
Here, the order_date column in the transactions table serves as the basis for the index. The index, dubbed idx_order_date, can be used to speed up data search and sort operations based on the order date column.

### Using Common Table Expressions (CTEs)
The use of temporary tables, CTEs, is another important SQL data manipulation technique. It is possible to refer to a temporary result set that is stored in temporary tables from the main query.
CTEs are useful for breaking down complex queries into smaller portions.

WITH sales_by_market AS (
  SELECT market_code, SUM(sales_amount) AS total_sales
  FROM transactions
  GROUP BY market_code
)
SELECT market_code, total_sales
FROM sales_by_market
ORDER BY total_sales DESC
LIMIT 5;

In this example, a CTE named sales_by_market is created to calculate the total sales for each market by using the GROUP BY clause. In the main query, the top 5 markets are then chosen based on their total sales, and the output is sorted in descending order.

Instead of performing the same calculation within the main query, CTEs make the code much more readable and understandable. By doing so, errors are less likely to be introduced into the code in the future.
