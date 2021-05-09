# SQL

## Postgres(Supports more SQL features than MySQL)
- Download Postgres database installer: install pgadmin separately-install command line tools- 5432 default port
- Download Postgres GUI: pg admin: launch- server group contains all servers- Default server= 'PostgreSQL 13' - Has 1 DB by default - postgres - to see tables - select schema-> tables - To create new database - rightclick on Databases -> enter name and also see SQL for it

## Basic Things to Know
- A server has databases | a database has tables
- A table has a specified number of columns but can have any number of rows.
- Primary Key: 
    - a column set, which contains only unique entries.
    - To ensure uniqueness of a row
    - 1 PK per table.
    - Can't be null
    - [Sololearn](https://www.sololearn.com/learning/1060/1849/3577/1). 
    - Eg: set UserID as primary key in table Users
    ```
    CREATE TABLE Users
    (
        UserID int, 
        FirstName varchar(50),
        LastName varchar(50),
        City varchar(50), 
        PRIMARY KEY (UserID)
    );
    ```
### Creating a column which doesn't exist while fetching data
- See example from VIEW Socratica. While fetching data from table martian_confidential we're creating a new column on the go with value 'Martian' and giving it a label status. Note: status' datatype is automatically decided (text)
```
SELECT first_name, last_name, martian_id, 'Martian' AS status
FROM martian_confidential;
```
- If don't label column AS status, then default name- ?column?
### In UNION by default the name of columns of first query will be displayed in final result
- See example Socratica VIEW. martian_confidential UNION visitor, all column names are according to martian_confidential's column names
```
SELECT first_name, last_name, martian_id
FROM martian_confidential
UNION
SELECT first_name, last_name, visitor_id
FROM visitor;
```
- last col will be martian_id unless used AS

### SELECT produces results row by row
- See example 3 Socratica VIEW. The quantity gets DEFAULT value null if it can't find a matching base_id for that item or it can't find the item_id for the item, in the inventory table. In order to show 0 instead of NULL values in quantity column, COALESCE() function is used. It has been given 2 arguments, the first one is the quantity of items for that base and item_id, and the other one is 0.
```
SELECT b.base_id, s.supply_id, s.name, 
    COALESCE((SELECT quantity 
             FROM inventory
             WHERE base_id = b.base_id AND supply_id = s.supply_id), 0)
FROM base AS b
CROSS JOIN supply AS s;
```  
- Now, every row is created one by one. Eg- creation of first row: 1st row's value in column- b.base_id, s.supply_id, s.name is fetched. Then 1st row's value of column <subquery>(ie SELECT quantity .... s,supply_id) is checked for null - if isn't null, that value is returned and put in the row generated. If is null then 0 is returned and put in row generated.

## Importing a Table
- Have the excel file ready to import-eg: earthquakes.csv 
-   1. in pgAdmin: Tools - Query Tool
    2. paste this in the Query Editor:

    CREATE TABLE public.earthquake
    (
        earthquake_id integer NOT NULL,
        occurred_on timestamp without time zone,
        latitude numeric,
        longitude numeric,
        depth numeric,
        magnitude numeric,
        calculation_method character varying,
        network_id character varying,
        place character varying,
        cause character varying,
        CONSTRAINT earthquake_pkey PRIMARY KEY (earthquake_id)
    )
    WITH (
        OIDS = FALSE
    )

    3. now execute (F5)
    4. right-click the 'earthquake' table
    5. use Import (make sure the 'Header' switch is set to 'Yes')

## SQL Datatypes
- int: Z on number line, size not necessary to mention
- float(m,d): a real number, m = total digits, d = digits after decimal, both optional
- double(m,d): more precise real value
- date: YYYY-MM-DD
- datetime: YYYY-MM-DD HH:MM:SS
- timestamp: Stores both date and time. has a range of '1970-01-01 00:00:01' UTC to '2038-01-19 03:14:07' UTC(in MySQL)
- time: HH:MM:SS
- char(m): fixed length characters of m length
- varchar(m): variable length characters with max-size: m. m is optional
- blob: Binary Large Objects- stores images or other files -large binary data
- text: for large amt og text
- [mysql vs postgress](https://www.codegrepper.com/code-examples/sql/data+types+mysql+vs+postgresql)

- Postgres: numeric, character varying, timestamp, timestampz, see...

## SQL Constraints
- They are rules which the data residing in the columns in which they're applied must follow
- All constraints are applied after mentioning the datatype of column

### NOT NULL
- Doesn't allow that column to take NULL/null values

- Eg: username doesn't allow null values
```
username varchar(50) NOT NULL,
```

### PRIMARY KEY

### UNIQUE
- Doesn't allow that column to store duplicate data/entries.
- 2 or more columns in a table can also be set UNIQUE

### DEFAULT
- Specifies the DEFAULT value of that column in case no value is set by user for that column, when creating a new row

### CHECK
- Checks if the data entered in that column is valid or not based on the condition it holds
- Syntax(MySQL)
Basic
```
col-name datatype,
CHECK (col-name condition)
```
CHECK constraint for multiple columns with a label/name of that CHECK condition
```
col-1-name datatype,
col-2-name datatype,
CONSTRAINT label-name CHECK(col-1, col-2 condition)
```
- Eg: age should always be >= 18
```
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(100) NOT NULL,
    FirstName varchar(100),
    age int,
    City varchar(100),
    CHECK (age >= 18)
)
```
- Eg: age >= 18 and city should be Vice City
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(100) NOT NULL,
    FirstName varchar(100),
    age int,
    City varchar(100),
    CONSTRAINT CHL_Persons CHECK (age >= 18 AND City = 'Vice City')
);

## AUTO INCREMENT
- It generates a unique number(+ve Z) automatically for the column whenever a new row is created/new entry is required in the column.
- No need to fill in value for this column when creating a new row
- By default it starts generating numbers from 1 and increments by 1 thereafter.
- Often used in PRIMARY KEY column.

- Eg: 'id'' column auto increment by one each time a new row is inserted.
```
id int AUTO INCREMENT,
```
- Eg: set UserID as PK and AUTO INCREMENT it
```
UserID int NOT NULL AUTO INCREMENT,
PRIMARY KEY(UserID)
```
- Eg: constraints usage
```
CREAtE TABLE Users (
    id int NOT NULL AUTO INCREMENT,
    username varchar(100) NOT NULL,
    password varchar(100) NOT NULL,
    PRIMARY KEY (id)
);
```
 

## SQL Commands

> - All commands end with ;
> - We can run multiple SQL queries at once
> - SQL: case insistive, commonly written in UPPER CASE


## Basic Commands
- Basic syntax is:
```
(Scrotica)
SELECT <col-1>, <col-2>...
FROM <table-1>
WHERE <condition-1> AND <condition-2>
ORDER BY <col-i> (ASC | DESC)
LIMIT n;
```

### SELECT
- Retrieves data from one table/ multiple tables(using JOIN)
- Syntax
- SELECT `<column-name-1>, <column-name-2>... ` FROM `<table-name-1>, <table-name-2>... 
> SELECT FirstName, LastName, City FROM customers; 

> SELECT * FROM customers; - selects all columns and displays complete table

### ORDER BY
- used to sort column entries, used with SELECT. Default- ascending
> SELECT FirstName, LastName, City FROM customers ORDER BY FirstName;

> SELECT name, city FROM people ORDER BY id;

-Sorting Multiple Columns:
```
Using ORDER BY on more than one column will sort the 1st column first and if there are same entries in the sorted first column, the next colum in ORDER BY will decide which entry to display first, and so on.

SELECT * FROM customers ORDER BY LastName, Age;
```
- To sort in descending order use keyword DESC. To sort in ascending order use ASC keyword.
> SELECT FirstName, Salary FROM employee WHERE Salary > 10000 ORDER BY Salary DESC;

> SELECT * FROM items WHERE cost > 463 ORDER BY cost DESC;

- Eg: to find the maximum magnitude of earthquake which occurred in 2010. First get the list of all earthquakes in 2010, then sort in descending order. Use LIMIT if don't want all EQ in 2010
> SELECT * FROM earthquake WHERE occurred_on >= '2010-01-01' AND occurred_on <= '2010-12-31' ORDER BY magnitude DESC LIMIT 1;


### CREATE DATABASE
- Creates a database(owner is postgres)
```
CREATE DATABASE chocolate_brands
    WITH
    OWNER = postgres
    ENCODING  = 'UTF8'
    CONNECTION LIMIT = -1;
```
### SHOW
- SHOW DATABASES: shows all databases available
- SHOW TABLES: shows all tables in current database
- SHOW COLUMNS FROM `<table-name>`: shows following info about all columns in table | for MySQL only
    - Field: the column name
    - type: column datatype
    - Key: what type of key the column is
    - Default: default value of column
    - Null: If column's value is null
    - Extra: any additional info

### Change Database name
```
ALTER DATABASE chocolate_brands RENAME TO socratica_database;
```
### CREATE TABLE
- Creates a new table in database
- Syntax:
```
CREATE TABLE <table-name>
(
    col-1 datatype(size),
    col-2 datatype(size), 
    .
    . 
);
```
- Eg: create 'Users' with UserID, FirstName, LastName, City
```
CREATE TABLE Users
(
    UserID int, 
    FirstName varchar(50),
    LastName varchar(50),
    City varchar(50)
);
```

- Eg: create a table test with three columns: ''id'' as a primary key, username and password of type varchar
```
CREATE TABLE test 
(
    id int,
    username varchar(100),
    password varchar(100),
    PRIMARY KEY (id)
);
```


### INSERT INTO
- To add a new row in table
- When adding it, you've the option of omitting those columns which can insert a default value. (But can't omit columns not having any default value and also not supporting NULL)
- Order is important
- Syntax:
```
INSERT INTO <table-name> 
    (col-1, col-2,...)
VALUES
    (value1, value2,...);
```
OR
```
INSERT INTO <table-name>
VALUES
    (value1, value2,... );
```

- Eg: insert values-John Smith and MIT in columns of table students
```
INSERT INTO students
VALUES
    ('John Smith', 'MIT');
```
- Eg: Insert values- Peter Parker and Standford in name and university columns of table students
```
INSERT INTO students
    (name, university)
VALUES
    ('Peter Parker', 'Stanford');
```
- Eg: Insert values- 9, Samuel, Clark into columns ID, FirstName, LastName, Age in table Employees. Age has a default value.
```
INSERT INTO Employees
    (ID, FirstName, LastName)
Values
    (9, 'Samuel', 'Clark');
```

### UPDATE
- To update a column's data in the table
- Syntax
```
UPDATE <table-name>
SET col-1 = value-1, col-2 = value-2,...
WHERE <condition>
```
- Without WHERE, the whole column gets updated

- Eg: Change salary of John with ID 1 from 2000 to 5000 in table Employees
```
UPDATE Employees
SET Salary = 5000
WHERE ID = 1;
```
- update the 'students' table by changing the university's value to 'Stanford' if the student's name is 'John'
```
UPDATE students
SET university = 'Standford'
WHERE name = 'John';
```
- Eg: change the Salary to 1000 and FirstName to Star of ID 1 employee in Employees
```
UPDATE Employees
SET Salary = 1000, FirstName = 'Star'
WHERE ID = 1;
```
- Eg: update the ''name'' and ''age'' columns of the ''students'' table, set it to Peter and 32 for ID 147
```
UPDATE students
SET name = 'Peter', age = 32
WHERE id = 147;
```
### DELETE FROM
- Deletes a row from table
- Syntax:
```
DELETE FROM <table-name>
WHERE condition
```
- Without WHERE, all rows get deleted
- Eg: delete all rows from ''people'' in which the id falls in the range of 5 to 10, excluding them
```
DELETE FROM people
WHERE id > 5 AND id < 10;
```
### ALTER TABLE
- Modifies columns
#### ADD 
- Used to ADD, DROP or modify columns, and to add and drop CONSTRAINTS from them
- When ADD column, then its DEFAULT value is used
- Eg: to add a new column in table Persons, DateOfBirth
```
ALTER TABLE Persons
ADD DateOfBirth date;
```
- Eg: ADD column speciality to table students
```
ALTER TABLE students
ADD speciality varchar(100);
```
#### DROP COLUMN
- Deletes column
- Eg: Delete DateOfBirth
```
ALTER TABLE Persons
DROP COLUMN DateOfBirth;
```

#### RENAME TO 
- RENAME a column
- Eg: rename FirstName to name
```
ALTER TABLE People
RENAME FirstName TO name;
```

### RENAME TABLE TO
- RENAME a table
- Eg: rename Persons to Users
```
RENAME TABLE Persons TO Users;
```
- Eg: rename people to humans
```
RENAME TABLE people TO humans;
```

### DROP TABLE
- Deletes table completely
- Eg: delete Persons
```
DROP TABLE Persons;
```

## CREATE VIEW AS (Socratica)
- VIEW is a virtual table (means db doesn't have it explicitly like other tables) which can store data from multiple tables or the result of complex queries. (mine: it's like a variable which can store tabular data)
- Uses
    - Simplicity: Eliminates the need to write complex queries just to fetch the desired table, hence common queries on that table are simple, saves time
    - Security: restricts access to all data/ provides access to limited data/we can't fetch data which is not present in VIEW 
- VIEW is automatically updated when the data it holds is updated in table
- Syntax:
```
CREATE VIEW <view-name> AS
<any-query>
```
### Socratica Database
- Everything same as martiandb in JOIN example except that it has a martian_confidential table instead of martian which has 2 extra columns- salary and dna_id. I'm just adding a new table in martiandb instead of creating a new db.
- martian_confidential lists all people living(not visiting) on Mars
- Query to add martian_confidential and its values
```
Append in resources/Martian Database.txt
```
### Create VIEW martian_public to prevent access to sensitive data in martian_confidential- salary and dna_id
```
SELECT martian_id, first_name, last_name, base_id, super_id
FROM martian_confidential;
```
Then put it in a view
```
CREATE VIEW martian_public AS
SELECT martian_id, first_name, last_name, base_id, super_id
FROM martian_confidential;
```
### create VIEW people_on_mars of ALL people living on Mars(martian + visitor) containing their first name, last name, unique ID and status(martian/visitor)
- Need to merge/UNION martian_confidential and visitor. Create a new column status. 
```
SELECT martian_id, first_name, last_name, 'Martian' AS status
FROM martian_public
UNION
SELECT visitor_id, first_name, last_name, 'Visitor' AS status
FROM visitor;
```
- Change name of column martian_id to id, also differentiate martian and visitor id
```
SELECT CONCAT('m', martian_id) AS id, first_name, last_name, 'Martian' AS status
FROM martian_public
UNION
SELECT CONCAT('v', visitor_id) AS id, first_name, last_name, 'Visitor' AS status
FROM visitor;
```
- CREATE VIEW AS
```
CREATE VIEW people_on_mars AS
SELECT CONCAT('m', martian_id) AS id, first_name, last_name, 'Martian' AS status
FROM martian_public
UNION
SELECT CONCAT('v', visitor_id) AS id, first_name, last_name, 'Visitor' AS status
FROM visitor;   
```
### Create VIEW of ALL the bases (base_id), quantities of ALL the supply items(supply_id) on the bases with their names
- Note: base #5 doesn't have any item, hence is not included in inventory. Inventory only shows quantity of items available on each base (means those items which are atleast 1 in quantity). I need 4 columns - base_id, supply_id, name and quantity. Means for every base, I need all 10 items' supply_id and name- hence do CROSS JOIN.

```
SELECT b.base_id, s.supply_id, s.name
FROM base AS b
CROSS JOIN supply AS s;
```
- For last column- quantity - fetch data from inventory, which tells which base has which all _available_ supply_id and its quantity 

```
SELECT b.base_id, s.supply_id, s.name, 
	   (SELECT quantity 
	   FROM inventory
	   WHERE base_id = b.base_id AND supply_id = s.supply_id)
FROM base AS b
CROSS JOIN supply AS s;
```
- Now replace null with 0 and create a VIEW, rename coalesce column to quantity

```
CREATE VIEW base_storage AS
SELECT b.base_id, s.supply_id, s.name, 
	   COALESCE((SELECT quantity 
	   FROM inventory
	   WHERE base_id = b.base_id AND supply_id = s.supply_id), 0) 
       AS quantity
FROM base AS b
CROSS JOIN supply AS s;
```

cant drop columns
can add columns in view
cant rename a columns name using create/replace

## Modifying VIEW

### Dropping a column
- Not supported in Postgres(MySQL- don't know)

### Adding column
- If earlier VIEW List has FIrstName and Salary ,then can LastName also
```
CREATE OR REPLACE VIEW List AS
SELECT FirstName, LastName, Salary
FROM Employees;
```

### Renaming column
- Eg: See Scoratica VIEW example 3. RENAME COLUMN quantity to item_quantity in VIEW base_storage 
```
ALTER VIEW base_storage 
RENAME COLUMN quantity
TO item_quantity;
```

## Keywords

### DISTINCT
- DISTINCT: is used with SELECT to fetch only distinct values of a particular column
> SELECT DISTINCT City FROM customers;

> NOTE: SELECT DISTINCT City, ID FROM customers; returns  complete list as as all IDs must be showed, hence repeated City will also appear

> SELECT DISTINCT name FROM students ORDER BY name;

### LIMIT
- LIMIT: used to restrict the number of column entries displayed
> SELECT FirstName, LastName FROM customers LIMIT 3;  will show only 3 entries contrary to default which shows all entries


### OFFSET
- OFFSET: is like what index-number is to an array. It is the index-number of entries in a column with 1st column entry having OFFSET value of 0. It is used to SELECT/pick up the records starting from a particular OFFSET.

> SELECT ID, FirstName, LastName, City FROM customers OFFSET 3 LIMIT 4;

- Shorthand version of same code above (not supported in postgres): 
> SELECT ID, FirstName, LastName, City FROM customers LIMIT 4, 12;

### AS
- AS: CONCAT function (or using other things like arithmetic operators)results in a new column named CONCAT(...)(or other) by default, so to name this column, use AS.

> SELECT CONCAT(FirstName, ',', LastName) AS Name FROM customers;

- See 'Custom Table Names in Join'

### DESC/ASC
- DESC/ASC: used with order by to specify order. ASC not mandatory.

### LIKE
- Used when we have a _search condition_ inside/after a where clause
- Eg: Listing some columns WHERE city column ends with the letter 'd'.
- Syntax:
```
SELECT <col-name> FROM <table-name> WHERE <col-name> LIKE 'pattern';
```
- `pattern` is basically the search condition. Inside `pattern` we're allowed to use two symbols (any number of times):
    1. %: represents all characters _(if present)_
    2. _: represents any _single_ character. 
- Eg: to display all employees whose name starts with 'A'
> SELECT * FROM employees WHERE FirstName LIKE 'A%';

- Eg: to display all employees whose name ends with 's'
> SELECT * FROM employees WHERE LASTNAME LIKE '%s';

- Eg: Displays all employees:
> SELECT * FROM employees WHERE FirstName LIKE '%';

- Eg: Displays id WHERE the column name ends with the string 'boxes'.
> SELECT seller_id FROM items WHERE name LIKE '%boxes';

## Fully Qualified Column Names
There can be a few tables having columns with same names, so its better to write fully qualified column names.
Write
> SELECT customers.FirstName FROM customers;

Instead of
> SELECT FirstName FROM customers;

## Filtering 

- WHERE: is used to extract only those column entries that fulfill a particular condition

> SELECT id, name FROM students WHERE id = 23;

## SQL Operators

Comparison Operators
1. =  Equal
1. != Not Equal
1. `>` Greater than
1. < Less than
1. `>=` Greater than or equal
1. <= Less than or equal
1. BETWEEN Between an inclusive range. First value must be lower bound and second the upper.

Logical operators
1. AND 
1. OR
1. IN True if operand is equal to one of a list of expressions/ Its a replacement of OR operator.
1. NOT

Arithmetic Operators
1. `+`
1. `-`
1. `*`
1. /

> SELECT id, name FROM students WHERE id >= 12;

> SELECT id, name FROM students WHERE id BETWEEN 13 AND 45;

> SELECT name, state FROM customers WHERE state IN('CA', 'NY', 'NC');

> SELECT * FROM customers 
WHERE City = 'New York'
OR City = 'Los Angeles'
OR City = 'Chicago';

> SELECT name, state FROM customers WHERE state NOT IN('CA', 'NY');

Displays a table where salary is 500 more than actual. The actual table is not changed.
> SELECT FirstName, LastName, Salary + 500 AS Salary FROM customers;

## Text Values

When working with strings as column entries, use single quotes to surround them in order to include them in your query.

If string contains a single quote, to escape it use a double quote at its place. Eg: 'can"t'

> SELECT * FROM people WHERE city = 'Boston';

> SELECT * FROM customers WHERE state = 'CA' AND city = 'Hollywood'; 

> SELECT name, state, city FROM customers WHERE state = 'CA' OR city = 'Boston';


## Conjunctive Operators

- AND combined with OR = conjunctive operators
- Use parentheses to know ordering

> SELECT * FROM customers where (id = 1 OR id = 2) AND city = 'Boston';

## Functions

## COUNT()
- Returns the count of a column
- Eg: to find total number of earthquakes
> SELECT COUNT(*) FROM earthquake;

OR

> SELECT COUNT(magnitude) FROM earthquake;

- COUNT is faster than displaying all columns from earthquake

## CONCAT()
It is used to concatenate two or more strings and display them. (Data type can be different also, just like we can concatenate different datatypes in java System.out.println)

> SELECT CONCAT(FirstName, ',', LastName) FROM customers;

> SELECT CONCAT(city, ',', state) AS new_address FROM customers;

> SELECT CONCAT('m', martian_id) AS id FROM martian_public;

## UPPER and LOWER()
Converts all _alphabets_ in the specified string to upper/lowercase 

> SELECT FirstName, UPPER(LastName) AS LastName FROM customer;

## SQRT, AVG, POWER()
- SQRT: finds the square root of the specified value

> SELECT Salary, SQRT(Salary) FROM employees;

- AVG: finds the average of the specified column.

> SELECT AVG(Salary) FORM employees;

- POWER: finds the result when the specified value is raised to an exponent power.(less better than SQRT for finding sq roots)

## SUM()
- Used to sum up ALL(till now) the values of a column.
- Eg
> SELECT SUM(salaries) FROM employees;

## MIN()/ MAX()
- Used to find the minimum/ maximum value in a column.
- Eg:
> SELECT MIN(Salary) AS Salary FROM employees;

## COALESCE()
- Returns the first non-null value present in its arguments
- Syntax:
```
COALESCE(value-1, value-2,...value-n)
```
- Working:
    - Evaluates arguments one by one
-Eg
```
SELECT COALESCE(null, null 'star');
```
> OP: star
- Eg
```
SELECT COALESCE (NULL,NULL,NULL,NULL,1,'star');
```
> OP: 1

- Eg:
```
SELECT COALESCE (NULL,NULL,NULL,NULL,'star',1);
```
> OP: Error- cant convert varchar to int: Why- see

## Subqueries
- Is a query within a query.
- Eg: select the name and salaries of all those employees whose salaries are greater than the average. First calculate the average salary, then use it.
> SELECT AVG(salary) FROM employees;

Got answer: 3100

> SELECT FirstName, Salary FROM employees WHERE salary > 3100 ORDER BY salary DESC;

- To make a single subquery - combine both, with subquery in parentheses with NO semicolon in the subquery. 
> SELECT FirstName, Salary FROM employees WHERE Salary > (SELECT AVG(Salary) FROM employees) ORDER BY Salary DESC;

```
Note: SELECT FirstName, Salary FROM employees WHERE Salary > AVG(Salary) ORDER BY Salary DESC;
Would be WRONG
```

- Eg:
> SELECT * FROM items WHERE cost > (SELECT AVG(cost) FROM items);

## Joining Tables
- SQL allows queries to fetch data from more than one table, hence creating a temporary table using all such tables.
- To create link between tables, we can reference a colum present in the referenced table in the referring table/reference table. More efficient than creating the same column redundantly in the other table.
- Eg: tables: customers and orders. orders contains a reference to the Customer_ID column in the customer table rather than having FirstName and LastName again in its table

## Joining simply using WHERE
- Use fully qualified column names as column names may be same in diff tables
- Multiple tables can be sepecified in FROM by using comma to separate them
- Eg: Display Customers Name, ID and _his_ orders Name and Amount 
> SELECT customers.ID, customers.Name, orders.Name, orders.Amount FROM customers, orders WHERE customers.ID = orders.Customer_ID ORDER BY customers.ID;
- Following query returns a temporary table showing those many number of _sets of all customer Names(means each set contains all customer Names)_ as there are _orders Names_ because in every set of all customer Names, there is only one orders Name that all those customer Names in that set correspond to. _So if there n entities in a column to be displayed and there are m entities in another column to be displayed the total number of rows will be n * m_ in the following query:
> SELECT customers.Name, orders.Name from customers, orders;

- Display all customer Names and _their corresponding order Names
> SELECT customers.Name, orders.Name FROM customers, orders WHERE orders.seller_ID = customers.ID;

```
The above example is the subset of its previous example because it only keeps those rows out of all n * m rows in which the orders' seller_ID value matches customers' ID in the backend
```

## Using custom table names to reduce the length of join statements using AS
- Use AS keyword to define custom table name when they're used in FROM, and use the custom keyword at rest of the places 
- Eg: Display Customers Name, ID and _his_ orders Name and Amount  
> SELECT ct.ID, ct.Name, ord.Name, ord.Amount FROM customers AS ct, orders AS ord WHERE ct.ID = ord.Customer_ID ORDER BY ct.ID;

- Display item names and customers names who bought the item
> SELECT ct.names, it.names FROM customers AS ct, items AS it WHERE it.seller_id = ct.id;

- Display names of students and the university they study in.
> SELECT students.Name, university.Name FROM students, universities WHERE students.university_id = universities.id;

## Types of Joins

## INNER JOIN
- Same as JOIN/JOIN using WHERE with different syntax. The comma is replaced by 'INNER JOIN' and WHERE is replaced with 'ON', meaning _'ON applying the following condition'_.
- Syntax: `SELECT <col-names> FROM <table-1-name> INNER JOIN <table-2-name> ON condition`.
condition can be table-1-name.col-name = table-2-name.col-name.
- [Venn Diagram for INNER JOIN with ON condition](https://api.sololearn.com/DownloadFile?id=2833). 
- If only use INNER JOIN, then again total rows will be n * m, as above;

## LEFT JOIN/ LEFT OUTER JOIN(both are same)
- (ON condition is always required) It is used to fetch data from both tables, wherein the number of rows in the result table will _always_ be greater than or equal to the number of rows in left hand side table, even if the ON condition is violated/not matched by displaying _all_ rows from left table./ It is used to fetch data from two tables wherein the result will _always_ contain _all distinct rows_ from the left table, even if the ON condition is violated/not met. Note: because of displaying all distinct rows in result table, there may be more number of rows in result table as a consequence of duplicated row values in result table(see below example 3/2).   

- Eg1: Display items' name if items' ID match with customers' ID. Now, this is never possible because items' ID starts from 100 to 110, and customers' ID ranges from 1 to 7.
> SELECT items.name FROM customers LEFT JOIN items ON items.ID = customers.ID;

```
The result is 7 empty rows and 1 column with column name of name.
```
- Eg2: Display all items' name when items' Seller_id matches customers' ID. Note: items and customers both have 7 rows. customers' ID ranges from 1 to 7. items' Seller_id can take values: 1, 2, 3, 4, 6, and has values in order - 1, 1, 2, 2, 3, 4, 6.
> SELECT items.name FROM customers LEFT OUTER JOIN items ON items.Seller_ID = customers.ID;

```
Result table will contain 9 rows * 1 column, with only 7 values filled, rest 2 are empty (not NULL).
```
- Eg3: Display all customers' name and items' name when items' Seller_id matches customers' ID.
> SELECT customers.name, items.name FROM customers LEFT OUTER JOIN items ON items.Seller_id = customers.ID;

```
Result table contains all the seven items' name with thier corresponding customers' name, but customers' name are duplicated, so 2 more rows with values of the left out unique customers' name are also present and their corresponding values in items' name is NULL. Total 9 rows * 2 columns.
```
- [Venn diagram for LEFT JOIN](https://api.sololearn.com/DownloadFile?id=2834)

### RIGHT JOIN/ RIGHT OUTER JOIN(both same)
- (ON condition is required) It is used to fetch data from two tables wherein the resulting table 

## join varun
- sql vs relational algerba- same concept,syntax different
to find result from two/more tables
Natural join (imp ), befre NJ, Join = crosspdt + some condition
natural join = when need to make some common attribute equal, use NJ
NOTE table1, table2: comma means cross pdt, cross pdt means= 1st tsables row has corresponding values for all rows of 2nd tavble, 2nd row of 1st table has corresponding values for all rows of 2nd table,so on. If n rows in table 1, m rows in table 2, then total rows = n * m 
after getting crosspdt, check condition and if condition matched then select/
choose that row.
Left outer join - returns matching rows and the rows which are in the left table but not in right table. Need to know natural join
outer join = NJ + something


## Join Socratica database
- [Socratica](https://www.youtube.com/watch?v=9yeOJ0ZMUYw&list=PLi01XoE8jYojRqM4qGBF1U90Ee1Ecb5tt&index=10)
- Refer resources/Martian Database.txt - use it to create Martian Database.
- Database terms
    - martiandb: name of database, has 5 tables
    - martian: All people living on Mars
    - base: All habitats on Mars
    - visitor: All current visitors on Mars
    - inventory: Supplies available at each base
    - supply: Items available at central storage
- Check all columns of all tables
- Every base has a head martian, followed by another martian who is also reported to by other martians of that base. super_id in martian table is the martian_id of the martian that they report to in their base. The martian with null super_id is the one who doesn't report to anyone and is the head. Eg- in base 1 there're 4 martians, and the 1st one is the head of the base and doesn't report to anyone. The 2nd martian reports to martian_id 9 and the 3rd and 4th ones report to the head. 
- There're 7 visitors and only 5 are attended by a host.
---
## JOIN Scoratica
- Syntax
```
SELECT t1.col-1, t2.col-2,...
FROM table-1 AS t1
_____ JOIN table-2 AS t2
ON t1.id = t2.id
WHERE condition(s)
ORDER BY column
```

## CROSS JOIN/Cartesian Product
- It finds/returns pairs of every row in first table with each row in the second table. It can then be used as a table and manipulated. (The 2nd table's all rows will be written against 1st row of 1st table, so on)
- It doesn't require the tables to be linked
- It doesn't have a condition or WHERE/ON clause because if it does, then it will act just like INNER JOIN
- It can be repliacted by using comma also
- If table 1 has n rows, table 2 m, new table will have n * m rows, and total columns = n + m
- Eg: Find CROSS JOIN of martian and base table
> SELECT * FROM martian CROSS JOIN base;

OR

> SELECT * FROM martian, base;

## INNER JOIN
- It selects only those rows from the CROSS JOIN of the two tables which match the ON condition/ It only returns connected rows when there's a matching id in both tables(Socratica).
- The two tables _must_ be linked
- [Venn Diagram](https://api.sololearn.com/DownloadFile?id=2833)

> ON acts as a condition/WHERE

## LEFT JOIN
- (Socratica) Returns all rows from the left table even if there's no matching row in the right table(which'll be NULL)/ It returns the INNER JOIN of the tables and those rows from the left table which were not included in INNER JOIN.
- It can't be used without ON.
- [Venn](https://api.sololearn.com/DownloadFile?id=2834)

## RIGHT JOIN
- (Socratica) Returns all rows from the right table even if there is no matching row in the left table(will be NULL)
- It can't be used without ON.
- [Venn](https://www.w3schools.com/sql/img_rightjoin.gif)

## FULL JOIN/ FULL OUTER JOIN(both are same)
- (Socratica) Returns all rows from the left table and all rows from the right table, connected/matching rows are connected. If there's no match, rows are still included with NULLs for the columns of the other table.
- [Venn](https://www.w3schools.com/sql/img_fulljoin.gif)

## JOIN problems
### Martian report: Create a report of all martian names with their base names
- Link b/w martian and base: base_id
1. Join the tables using INNER JOIN with condition of matching base_id

``` 
SELECT * 
FROM martian 
INNER JOIN base 
ON martian.base_id = base.base_id;
``` 
- Problem: doesn't include martians without a base, John Carter

2. Join tables using INNER JOIN so rows from martian table not covered in INNER JOIN also show up.
```
SELECT * 
FROM martian
LEFT JOIN base
ON martian.base_id = base.base_id;
```
3. Now filter the results, use fully qualified column names, otherwise get ambiguity error.
```
SELECT martian.martian_id, base.base_id, base.base_name
FROM martian
LEFT JOIN base
ON martian.base_id = base.base_id;
```
4. Use AS to reduce length of query
```
SELECT m.martian_id, b.base_id, b.base_name
FROM martian AS m
LEFT JOIN base AS b
ON m.base_id = b.base_id;
```
### Visitor report: Display name of each visitor. Show name of visitor's host. 
- Link: host_id in visitor and martian_id in martian
- LEFT JOIN: as some visitors don't have any host

```
SELECT *
FROM visitor
LEFT JOIN martian
ON visitor.host_id = martian.martian_id;
```

Improving
```
SELECT v.first_name AS v_fn, v.last_name AS v_ln, 
        m.first_name AS m_fn, m.last_name AS m_ln
FROM visitor AS v
LEFT JOIN martian AS m
ON v.host_id = m.martian_id;
```
### (Self Join)Super Report: Display list of each Martian and the person they report to
- Link: martian_id with martian_id (table martian and martian) 
- There are some martians that don't have super_id because they are head. So use LEFT JOIN

```
SELECT * 
FROM martian
LEFT JOIN martian
ON martian.super_id = martian.martian_id;
```
Improve
```
SELECT m.first_name AS fn, m.last_name AS ln, 
        s.first_name AS super_fn, s.last_name AS super_ln
FROM martian AS m
LEFT JOIN martian AS s
ON m.super_id = s.martian_id
ORDER BY m.martian_id;
```
### Inventory report for base #1: Display the item's id (supply id), quantity present at the base, item name and description, and also those items which that base doesn't have
- (initial)link: supply_id in base and supply. There can't be any inventory which doesn't take supply from the Centralized Supply Hub(supply). But there can be some supply locations which don't supply at base #1. Hence, use RIGHT JOIN to also see which supply locations have what other items.

- (later)Link: supply_id in base and supply. Ideally all supply items(all supply_id) should be present in a base, but here in base #1 those items which are not present(have qty = 0) are not shown in inventory. So we need to display such items also which are not present in inventory of base 1, but need to be added.
```
(Mine)
SELECT * 
FROM inventory AS i
RIGHT JOIN supply AS s
ON i.supply_id = s.supply_id
AND base_id = 1
ORDER BY s.supply_id;
```
Note: using WHERE instead of AND will be wrong as it'll only fetch base_id 1

```
(Socratica)
SELECT s.supply_id, i.quantity, s.name, s.description
FROM (SELECT * FROM inventory WHERE base_id = 1) AS i
RIGHT JOIN supply AS s
ON i.supply_id = s.supply_id
ORDER BY s.supply_id;
```
Improvement:homework
Replace all null entries with 0

### No Host Report: List of visitors without host. Martians available to do the job
- Link: visitor host_id and martian martian_id. INNER JOIN only gives visitors that have host
```
SELECT  * 
FROM visitor AS v
INNER JOIN martian AS m
ON v.host_id = m.martian_id;
```
- LEFT JOIN will only add visitors that need host, but won't show available hosts
```
SELECT  * 
FROM visitor AS v
LEFT JOIN martian AS m
ON v.host_id = m.martian_id;
```
- FULL JOIN shows available hosts also
```
SELECT  * 
FROM visitor AS v
FULL JOIN martian AS m
ON v.host_id = m.martian_id;
```
- Filtering: need only unattended visitors and available martians, only names
```
SELECT v.first_name AS visitor_fn, v.last_name AS visitor_ln, 
		m.first_name AS martian_fn, m.last_name AS martian_ln
FROM visitor AS v
FULL JOIN martian AS m
ON v.host_id = m.martian_id
WHERE m.martian_id IS null OR v.host_id IS null;
```

## UNION, UNION ALL
- Both used to combine data from multiple tables within the same database or from different databases or from different servers, into a single dataset/table.
- Condition for UNION and UNION ALL: in each table to be combined
    - Number of columns must be same
    - All columns must come in same order, otherwise result may be wrong
    - All columns must have the same order of data type
    - If columns don't match across all queries, use NULL(or any other value)

## UNION
- Combines rows/data of multiple tables into single table without having any duplicate row. (just like set in math)

- Eg: to combine the data of ID, FirstName, LastName ,City in tables- first and second removing duplicates
```
SELECT ID, FirstName, LastName, City
FROM first
UNION
SELECT ID, FirstName, LastName, City
FROM second; 
``` 

## UNION ALL
- Combines data from multiple tables into single table without removing duplicate rows

- Eg: to combine the data of ID, FirstName, LastName ,City in tables- first and second w/o removing duplicates
```
SELECT ID, FirstName, LastName, City
FROM first
UNION ALL
SELECT ID, FirstName, LastName, City
FROM second;
```


## Problems on Commands
- Pickup 3 cakes with least calories from a table cakes containing name and calories column
> SELECT names, calories FROM cakes ORDER BY calories LIMIT 3;

Not Working:
```
Multiple command
SELECT names, calories FROM cakes ORDER BY calories;
SELECT names, calories FROM cakes LIMIT 3;
```
- Select "id" from "students" and order the results by id, in descending order.
> SELECT id FROM students ORDER BY id DESC;

- Display name and minimum cost from table items, filtering by namr and seller id
> SELECT name, MIN(cost) FROM items WHERE name LIKE '%boxes of frogs' AND seller_id IN(68, 6, 18);

- Display all values from students table in which the field "university" equals "MIT"
> SELECT * FROM students WHERE university LIKE 'MIT'; (dont know if correct)

> SELECT * FROM students WHERE university = 'MIT'; (correct)

- Select name and university columns from table students who are belonging to MIT or Stanford and order the results by university column
> SELECT name, university FROM students WHERE university IN('MIT', 'Stanford') ORDER BY university

- Display the name and age from students table where age is greater than the average of all ages
> SELECT name, age FROM students WHERE age > (SELECT AVG(age) FROM students);

- Display all fields from table "Apartments" in which the price is greater than the average price and its "status" is 'Not rented' and sort it by price.
> SELECT * FROM Apartments WHERE price > (SELECT AVG(price) FROM Apartments) AND status = 'Not rented' ORDER BY price;

> SELECT * FROM Apartments WHERE price > (SELECT AVG(price) FROM Apartments) AND status LIKE 'Not rented' ORDER BY price;

- Display students' names with their universities' names and all other universities' names
```
SELECT students.names, universities.names
FROM student
RIGHT OUTER JOIN universities
ON students.university_id = universities.id;
``` 

- select name, cost, and bids from the ''items'' table. Select only those items whose bids are greater than 123
```
SELECT name, cost, bids
FROM items
WHERE bids > 123;
```

- create VIEW temp containing students' id, name and marks columns and has data for 10 toppers
```
CREATE VIEW temp AS
SELECT id, name, marks
FROM students
ORDER BY marks DESC
LIMIT 10;
```

- students table has col- university_id and names, universities table has id, and names column. Display ALL students' names and their universities' names(if any).  
```
SELECT students.name, universities.names
FROM students
LEFT JOIN universities
ON students.university_id = universities.id;
```
- Add a new row in table people with values- John Smith, '1' and 22
```
INSERT INTO people
VALUES ('John Smith', '1', 22);
```
- Change the name to Jordan for id 147 in table people
```
UPDATE people 
SET name = 'Jordan'
WHERE id = 147;
```

- Create a table test with id as PK integer and name as varchar
```
CREATE TABLE test (
    id int,
    name varchar(30),
    PRIMARY KEY (id)
);
```

- Remove column age from people
```
ALTER TABLE people
DROP COLUMM age;
```
- Create a view most_abs containing students' id, name and absences for 10 students with greatest number of absences
```
CREATE VIEW most_abs AS
SELECT id, name, absences
FROM students
ORDER BY absences DESC
LIMIT 10;
```
- Delete table students form database
```
DROP TABLE students;
```

- Remove column temp from students
```
ALTER TABLE students
DROP COLUMN temp;
```

- You manage a zoo. Each animal in the zoo comes from a different country. Here are the tables you have: Animals - cols: name, type, country_id. Countries- cols: id, country
- 1) A new animal has come in, with the following details:
name - "Slim", type - "Giraffe", country_id - 1
Add him to the Animals table.
- 2) You want to make a complete list of the animals for the zoo’s visitors. Write a query to output a new table with each animal's name, type and country fields, sorted by countries.

```
INSERT INTO Animals
VALUES ('Slim', 'Giraffe', 1);

SELECT A.name, A.type, C.country
FROM Animals AS A
INNER JOIN Countries AS C
ON A.country_id = C.id
ORDER BY A.country_id DESC;
```
- In the "users" table of website logins and passwords, select the first 10 records in the table
```
SELECT * 
FROM users 
LIMIT 10
```

-  create the table "users" to store website user logins and passwords, with auto increment and not null id
```
CREATE TABLE users (
    id INT NOT NULL AUTO INCREMENT,
    logins varchar(100),
    password varchar(100)
);
```

- Select all students under the age of 21. The results should be sorted according to the students' names
```
SELECT * 
FROM students
WHERE age < 21
ORDER BY name;
```
- Your boss asks you to print(everything) a list of the first one hundred customers who have balance greater than $1000 or who are from NY city
```
SELECT *
FROM customers
WHERE balance > 1000 
OR city = 'NY'
LIMIT 100;
```

- You need the ages of all animals. The first query shows the ages of bears and birds from zoo1, the other shows the ages of lions and crocodiles from zoo2. Cols- animal,age. Tables -zoo1, zoo2
```
SELECT age
FROM zoo1
WHERE animal IN('bear', 'bird')
UNION
SELECT age
FROM zoo2
WHERE animal IN('lion', 'crocodile');
```

- Display a list of customers in the form "name is from city", where name and city are present in customers
```
SELECT CONCAT(name, ' is from ', city)
FROM customers;
```

- The zoo administration wants a list(everything) of animals whose age is greater than the average age of all of the animals. Table - zoo
```
SELECT *
FROM zoo
WHERE age > (SELECT AVG(age) FROM zoo);
```
- There are many wolves in the zoo: black wolf, white wolf, lucky wolf, little wolf. They all have 'wolf' at the end of their names. Print the ages of all of the wolves. Use LIKE. Table - zoo, col -animal, age
```
SELECT age 
FROM zoo
WHERE animal LIKE '%wolf';
```
- Retrieve all students' name between the ages of 18 and 22. Table- students. col- name, age 
```
SELECT name
FROM students
WHERE age BETWEEN 18 AND 22;
```
- Update the "students" table to set Jake's university to MIT. His id is 682
```
UPDATE students
SET university = 'MIT'
WHERE id = 682;
```

- When you inserted "elephant" as a new animal, you forgot to include the elephant's age as 14. Correct this mistake by updating the "zoo" table
```
UPDATE zoo
SET age = 14
WHERE animal = 'elephant';
```
- Update the food_balance to 23 for animals whose age is greater than the average age of the animals
```
```

## See
- See IS and SELF JOIN
- DEFAULT, CHECK, UNIQUE
- Serial, bitserial, serial8[see](https://www.ibm.com/docs/en/informix-servers/12.10?topic=types-serialn-data-type)
- for postgres- common for integer - integer, characters - text, and decimal values- numeric 
> COALESCE()- all values same datatype?, if first one is string, then int, returns error, why?
