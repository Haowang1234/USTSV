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

```


