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

```


