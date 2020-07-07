# USTSV
HW1 for haowang

https://www.richardtwatson.com/dm6e/Reader/ClassicModels.html

SQL queries

ClassicModels is a fictitious company. Use the ClassicModels database to answer the following requests (SQL to create the database). Your instructor has the answers to all queries.
The latitude and longitude are specified for office and customer locations in the Offices and Customers tables, respectively. The SRID is set to 4326 to indicate the Spacial Reference System (SRS) is geographic (see Chapter 11 for more details on SRID and SRS).
You can access ClassicModels at richardtwatson.com with accountid=db1 and password=student.

- [Single entity](#Single-entity)
  - [1 Prepare a list of offices sorted by country, state, city](#Prepare-a-list-of-offices-sorted-by-country-state-city)
  - [2 How many employees are there in the company?](#How-many-employees-are-there-in-the-company)
  - [3 What is the total of payments received?](#What-is-the-total-of-payments-received)
  - [4 List the product lines that contain 'Cars'](#List-the-product-lines-that-contain-Cars)
  - [5 Report total payments for October 28, 2004](#Report-total-payments-for-October-28-2004)
  - [6 Report those payments greater than $100,000](#Report-those-payments-greater-than-100000-dollar)
  - [7 List the products in each product line](#List-the-products-in-each-product-line)
  - [8 How many products in each product line?](#How-many-products-in-each-product-line)
  - [9 What is the minimum payment received?](#What-is-the-minimum-payment-received)
  - [10 List all payments greater than twice the average payment](#List-all-payments-greater-than-twice-the-average-payment)
  - [11 What is the average percentage markup of the MSRP on buyPrice?](#What-is-the-average-percentage-markup-of-the-MSRP-on-buyPrice)
  - [12 How many distinct products does ClassicModels sell?](#How-many-distinct-products-does-ClassicModels-sell)
  - [13 Report the name and city of customers who don't have sales representatives?](#Report-the-name-and-city-of-customers-who-do-not-have-sales-representatives)
  - [14 What are the names of executives with VP or Manager in their title?](#What-are-the-names-of-executives-with-VP-or-Manager-in-their-title)
  - [15 Which orders have a value greater than $5,000?](#Which-orders-have-a-value-greater-than-5000)
  
 ## Single entity
 
#### Prepare a list of offices sorted by country state city
```sql
SELECT officeCode FROM offices ORDER BY country, state, city;
```

#### How many employees are there in the company
```sql
SELECT COUNT(DISTINCT employeeNumber) AS allemployee_Num FROM employees;
```

#### What is the total of payments received
```sql
SELECT ROUND(SUM(amount),1) AS total_Pay FROM payments;
```

#### List the product lines that contain Cars
```sql
solution1
SELECT * FROM productlines WHERE productLine = 'Classic Cars' 
OR  productLine = 'Vintage Cars';
```
```sql
solution2
SELECT * FROM productlines WHERE productLine LIKE '%Cars%';
```

#### Report total payments for October 28 2004
```sql
SELECT ROUND(SUM(amount),1) AS total_Pay FROM payments WHERE paymentDate = '2004-10-28';
```

#### Report those payments greater than 100000 dollar
```sql
SELECT * FROM payments WHERE amount > 100000;
```

#### List the products in each product line
```sql
SELECT DISTINCT productName, productLine FROM products ORDER BY productLine;
```

#### How many products in each product line?
```sql
SELECT COUNT(DISTINCT productName) AS product_Count, productLine FROM products GROUP BY productLine;
```

#### What is the minimum payment received
```sql
SELECT min(amount) AS min_Pay FROM payments;
```

#### List all payments greater than twice the average payment
```sql
SELECT AVG(amount) AS avg_Price FROM payments;
SELECT * FROM payments WHERE amount > 2 * (SELECT AVG(amount) AS avg_Price FROM payments);
```

#### What is the average percentage markup of the MSRP on buyPrice
```sql
SELECT CONCAT(ROUND(AVG(MSRP/buyPrice),2) * 100, '%' ) AS markup_Per FROM products;
```

#### How many distinct products does ClassicModels sell
```sql
SELECT COUNT(DISTINCT productCode) AS sell_Num FROM products;
```

#### Report the name and city of customers who do not have sales representatives
```sql
SELECT customerName, city FROM customers WHERE salesRepEmployeeNumber is NULL;
```

#### What are the names of executives with VP or Manager in their title
```sql
SELECT CONCAT(firstname,' ', lastname) AS full_Name,jobTitle FROM employees WHERE jobTitle like '%VP%' 
OR jobTitle like '%Manager%';
```

#### Which orders have a value greater than 5000
```sql
SELECT orderNumber, (priceEach * quantityOrdered) AS order_Value FROM orderdetails 
WHERE priceEach * quantityOrdered > 5000 ORDER BY (priceEach * quantityOrdered) ASC;
```

- [One to many relationship](#One-to-many-relationship)
  - [1 Report the account representative for each customer](#Report-the-account-representative-for-each-customer)
  - [2 Report total payments for Atelier graphique](#Report-total-payments-for-Atelier-graphique)
  - [3 Report the total payments by date](#Report-the-total-payments-by-date)
  - [4 Report the products that have not been sold](#Report-the-products-that-have-not-been-sold)
  - [5 List the amount paid by each customer](#List-the-amount-paid-by-each-customer)
  - [6 How many orders have been placed by Herkku Gifts?](#How-many-orders-have-been-placed-by-Herkku-Gifts)
  - [7 Who are the employees in Boston?](#Who-are-the-employees-in-Boston)
  - [8 Report those payments greater than $100,000](#Report-those-payments-greater-than-100000-dollar)
  - [9 List the value of 'On Hold' orders](#List-the-value-of-On-Hold-orders)
  - [10 Report the number of orders 'On Hold' for each customer](#Report-the-number-of-orders-On-Hold-for-each-customer)
  
## One to many relationship

#### Report the account representative for each customer
```sql
SELECT customerName, CONCAT(employees.firstName,' ',employees.lastName) AS account_Name FROM customers INNER JOIN employees
ON customers.salesRepEmployeeNumber = employees.employeeNumber;
```

#### Report total payments for Atelier graphique
```sql
SELECT SUM(amount) AS total_Pay FROM payments INNER JOIN customers ON payments.customerNumber = customers.customerNumber 
WHERE customers.customerName = 'Atelier graphique';
```

#### Report the total payments by date
```sql
SELECT ROUND(SUM(amount),2) AS total_Pay, paymentDate AS pay_Date FROM payments 
GROUP BY paymentDate ORDER BY paymentDate ASC;
```

#### Report the products that have not been sold
```sql
SELECT productCode FROM orderdetails;
SELECT * FROM products WHERE products.productCode NOT IN (SELECT productCode FROM orderdetails);
```

#### List the amount paid by each customer
```sql
SELECT ROUND(SUM(amount),2) AS each_Pay, customerName FROM customers 
INNER JOIN payments ON customers.customerNumber = payments.customerNumber 
GROUP BY payments.customerNumber;
```

#### How many orders have been placed by Herkku Gifts
```sql
SELECT COUNT(DISTINCT o.orderNumber) FROM orders o INNER JOIN customers c ON o.customerNumber = c.customerNumber
WHERE c.customerName = 'Herkku Gifts';
```

#### Who are the employees in Boston
```sql
SELECT CONCAT(e.firstName,' ',e.lastName) AS employee_Name FROM employees e INNER JOIN offices o 
ON e.officeCode= o.officeCode
WHERE o.city = 'Boston';
```

#### Report those payments greater than 100000 dollar
```sql
SELECT c.customerNumber, c.customerName, p.checkNumber, p.paymentDate, p.amount AS high_Pay 
FROM payments p INNER JOIN customers c 
ON c.customerNumber = p.customerNumber
WHERE p.amount > 100000;
```

#### List the value of On Hold orders
```sql
SELECT ROUND((od.priceEach * od.quantityOrdered),2) AS order_values, o.status FROM orders o INNER JOIN orderdetails od 
ON o.orderNumber = od.orderNumber
WHERE o.status = 'On Hold';
```

#### Report the number of orders On Hold for each customer
```sql
SELECT COUNT(DISTINCT o.status) AS order_number, c.customerName, o.status FROM orders o INNER JOIN customers c
ON o.customerNumber = c.customerNumber
WHERE o.status = 'On Hold'
GROUP BY c.customerNumber;
```

- [Many to many relationship](#Many-to-many-relationship)
  - [1 List products sold by order date](#List-products-sold-by-order-date)
  - [2 List the order dates in descending order for orders for the 1940 Ford Pickup Truck](#List-the-order-dates-in-descending-order-for-orders-for-the-1940-Ford-Pickup-Truck)
  - [3 List the names of customers and their corresponding order number where a particular order from that customer has a value greater than $25,000?](#List-the-names-of-customers-and-their-corresponding-order-number-where-a-particular-order-from-that-customer-has-a-value-greater-than-25000-dollar)
  - [4 Are there any products that appear on all orders?](#Are-there-any-products-that-appear-on-all-orders)
  - [5 List the names of products sold at less than 80% of the MSRP](#List-the-names-of-products-sold-at-less-than-eighty-percent-of-the-MSRP)
  - [6 Reports those products that have been sold with a markup of 100% or more](#Reports-those-products-that-have-been-sold-with-a-markup-of-1-or-more)
  - [7 List the products ordered on a Monday](#List-the-products-ordered-on-a-Monday)
  - [8 What is the quantity on hand for products listed on 'On Hold' orders?](#What-is-the-quantity-on-hand-for-products-listed-on-On-Hold-orders)
  
## Many-to-many-relationship
 
#### List-products-sold-by-order-date
```sql
SELECT o.orderNumber, od.productCode, p.productName, o.orderDate FROM orders o, orderdetails od, products p
WHERE o.orderNumber = od.orderNumber 
AND od.productCode = p.productCode
ORDER BY o.orderDate ASC;
```

#### List the order dates in descending order for orders for the 1940 Ford Pickup Truck
```sql
SELECT o.orderNumber, p.productName, o.orderDate FROM orders o, orderdetails od, products p 
WHERE od.orderNumber = o.orderNumber
AND p.productCode = od.productCode
AND productName = '1940 Ford Pickup Truck'
ORDER BY o.orderDate DESC;
```

#### List the names of customers and their corresponding order number where a particular order from that customer has a value greater than 25000 dollar
```sql
SELECT c.customerName, o.orderNumber, ROUND(SUM(quantityOrdered * priceEach),2) AS cu_value
FROM orders o, customers c, orderdetails od 
WHERE o.customerNumber = c.customerNumber 
AND od.orderNumber = o.orderNumber
GROUP BY od.orderNumber
HAVING cu_value > 25000;
```

#### Are there any products that appear on all orders
```sql
???
SELECT p.productCode, productName FROM products p, orders o, orderdetails od
WHERE p.productCode = od.productCode
AND o.orderNumber = od.orderNumber
AND o.orderNumber IN (SELECT orderNumber FROM orders); 
```

#### List the names of products sold at less than eighty percent of the MSRP
```sql
SELECT productName, ROUND((priceEach/MSRP),2) AS ratio FROM products p, orderdetails od
WHERE od.productCode = p.productCode
AND priceEach/MSRP < 0.8;
```

#### Reports those products that have been sold with a markup of 1 or more
```sql
SELECT DISTINCT orderNumber, productName, ROUND(((priceEach - buyPrice)/buyPrice),2) AS markup 
FROM products p, orderdetails od
WHERE od.productCode = p.productCode
AND (priceEach - buyPrice)/buyPrice > 1;
```

#### List the products ordered on a Monday
```sql
???
SELECT p.productCode, p.productName, o.orderDate FROM products p, orderdetails od, orders o
WHERE p.productCode = od.productCode
AND o.orderNumber = od.orderNumber
AND WEEK(o.orderDate) = 1
ORDER BY o.orderDate ASC;  -- 以每周周一作为周一
```

#### What is the quantity on hand for products listed on On Hold orders
```sql
SELECT p.quantityInStock, p.productName, o.status
FROM orders o, orderdetails od, products p
WHERE o.orderNumber = od.orderNumber
AND p.productCode = od.productCode
AND o.status = 'On Hold';
```

- [Regular expressions](#Regular-expressions)
  - [1 Find products containing the name 'Ford'](#Find-products-containing-the-name-Ford)
  - [2 List products ending in 'ship'](#List-products-ending-in-ship)
  - [3 Report the number of customers in Denmark, Norway, and Sweden](#Report-the-number-of-customers-in-Denmark-Norway-and-Sweden)
  - [4 What are the products with a product code in the range S700_1000 to S700_1499?](#What-are-the-products-with-a-product-code-in-the-range-S700-1000-to-S700-1499)
  - [5 Which customers have a digit in their name?](#Which-customers-have-a-digit-in-their-name)
  - [6 List the names of employees called Dianne or Diane](#List-the-names-of-employees-called-Dianne-or-Diane)
  - [7 List the products containing ship or boat in their product name](#List-the-products-containing-ship-or-boat-in-their-product-name)
  - [8 List the products with a product code beginning with S700](#List-the-products-with-a-produc-code-beginning-with-S700)
  - [9 List the names of employees called Larry or Barry](#List-the-names-of-employees-called-Larry-or-Barry)
  - [10 List the names of employees with non-alphabetic characters in their names](#List-the-names-of-employees-with-non-alphabetic-characters-in-their-names)
  - [11 List the vendors whose name ends in Diecast](#List-the-vendors-whose-name-ends-in-Diecast)
  
## Regular-expressions
 
#### Find products containing the name Ford
```sql
SELECT * FROM products WHERE productName LIKE '%Ford%';
```

#### List products ending in ship
```sql
SELECT * FROM products WHERE productName LIKE '%ship';
```

#### Report the number of customers in Denmark Norway and Sweden
```sql
SELECT COUNT(customerNumber) AS customer_num FROM customers
WHERE country = 'Sweden' 
OR country = 'Denmark' 
OR country = 'Norway';
```

#### What are the products with a product code in the range S700_1000 to S700_1499
```sql
SELECT  DISTINCT productName,p.productCode FROM orderdetails od, products p 
WHERE od.productCode = p.productCode
AND p.productCode BETWEEN 'S700_1000' AND 'S700_1499';
```

#### Which customers have a digit in their name
```sql
solution1
SELECT * FROM customers WHERE customerName LIKE '%0%'
OR customerName LIKE '%1%' OR customerName LIKE '%2%' OR customerName LIKE '%3%' OR customerName LIKE '%4%' 
OR customerName LIKE '%5%' OR customerName LIKE '%6%' OR customerName LIKE '%7%' OR customerName LIKE '%8%'
OR customerName LIKE '%9%';
```
```sql
solution2
SELECT * FROM 
customers
WHERE customerName REGEXP '[0-9]';
```

#### List the names of employees called Dianne or Diane
```sql
select CONCAT(firstName,' ',lastName) AS full_Name from employees WHERE firstName LIKE '%Dianne%'
OR firstName LIKE '%Diane%'
OR lastName LIKE '%Dianne%'
OR lastName LIKE '%Diane%';
```

#### List the products containing ship or boat in their product name
```sql
SELECT * FROM products WHERE productName LIKE '%ship%'
OR productName LIKE '%boat%';
```

#### List the products with a product code beginning with S700
```sql
SELECT * FROM products WHERE productCode LIKE 'S700%';
```

#### List the names of employees called Larry or Barry
```sql
select CONCAT(firstName,' ',lastName) AS full_Name from employees WHERE firstName LIKE '%Larry%'
OR firstName LIKE '%Barry%'
OR lastName LIKE '%Larry%'
OR lastName LIKE '%Barry%';
```

#### List the names of employees with non alphabetic characters in their names
```sql
SELECT CONCAT(firstName,' ',lastName) AS full_Name FROM employees 
WHERE firstName NOT LIKE '%[^a-z]%'
OR lastName NOT LIKE '%[^a-z]%';
```

#### List the vendors whose name ends in Diecast
```sql
SELECT productVendor FROM products WHERE productVendor LIKE '%Diecast';
-- Vendors代表供应商
```

- [General queries](#General-queries)
  - [1 Who is at the top of the organization](#Who-is-at-the-top-of-the-organization)
  - [2 Who reports to William Patterson?](#Who-reports-to-William-Patterson)
  - [3 List all the products purchased by Herkku Gifts](#List-all-the-products-purchased-by-Herkku-Gifts)
  - [4 Compute the commission for each sales representative, assuming the commission is 5% of the value of an order](#Compute-the-commission-for-each-sales-representative)
  - [5 What is the difference in days between the most recent and oldest order date in the Orders file?](#What-is-the-difference-in-days-between-the-most-recent-and-oldest-order-date-in-the-Orders-file)
  - [6 Compute the average time between order date and ship date for each customer ordered by the largest difference](#Compute-the-average-time-between-order-date-and-ship-date-for-each-customer-ordered-by-the-largest-difference)
  - [7 What is the value of orders shipped in August 2004?](#What-is-the-value-of-orders-shipped-in-August-2004)
  - [8 Compute the total value ordered, total amount paid, and their difference for each customer for orders placed in 2004 and payments received in 2004](#Compute-the-total-value-ordered-total-amount-paid-and-their-difference-for-each-customer-for-orders-placed-in-2004-and-payments-received-in-2004)
  - [9 List the employees who report to those employees who report to Diane Murphy](#List-the-employees-who-report-to-those-employees-who-report-to-Diane-Murphy)
  - [10 What is the percentage value of each product in inventory sorted by the highest percentage first](#What-is-the-percentage-value-of-each-product-in-inventory-sorted-by-the-highest-percentage-first)
  - [11 Write a function to convert miles per gallon to liters per 100 kilometers](#Write-a-function-to-convert-miles-per-gallon-to-liters-per-100-kilometers)
  - [12 Write a procedure to increase the price of a specified product category by a given percentage](#Write-a-procedure-to-increase-the-price-of-a-specified-product-category-by-a-given-percentage)
  - [13 What is the value of orders shipped in August 2004](#What-is-the-value-of-orders-shipped-in-August-2004)
  - [14 What is the ratio the value of payments made to orders received for each month of 2004](#What-is-the-ratio-the-value-of-payments-made-to-orders-received-for-each-month-of-2004)
  - [15 What is the difference in the amount received for each month of 2004 compared to 2003?](#What-is-the-difference-in-the-amount-received-for-each-month-of-2004-compared-to-2003)
  - [16 Write a procedure to report the amount ordered in a specific month and year for customers containing a specified character string in their name](#Write-a-procedure-to-report-the-amount-ordered-in-a-specific-month-and-year-for-customers-containing-a-specified-character-string-in-their-name)
  - [17 Write a procedure to change the credit limit of all customers in a specified country by a specified percentage](Write-a-procedure-to-change-the-credit-limit-of-all-customers-in-a-specified-country-by-a-specified-percentage)
  - [18 Report the names of products that appear in the same order ten or more times](#Report-the-names-of-products-that-appear-in-the-same-order-ten-or-more-times)
  - [19 Compute the revenue generated by each customer based on their orders](#Compute-the-revenue-generated-by-each-customer-based-on-their-orders)
  - [20 Compute the profit generated by each customer based on their orders](#Compute-the-profit-generated-by-each-customer-based-on-their-orders)
  - [21 Compute the revenue generated by each sales representative based on the orders from the customers they serve](#Compute-the-revenue-generated-by-each-sales-representative-based-on-the-orders-from-the-customers-they-serve)
  - [22 Compute the profit generated by each sales representative based on the orders from the customers they serve](#Compute-the-profit-generated-by-each-sales-representative-based-on-the-orders-from-the-customers-they-serve)
  - [23 Compute the revenue generated by each product, sorted by product name](#Compute-the-revenue-generated-by-each-product)
  - [24 Compute the profit generated by each product line, sorted by profit descending](#Compute-the-profit-generated-by-each-product-line)
  - [25 Compute the ratio for each product of sales for 2003 versus 2004](#Compute-the-ratio-for-each-product-of-sales-for-2003-versus-2004)
  - [26 Compute the ratio of payments for each customer for 2003 versus 2004](#Compute-the-ratio-of-payments-for-each-customer-for-2003-versus-2004)
  - [27 Find the products sold in 2003 but not 2004](#Find-the-products-sold-in-2003-but-not-2004)
  - [28 Find the customers without payments in 2003](#Find-the-customers-without-payments-in-2003)
  
## General queries
  
#### Who is at the top of the organization 
```sql
SELECT e.employeeNumber, CONCAT(firstName,' ',lastName) AS full_Name FROM employees e  WHERE reportsTo is NULL;
```

#### Who reports to William Patterson
```sql
-- 主要体现复制两个表的思路 reportto_number = employe_enumber
SELECT e1.employeeNumber, CONCAT(e1.firstName,' ',e1.lastName) AS full_Name FROM employees e1, employees e2
WHERE e1.reportsTo = e2.employeeNumber
AND e2.firstName = 'William'
AND e2.lastName = 'Patterson';
```

#### List all the products purchased by Herkku Gifts
```sql
SELECT  DISTINCT od.productCode, p.productName FROM customers c, orders o, orderdetails od, products p
WHERE c.customerNumber = o.customerNumber
AND o.orderNumber = od.orderNumber
AND od.productCode = p.productCode
AND c.customerName = 'Herkku Gifts';
```

#### Compute the commission for each sales representative
```sql
-- commission 代表佣金 这题不需要用 title = sales rep 仔细推敲下
-- Assuming the commission is 5% of the value of an order. Sort by employee last name and first name
SELECT CONCAT(e.firstName,' ',e.lastName) AS full_Name, 
ROUND(SUM((quantityOrdered * priceEach) * 0.05),2) AS sales_commission
FROM employees e, customers c, orders o, orderdetails od
WHERE e.employeeNumber = c.salesRepEmployeeNumber
AND c.customerNumber = o.customerNumber
AND o.orderNumber = od.orderNumber
GROUP BY e.firstName, e.lastName
ORDER BY e.lastName, e.firstName ASC;
```

#### what is the difference in days between the most recent and oldest order date in the Orders file
```sql
-- 注意时间函数DAY()的用法
SELECT DAY(MAX(orderDate) - MIN(orderDate)) AS biggest_Dif FROM orders;
```

#### Compute the average time between order date and ship date for each customer ordered by the largest difference
```sql
-- 计算的是每一组的平均差值排序 不是最大最小值的极差
SELECT ROUND(AVG(DAY(o.shippedDate - o.orderDate)),0) AS day_Diff, c.customerName
FROM orders o, customers c
WHERE c.customerNumber = o.customerNumber
GROUP BY c.customerNumber
ORDER BY day_Diff DESC;
```

#### What is the value of orders shipped in August 2004
```sql
SELECT SUM(quantityOrdered * priceEach) AS ship_Value 
FROM orders o, orderdetails od
WHERE o.orderNumber = od.orderNumber
AND o.shippedDate LIKE '%2004-08%';
```

#### Compute the total value ordered, total amount paid and their difference for each customer for orders placed in 2004 and payments received in 2004 
Hint; Create views for the total paid and total ordered).
```sql
-- 注意view 创建和删除的用法 
-- 第一个是create value ordered
DROP VIEW IF EXISTS order_Value;

CREATE VIEW order_Value AS
SELECT SUM(quantityOrdered * priceEach) AS order_Value, customerNumber FROM orders o, orderdetails od
WHERE o.orderNumber = od.orderNumber
AND YEAR(o.orderDate) = '2004'
GROUP BY customerNumber
ORDER BY customerNumber;

-- 第二个是create amount paid
DROP VIEW IF EXISTS amount_Paid;

CREATE VIEW amount_Paid AS
SELECT SUM(amount) AS amount_Paid, customerNumber FROM payments p
WHERE YEAR(paymentDate) = '2004'
GROUP BY customerNumber
ORDER BY customerNumber;

-- 第三个是create amount paid

-- solution1 两个view差值取出来
SELECT order_Value - amount_Paid AS diff, tp.customerNumber
FROM amount_Paid tp, order_Value td
WHERE tp.customerNumber = tp.customerNumber
GROUP BY tp.customerNumber;

-- solution2 create total_Dif
DROP VIEW  IF EXISTS total_Dif;

CREATE VIEW total_Dif AS
SELECT SUM(quantityOrdered * priceEach) AS order_Value, o.customerNumber, 
SUM(amount) AS amount_Paid, o.customerNumber,
SUM(quantityOrdered * priceEach - amount) AS total_Dif
FROM orders o, orderdetails od, payments p
WHERE o.orderNumber = od.orderNumber
AND YEAR(o.orderDate) = '2004'
GROUP BY o.customerNumber
ORDER BY o.customerNumber;
```

#### List the employees who report to those employees who report to Diane Murphy
Use the CONCAT function to combine the employee's first name and last name into a single field for reporting
```sql
SELECT CONCAT(e1.firstName,' ',e1.lastName) AS full_Name, 
CONCAT(e2.firstName,' ',e2.lastName) AS report_To,
CONCAT(e3.firstName,' ',e3.lastName) AS top_Level
FROM employees e1, employees e2, employees e3
WHERE e1.reportsTo = e2.employeeNumber
AND e2.reportsTo = e3.employeeNumber
AND e3.firstName = 'Diane'
AND e3.lastName = 'Murphy';
```

#### What is the percentage value of each product in inventory sorted by the highest percentage first
```sql
-- 把子语句作为一个表来搜索查询

SELECT CONCAT(ROUND(((quantityInStock/t.total_Inventory) * 100),2), '%') AS percentage, p.productCode, p.productName
fROM products p, (SELECT SUM(quantityInStock) AS total_Inventory FROM products) AS t
ORDER BY percentage DESC;
```
#### Write a function to convert miles per gallon to liters per 100 kilometers
```sql
-- DETERMINISTIC是确定的，意思就是写入binlog的时候，写入的是一个指定的常量；如unix_timestamp()获取到的值是1，可能写入binlog的时候，unix_timestamp()获取到的时间戳却成了3了,这个时候会出现数据不一致问题,所以引入了DETERMINISTIC
-- 需要掌握自己写方程的步骤
CREATE FUNCTION `Convert_mg_to_kl` (mg INT)
RETURNS INT
DETERMINISTIC
BEGIN
DECLARE kl INT;
SET kl = 0;
SET kl = mg / 3.78541 * 1.60934 / 100;
RETURN kl;
END
```

#### Write a procedure to increase the price of a specified product category by a given percentage. 
You will need to create a product table with appropriate data to test your procedure. Alternatively, load the ClassicModels database on your personal machine so you have complete access. You have to change the DELIMITER prior to creating the procedure.
```sql
???没有设置定界符
-- delimiter表示定界符的意思 需要修改默认设置重新定义
CREATE DEFINER=`root`@`localhost` PROCEDURE `increase_price`(IN product_name VARCHAR(70), percentage DECIMAL)
BEGIN
UPDATE products
SET buyPrice = buyPrice * (1 + percentage)
WHERE productName = product_name;
END
```

#### What is the value of orders shipped in August 2004
```sql
-- solution1 与 solution2答案不一样

solution1

SELECT SUM(table1.shipped_Value) AS total_Value FROM
(SELECT SUM(quantityOrdered * priceEach) AS shipped_Value, shippedDate FROM orders o, orderdetails od
WHERE o.orderNumber = od.orderNumber
AND shippedDate BETWEEN '2004-08-01' AND '2004-08-31'
GROUP BY shippedDate
ORDER BY shippedDate) AS table1;

solution2

SELECT ROUND(SUM(quantityOrdered * priceEach),2) AS od_shipped FROM(
SELECT YEAR(o.shippedDate) AS YR, od.quantityOrdered, od.priceEach
FROM orderdetails od, orders o
WHERE od.orderNumber = o.orderNumber) AS TB1
WHERE YR = 2004;
```

#### What is the ratio the value of payments made to orders received for each month of 2004
(i.e., divide the value of payments made by the orders received)
```sql
SELECT MONTH(o.orderDate) AS MONTH, ROUND(SUM(p.amount) / SUM(od.quantityOrdered * od.priceEach),2) AS month_Ratio
FROM payments p, orders o, orderdetails od
WHERE p.customerNumber = o.customerNumber
AND o.orderNumber = od.orderNumber
AND YEAR(o.orderDate) = '2004'
AND YEAR(p.paymentDate) = '2004'
AND MONTH(o.orderDate) = MONTH(p.paymentDate)
GROUP BY MONTH(o.orderDate)
ORDER BY MONTH;
```

#### What is the difference in the amount received for each month of 2004 compared to 2003
```sql
SELECT ROUND((SUM(od4.quantityOrdered * od4.priceEach) - SUM(od3.quantityOrdered * od3.priceEach)),2) AS amount_Diff, 
MONTH(o3.orderDate) AS MONTH
FROM orders o3, orders o4, orderdetails od3, orderdetails od4
WHERE o3.orderNumber = od3.orderNumber
AND o4.orderNumber = od4.orderNumber
AND YEAR(o3.orderDate) = '2003'
AND YEAR(o4.orderDate) = '2004'
AND MONTH(o3.orderDate) = MONTH(o4.orderDate)
GROUP BY MONTH(o3.orderDate)
ORDER BY MONTH;
```

#### Write a procedure to report the amount ordered in a specific month and year for customers containing a specified character string in their name
-- locate(substr,str)
   含义:返回子串 substr 在字符串 str 中第一次出现的位置。如果子串 substr 在 str 中不存在，返回值为 0
   eg: 存在子串,locate('jd','jdgood')=1
   不存在子串,locate('jd','jingdong')=0
   原文链接：https://blog.csdn.net/u011900448/article/details/52176311
```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `amount_Order`(IN month INT, year YEAR, customer VARCHAR(50))
BEGIN
SELECT ROUND(SUM(od.quantityOrdered * od.priceEach),2) AS amount, customerName, year, month
FROM orders o, orderdetails od, customers c
WHERE o.orderNumber = od.orderNumber
AND o.customerNumber = c.customerNumber
AND MONTH(orderDate) = month
AND year = YEAR(orderDate)
AND LOCATE(customer, c.customerName) != 0
GROUP BY customerName;
END
```

#### Write a procedure to change the credit limit of all customers in a specified country by a specified percentage
```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `change_Limit`(IN country VARCHAR(70), percentage DECIMAL)
BEGIN
UPDATE customers
SET creditLimit = creditLimit * (1 + percentage)
WHERE country = customers.country;
END
```

#### Report the names of products that appear in the same order ten or more times
Basket of goods analysis: A common retail analytics task is to analyze each basket or order to learn what products are often purchased together
-- solution1,2结果不一样

solution1
```sql
SELECT COUNT(p.productCode) AS show_Time, p.productCode, p.productName, od.orderNumber
FROM  orderdetails od, products p
WHERE od.productCode = p.productCode
GROUP BY od.orderNumber
HAVING COUNT(p.productCode) >= 10
ORDER BY show_Time DESC;
```

solution2
```sql
SELECT DISTINCT productName, TB1.prod_count, productCode, productName FROM(
SELECT DISTINCT pd.productName, od1.productCode, od1.orderNumber, COUNT( od1.productCode) as prod_count 
FROM orderdetails od1
INNER JOIN orderdetails od ON od.orderNumber = od1.orderNumber
INNER JOIN products pd ON pd.productCode = od1.productCode
GROUP BY od1.productCode, od1.orderNumber
HAVING COUNT(od1.productCode) >= 10 ) AS TB1
ORDER BY TB1.prod_count DESC;
```

#### Compute the revenue generated by each customer based on their orders
Also, show each customer's revenue as a percentage of total revenue. Sort by customer name.(ABC reporting)
```sql
SELECT CONCAT(ROUND(SUM(quantityOrdered * priceEach)/
(SELECT (SUM(quantityOrdered * priceEach)) FROM orderdetails) * 100,2),'','%') AS revenus_Percentage, 
c.customerName, o.orderNumber
FROM orders o, orderdetails od, customers c
WHERE o.orderNumber = od.orderNumber
AND o.customerNumber = c.customerNumber
GROUP BY o.orderNumber
ORDER BY o.orderNumber;
```

#### Compute the profit generated by each customer based on their orders
Also, show each customer's profit as a percentage of total profit. Sort by profit descending.
```sql
SELECT DISTINCT c.customerNumber, c.customerName, CONCAT(ROUND(SUM(od.quantityOrdered * (od.priceEach - pd.buyPrice))/tb1.total_revenue * 100, 2) , '%') AS percent_revenue
FROM customers c, orderdetails od, orders o, products pd,
(SELECT SUM(od.quantityOrdered * (od.priceEach - pd.buyPrice)) AS total_revenue
FROM orderdetails od, products pd WHERE pd.productCode = od.productCode) as tb1
WHERE c.customerNumber = o.customerNumber 
AND od.orderNumber = o.orderNumber
AND pd.productCode = od.productCode
GROUP BY c.customerNumber, c.customerName
ORDER BY percent_revenue DESC;
```

#### Compute the revenue generated by each sales representative based on the orders from the customers they serve
```sql
SELECT ROUND(SUM(od.quantityOrdered * od.priceEach),2) AS eachrep_Rvenue, e.employeeNumber, e.firstName, e.lastName, e.jobTitle
FROM orders o, orderdetails od, customers c, employees e
WHERE o.orderNumber = od.orderNumber
AND o.customerNumber = c.customerNumber
AND c.salesRepEmployeeNumber = e.employeeNumber
GROUP BY e.employeeNumber
ORDER BY eachrep_Rvenue DESC;
```

#### Compute the profit generated by each sales representative based on the orders from the customers they serve
Sort by profit generated descending
```sql
SELECT ROUND(SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)),2) AS eachrep_Profit, e.employeeNumber, e.firstName, e.lastName, e.jobTitle
FROM orders o, orderdetails od, customers c, employees e, products p
WHERE o.orderNumber = od.orderNumber
AND o.customerNumber = c.customerNumber
AND c.salesRepEmployeeNumber = e.employeeNumber
AND p.productCode = od.productCode
GROUP BY e.employeeNumber
ORDER BY eachrep_Profit DESC;
```

#### Compute the revenue generated by each product
Sorted by product name
```sql
SELECT ROUND(SUM(od.quantityOrdered * od.priceEach),2) AS product_Revenue, p.productCode, p.productName
FROM orderdetails od, products p
WHERE p.productCode = od.productCode
GROUP BY p.productName
ORDER BY p.productName DESC;
```

#### Compute the profit generated by each product line 
Sorted by profit descending
```sql
SELECT ROUND(SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)),2) AS productLine_Profit, po.productLine
FROM orderdetails od, products p, productlines po
WHERE p.productCode = od.productCode
AND  p.productLine = po.productLine
GROUP BY po.productLine
ORDER BY productLine_Profit DESC;
```

#### Compute the ratio for each product of sales for 2003 versus 2004 (Same as Last Year (SALY) analysis)
```sql
SELECT CONCAT(ROUND(tb1.sales /tb2.sales *100, 2), '%') AS ratio, tb1.productCode, tb1.productName 
FROM 
(SELECT SUM(od.quantityOrdered * od.priceEach) AS sales, od.productCode, pd.productName
FROM orderdetails od, orders o, products pd
WHERE YEAR(o.orderDate) = 2003
AND o.orderNumber = od.orderNumber
AND od.productCode = pd.productCode
GROUP BY od.productCode, pd.productName
) AS tb1, 
(SELECT SUM(od.quantityOrdered * od.priceEach) AS sales, od.productCode, pd.productName
FROM orderdetails od, orders o, products pd
WHERE YEAR(o.orderDate) = 2004
AND o.orderNumber = od.orderNumber
AND od.productCode = pd.productCode
GROUP BY od.productCode, pd.productName) AS tb2;
```

#### Compute the ratio of payments for each customer for 2003 versus 2004
```sql
SELECT Tab_03.pay_03, Tab_04.pay_04, CONCAT(ROUND((Tab_03.pay_03 / Tab_04.pay_04) * 100, 2), '%') AS Ratio, Tab_03.customerNumber, Tab_03.customerName 
FROM (SELECT SUM(p.amount) AS pay_03, c.customerNumber, c.customerName FROM payments p, customers c, orders o
WHERE c.customerNumber = p.customerNumber
AND o.customerNumber = c.customerNumber
AND YEAR(p.paymentDate) = 2003
AND YEAR(o.orderDate) = 2003
GROUP BY c.customerNumber, c.customerName) AS Tab_03, 
(SELECT SUM(p.amount) AS pay_04, c.customerNumber, c.customerName FROM payments p, customers c, orders o
WHERE c.customerNumber = p.customerNumber
AND o.customerNumber = c.customerNumber
AND YEAR(p.paymentDate) = 2004
AND YEAR(o.orderDate) = 2004
GROUP BY c.customerNumber, c.customerName) AS Tab_04
WHERE Tab_03.customerNumber = Tab_04.customerNumber
ORDER BY ratio DESC;
```

#### Find the products sold in 2003 but not 2004
-- 题目原意是找出03年卖掉但是04年没有卖的商品
```sql
SELECT p.productCode, p.productName, o.orderDate FROM orders o, orderdetails od, products p
WHERE o.orderNumber = od.orderNumber
AND od.productCode = p.productCode
AND YEAR(o.orderDate) = 2003
AND p.productCode NOT IN (SELECT p.productCode FROM orders o, orderdetails od, products p
WHERE o.orderNumber = od.orderNumber
AND od.productCode = p.productCode
AND YEAR(o.orderDate) = 2004)
ORDER BY o.orderDate;
```

#### Find the customers without payments in 2003
```sql
SELECT DISTINCT p.customerNumber, c.customerName, p.paymentDate, p.amount FROM payments p, customers c
WHERE p.customerNumber = c.customerNumber
AND p.customerNumber NOT IN 
(SELECT p.customerNumber FROM payments p, customers c
WHERE p.customerNumber = c.customerNumber
AND YEAR(p.paymentDate) = 2003
ORDER BY p.paymentDate)
ORDER BY p.paymentDate;
```

- [Correlated subqueries](#Correlated-subqueries)
  - [1 Who reports to Mary Patterson](#Who-reports-to-Mary-Patterson)
  - [2 Which payments in any month and year are more than twice the average for that month and year](#Which-payments-in-any-month-and-year-are-more-than-twice-the-average-for-that-month-and-year)
  - [3 The percentage value of each product stock on hand as a percentage of the stock on hand for product line to which it belongs](#The-percentage-value-of-each-product-stock-on-hand-as-a-percentage-of-the-stock-on-hand-for-product-line-to-which-it-belongs)
  - [4 For orders containing more than two products, report those products that constitute more than 50% of the value of the order](#For-orders-containing-more-than-two-products-and-report-those-products-that-constitute-more-than-50-percent-of-the-value-of-the-order)

## Correlated subqueries

#### Who reports to Mary Patterson
```sql
SELECT e1.employeeNumber, CONCAT(e1.firstName,' ',e1.lastName) AS full_Name, e1.reportsTo FROM employees e1, employees e2
WHERE e1.reportsTo = e2.employeeNumber
AND e2.firstName = 'Mary'
AND e2.lastName = 'Patterson';
```

#### Which payments in any month and year are more than twice the average for that month and year
(i.e. compare all payments in Oct 2004 with the average payment for Oct 2004)? Order the results by the date of the payment. You will need to use the date functions.
```sql
SELECT p.checkNumber, YEAR(p.paymentDate) AS yr, MONTH(p.paymentDate) as mt, p.amount AS above_Avg, p1.avg
FROM payments p, (SELECT YEAR(paymentDate) AS year, MONTH(paymentDate) AS month, AVG(amount) AS avg FROM payments 
GROUP BY year, month) AS p1
WHERE p.amount > 2 * p1.avg
AND YEAR(p.paymentDate) = p1.year
AND MONTH(p.paymentDate) = p1.month
GROUP BY yr, mt
ORDER BY yr ASC, mt ASC;
```

#### The percentage value of each product stock on hand as a percentage of the stock on hand for product line to which it belongs
Order the report by product line and percentage value within product line descending. Show percentages with two decimal places
```sql
SELECT CONCAT(ROUND(p.quantityInStock / p1.quantity_Productline * 100, 2), '%') AS quantity_Percenntage, p.productName, p1.productLine 
FROM products p,
(SELECT ROUND(SUM(p.quantityInStock),2) AS quantity_Productline, po.productLine FROM products p, productlines po
WHERE p.productLine = po.productLine
GROUP BY po.productLine) AS p1
WHERE p1.productLine = p.productLine
GROUP BY p.productLine, p.productName
ORDER BY p.productLine, quantity_Percenntage DESC;
```

#### For orders containing more than two products and report those products that constitute more than 50 percent of the value of the order
```sql
SELECT o.productCode, o.orderNumber, Tab1.Value_Sum, ROUND((o.quantityOrdered * o.priceEach),2) AS product_Value
FROM orderdetails o,
(SELECT SUM(od.quantityOrdered * od.priceEach) AS Value_Sum, COUNT(DISTINCT productCode) AS count, o.orderNumber 
FROM orders o, orderdetails od
WHERE o.orderNumber = od.orderNumber
GROUP BY o.orderNumber
HAVING count > 2
ORDER BY o.orderNumber) Tab1
WHERE o.orderNumber = Tab1.orderNumber
AND o.priceEach * o.quantityOrdered > 0.5 * Tab1.Value_Sum
GROUP BY o.orderNumber
ORDER BY o.orderNumber;
```

- [Spatial data](#Spatial-data)
  - [1 Which customers are in the Southern Hemisphere](#Which-customers-are-in-the-Southern-Hemisphere)
  - [2 Which US customers are south west of the New York office](#Which-US-customers-are-south-west-of-the-New-York-office)
  - [3 Which customers are closest to the Tokyo office](#Which-customers-are-closest-to-the-Tokyo-office)
  - [4 Which French customer is furthest from the Paris office](#Which-French-customer-is-furthest-from-the-Paris-office)
  - [5 Who is the northernmost customer](#Who-is-the-northernmost-customer)
  - [6 What is the distance between the Paris and Boston offices](#What-is-the-distance-between-the-Paris-and-Boston-offices)
  
## Spatial data(空间数据)
The Offices and Customers tables contain the latitude(纬度) and longitude(经度) of each office and customer in officeLocation and customerLocation, respectively, in POINT format. Conventionally, latitude and longitude and reported as a pair of points, with latitude first.
A **negative latitude** means **South** of the Equator(赤道), and a **negative longitude** means **West** of the Prime Meridian(子午线)

#### Which customers are in the Southern Hemisphere(南半球)
ST_X(代表纬度) ST_Y(代表经度)
```sql
SELECT ST_X(customerLocation) AS Latitude, ST_Y(customerLocation) AS Longitude, customerName
FROM classicmodels.customers
WHERE  ST_X(customerLocation) < 0;
```

#### Which US customers are south west of the New York office
```sql
SELECT ST_X(customerLocation) AS Latitude, ST_Y(customerLocation) AS Longitude, customerName, o.state
FROM classicmodels.customers c, Offices o
WHERE  ST_X(customerLocation) < ST_X(officeLocation)
AND ST_Y(customerLocation) < ST_Y(officeLocation)
AND o.state = 'NY';
```

#### Which customers are closest to the Tokyo office 
(i.e., closer to Tokyo than any other office)

To be precise for long distances, the distance in kilometers, as the crow flies, between two points when you have latitude and longitude, is (ACOS(SIN(lat1*PI()/180)*SIN(lat2*PI()/180)+COS(lat1*PI()/180)*COS(lat2*PI()/180)* COS((lon1-lon2)*PI()/180))*180/PI())*60*1.8532(有经纬度计算两点间的距离)

```sql
AS min_Dis, t1.customerName, o1.city, t1.postalCode FROM
(SELECT ST_X(customerLocation) AS lat1, ST_Y(customerLocation) AS lon1, 
ST_X(officeLocation) AS lat2, ST_Y(officeLocation) AS lon2, 
c.customerName, o.officeCode, c.postalCode FROM Customers c, Offices o) AS t1, Offices o1
WHERE o1.city = 'Tokyo'
HAVING min_Dis < 5
ORDER BY min_Dis, t1.customerName;
```

#### Which French customer is furthest from the Paris office
```sql
SELECT ROUND(((ACOS(SIN(t1.lat1*PI()/180)*SIN(t1.lat2*PI()/180)+COS(t1.lat1*PI()/180)*COS(t1.lat2*PI()/180)* COS((t1.lon1-t1.lon2)*PI()/180))*180/PI())*60*1.8532),2)
AS min_Dis, t1.customerName, t1.city, t1.postalCode FROM
(SELECT ST_X(customerLocation) AS lat1, ST_Y(customerLocation) AS lon1, 
ST_X(officeLocation) AS lat2, ST_Y(officeLocation) AS lon2, 
c.customerName, o.officeCode, c.postalCode, o.city FROM Customers c, Offices o
WHERE c.country = 'France'
AND o.city = 'Paris') AS t1
ORDER BY min_Dis DESC, t1.customerName ASC;
```

#### Who is the northernmost customer
-- 选择latitude值为正数且最大的customer即可 但是max就已经是最大的了 不需要>0的条件
```sql
SELECT MAX(ST_X(customerLocation)) AS lat, customerNumber, customerName FROM Customers;
```

#### What is the distance between the Paris and Boston offices

To be precise for long distances, the distance in kilometers, as the crow flies, between two points when you have latitude and longitude, is (ACOS(SIN(lat1*PI()/180)*SIN(lat2*PI()/180)+COS(lat1*PI()/180)*COS(lat2*PI()/180)* COS((lon1-lon2)*PI()/180))*180/PI())*60*1.8532

```sql
SELECT ROUND(ACOS(SIN(lat1*PI()/180)*SIN(lat2*PI()/180)+COS(lat1*PI()/180)*COS(lat2*PI()/180)* COS((lon1-lon2)*PI()/180))*180/PI()*60*1.8532, 1)  AS DIS
FROM
(SELECT ST_X(ofc1.officeLocation) AS lat1, ST_Y(ofc1.officeLocation) AS lon1,  ST_X(ofc.officeLocation) AS lat2, ST_X(ofc.officeLocation) AS lon2
FROM offices ofc1, offices ofc
WHERE ofc.city = 'Paris'
AND ofc1.city = 'Boston'
) AS tb1;
```

