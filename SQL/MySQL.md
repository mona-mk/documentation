# MySQL

[Udemy course: The Ultimate MySQL Bootcamp: Go from SQL Beginner to Expert](https://www.udemy.com/share/101Wiw3@az0LA2LPO45cpQXsdRbsSMp2Q7UszLZcDpBiF_PXc5bZhyRPyFXRCPVQMV-gr0Onsg==/)

---
**database**

A structured set of computerised data with an accessible interface, i.e.
* collection of data
* a method for accessing and manipulating that data

---
**database management system**

interface for the database. Your app ——> DBMS ——> Database
Typically combination of these two are called databases (DBMS ——> Database) and there are a lot of them out there: PostgreSQL, MySQL, Oracle database, SQLite. These names are actually DBMS but they are called databases anyway. They use the same language, but the difference is not in language but the feature they have. How fast one is, how secure one is compared to other, how big is the download, how permissions work.

---
**SQL (structured Query Language)**

is the language that we use to talk to database: “find all users”, “”delete every single user”. So this English language is then translated to SQL language. There is a standard for how SQL syntax should be, and these DBMS are tasked by implementing that standard in their system, and they deviate sometimes a bit.

---
**MySQL Server & MySQL Workbench**

We first need to install MySQL server which is the DBMS. When the server is running, we can interact with it in different ways. One way is through the command line. Another way is to use a tool/application/GUI called MySQL Workbench, where you write your queries in a file, save them, and when you are ready you can execute them.

First we need to instal MySQL server: Download from mysql website. You need to set a password.

Add following line to your bash profile: `export PATH=${PATH}:/user/local/mysql/bin`

`mysql -u root -p` (this will prompts for a password from user)

Then install mysql workbench, and use your mysql password here to connect to local database running on port 3306

---
It is a common pattern with SQL that SQL keywords like CREATE will capitalised. You don’t have to, but it just helps to distinguish.
Also, it doesn’t care about new lines, all it care about is spaces between words.

--
comment. Requires a space after double dash

`command + shift + /`    (multi-line-comment)

`;` command terminator

`'` In MySQL, technically depending on the mode it is running, you can use double quote or single quote, but that is not the case in other DBMS aka other flavours of SQL, you can’t use double quotes. Therefore, to wrap a text, always use single quotes.

`\` To escape a quote used inside the text `(’Mario\’s game’)` . You don’t need to escape double quotes if you always use single quotes to wrap the text `(‘Maria said “haha”’)

If you have a script, you can run the script in mysql command line: `source <my-script.sql>`

SQL formatting: there are many different formatting or code beautifiers. In workbench, there is a brush for this. In VS code install sql formatting extension. The point is to be consistent, and use a tool and don’t do manual work.

---
**creating databases and tables**

We installed our database server, and within this database server, we have silos of information called databases. A relational database, is comprised of tables (=a collection of related data held in a structure format within a database)

`show databases;` (list of available databases)

`CREATE DATABASE <name>;` (create database. For name, you can use snake_case, camelCase, PascalCase)

`DROP DATABASE <name>;`

`USE <database-name>;` (is equivalent to double clicking the database from mysql workbench. Saying this is the database I want to interact with)

`SELECT DATABASE()` Tell you what is the current database, i.e. what database you are in

**data types**

We need consistency of type for a column. There are a lot of data types in MySQL world.  Refer to documentation on data type for more info.
- Numeric type (INT, SMALLINT, TINYINT, MEDIUMINT, .., DECIMAL, NUMERIC, FLOAT, DOUBLE, BIT,..)
- String type (CHAR, VARCHAR, BINARY, BLOB, TINYBLOB, MEDIUM BLOB, …, TEXT, TINYTEXT, MEDIUMTEXT, .., ENUM)
- Date type (DATE,  DATETIME, TIMESTAMP, TIME, YEAR)

`varchar(100)` variable-length string with maxium 100 character length (example 'The quick brown fox jumps over the lazy brown dog' )
```
CREATE TABLE <tablename>
(
   column_name data_type NOT NULL,
   column_name data_type,
   column_name data_type DEFAULT ‘unnamed’
   column_name data_type NOT NULL DEFAULT ‘unnamed’
   column_name data_type NOT NULL PRIMARY KEY,
   column_name data_type PRIMARY KEY AUTO_INCREMENT,
   column_name TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
   column_name TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
   column_name data_type NOT NULL UNIQUE,
   column_name data_type CHECK expr, -> example age INT CHECK (age > 18)
);
```

Another syntax for primary key:
```
CREATE TABLE <tablename>
(
   column_name data_type,
   PRIMARY KEY (colum_name)
);
```

Another syntax for constraint where we can give constraint a name. This way the error message will be more understandable,
```
CREATE TABLE <tablename>
(
   column_name data_type,
   CONSTRAINT constraint_name CHECK expr, -> you can have more columns in expression e.g. UNIQUE (col1, col2) where combos must be unique. Also, custom constraints are possible e.g. CHECK (col1 > col2)
);
```
- By default, NULL is default value, and if not given, it will insert NULL as values. If you set some default, the table still can have null values only if you specify NOT NULL.
- Table name is normally plural, but there is no rule for this.
- PRIMARY KEY is a unique identifier to differentiate rows. They cannot be NULL, therefore specifying NOT NULL is redundant.
- AUTO_INCREMENT will automatically increment with unique values.
- with UPDATE ON we can keep track of the last time the row was updated

If you are not using GUI and just using command line:

`SHOW TABLES;` (what tables exist in current database)

`SHOW COLUMNS FROM <tablename>`(information about each column will be given)

`DESC <tablename>` or `DESCRIBE <tablename>` (same command as above)

`DROP TABLE <tablename>`  (deletes the table completely)

---
**Inserting data**
```
INSERT INTO <table-name> (col1, col2) VALUES (value1, value2), (value11, value22), (values111, value222);
INSERT INTO <table-name> (col2 col1) VALUES (value2, value1);    (order matters)
```
If there are more columns, the rest of the values will be filled with null. You can even write a completely null row: `INSERT INTO <table-name> () VALUES ();`

---
**CRUD (CREATE, READ, UPDATE; DELETE)**
```
SELECT * FROM <table-name>
SELECT col1, col3 FROM <table-name>
````

**WHERE clause**

we narrow down with WHERE clause and it is not just limited to be used with SELECT (e.g. DELETE or UPDATE). The column used in WHERE clause does not have to be in the returned statement.
```
WHERE name=‘eGG’ By default the = sign is case insensitive. The example finds rows with Egg.
WHERE col1=col2 (compare two columns)
```

**alias**

you can rename your results by using aliases. `SELECT col1 AS col1_new`. If you want to alias with a text that have space, wrap alias in quotes `SELECT col1 as 'col1 new' FROM <table>`

**UPDATE**

(try SELECT before you UPDATE)
```
UPDATE <table-name> SET col1='something', col2='something_else'
UPDATE <table-name> SET col1='something', col2='something_else' WHERE
```

**DELETE**

(try SELECT before you DELETE)
```
DELETE FROM <table-name>; (deletes all rows)
DELETE FROM <table-name> WHERE

```

---
**String functions**

Different builtin operators that can be performed on text columns. Here we cover half of what’s available, check documentation for more.

**CONCAT**

concatenate strings
```
SELECT CONCAT(col1, col2) FROM <table-name>
SELECT CONCAT(col1, str) FROM <table-name>
SELECT CONCAT(col1, ‘!!!’) FROM <table-name>
SELECT CONCAT(‘h’, ‘e’, ‘l’)
```

**CONCAT_WS**

another flavour of CONCAT with separator
```
SELECT CONCAT(separator, col1, col2) FROM <table-name>
SELECT CONCAT(‘ - ’, col1, col2) FROM <table-name> (just between the columns not before or after)
```

**SUBSTRING** or **SUBSTR**

get smaller piece of a larger string
```
SELECT SUBSTRING(str, n1, n2)
SELECT SUBSTRING(‘hello world’, 1, 4) -> hell
SELECT SUBSTRING(‘hello world’, 7) -> world
SELECT SUBSTRING(‘hello world’, -1) -> d  (one to the last until the end)
SELECT SUBSTRING(‘hello world’, 1, 1) -> h
```

**REPLACE**

replace part of string

`REPLACE(str, from_str, to_str)`


**REVERSE**

`REVERSE(str) -> reverses the string`

**CHAR_LENGTH**

number of characters in the string

`CHAR_LENGTH(str)`

**LENGTH**

returns length of string in bytes

`LENGTH(str)`

**UPPER(str)** = **UCASE(str)**

**LOWER(str)** = **LCASE()**

**INSERT**

`INSERT(str, pos, len, newstr)` -> insert some string into another string. len is the number of characters you want to replace, use 0 for just adding without replacing any characters.

`LEFT(str, len)` -> get the left number of strings

`RIGHT(str, len)`

`REPEAT(str, count)` -> repeat string count number of times

```
TRIM([{BOTH | LEADING | TRAILING} [str-to-remove] FROM] str)
TRIM([str-to-remove FROM] str)
```
removing leading or trailing characters from str. If str-to-remove is not provided, it is space by default

---
**Refining selections**

**DISTINCT**

will remove duplicates from the result
```
SELECT DISTINCT col_name FROM <table_name>
SELECT DISTINCT col1, col2 FROM <table_name>  -> distinct combination of col1 and col2
```

**ORDER BY**

at the end asking to sort results based on column/s

`SELECT col FROM <table> ORDER BY col1 DESC;`

By default it is ASCENDING/ASC. You can also sort by numeric columns.

`SELECT col FROM <table> ORDER BY 2;`  -> order by second column

`SELECT col FROM <table> ORDER BY col1, col2, col3;` -> sorts first but first column, after finished, sorts by second col and so on. So, in this case for each group of rows from the first sort, the second sorting happens. Therefore, there must be some duplicated values in rows that ordering with respect to more columns makes sense.

`SELECT CONCAT(col1, col2) AS col_new FROM <table> ORDER BY col_new` -> you can order by one of the returned alias columns

**LIMIT**

control number of results. It is especially useful together with ORDER BY
```
SELECT col FROM <table> LIMIT 0,5 -> first five
SELECT col FROM <table> LIMIT 1,5 -> start with first row and give 5 rows. This allows selecting from middle of results
````

**LIKE**

basic searching `expression LIKE pattern`. There are two wildcard characters to make a pattern:
- % percent wildcard matches zero, one, or more characters
- _ underscore wildcard matches a single character.
```
LIKE 'Kim%'	match a string that starts with Kim
LIKE '%ch%'	match a string that contains ch
LIKE '_uy'	ends with uy and is preceded by one character e.g., guy
```
This is not as powerful as regex, and the regex patterns are different than LIKE patterns. 

---
**Aggregate functions**

**COUNT**
```
SELECT COUNT(*) FROM <table>  
SELECT COUNT(col1) FROM <table>   -> COUNT does not count nulls.
SELECT COUNT(DISTINCT col1) FROM <table>
```

**GROUP BY**

summarises or aggregates identical data into single rows

`SELECT col FROM <table> GROUP BY col;`  -> This is just similar to DISTINCT, and gives us just the distinct groups and we are not taking advantage of the groups created
`SELECT col, COUNT(*) FROM <table> GROUP BY col;` -> here we apply an aggregation to each group. Adding the group by col to select is not mandatory, but col is the only non-aggregated column that you can select, for the rest of the columns you need to apply an aggregate function

**MIN / MAX**

can be used with or without GROUP BY
```
SELECT MIN(*) FROM <table>
SELECT col1, col2, MIN(col3) FROM <table> GROUP BY col1, col2
```

**SUBQUERIES**

is a query within another query and it tells sql to run the subquery first. You have to uses parentheses () for the subquery  

`SELECT title, pages FROM books WHERE pages = (SELECT MAX(pages) FROM books);`

**SUM**
```
SELECT SUM(*) FROM <table>
SELECT col1, col2, SUM(col3) FROM <table> GROUP BY col1, col2
```

**AVG**
```
SELECT AVG(*) FROM <table>
SELECT col1, col2, AVG(col3) FROM <table> GROUP BY col1, col2
```

Check the rest of aggregate functions from documentation

---
**Revisiting data types**

For details on storage size of each type and many other types, refer to doc.

**CHAR vs VARCHAR**

CHAR has a fixed length, i.e. if you create CHA(2) it is optimised with two spaces, and even if you provide one character, they will be right-padded with spaces, and when you read back they spaces will be removed.

```
SIGNED / UNSIGNED (negative values handling)
CREATE TABLE <table> (col TINYINT UNSIGNED)
```

**DECIMAL**

with decimal, we can have float number with fixed number of digits. `DECIMAL(5, 2)` -> 5 total number of digits, 2 digits decimal (more decimals will be rounded). More than 3 digits before decimal won’t be inserted into the table

**FLOAT vs DOUBLE vs DECIMAL**
- DECIMAL take more space than FLOAT and DOUBLE, but the latter two comes with the cost of precision
- float 4 Bytes 7 digits precision
- DOUBLE 8 Bytes 15 digits precision

**DATE / TIME / DATETIME**

Format is ‘YYYY-MM-DD hh:mm:ss[.fraction]

There are bunch of functions available for date and time. 
```
CURTIME / CURRENT_TIME
CURDATE / CURRENT_DATE
NOW / CURRENT_TIMESTAMP
DAY / DAYOFMONTH
DAYOFWEEK
DAYOFYEAR
MONTHNAME
HOUR
MINUTE
````

`DATE_FORMAT(date, format)` -> `SELECT DATE_FORMAT('2009-10-04 22:23:00', '%W %M %Y’);` -> 'Sunday October 2009'

**DATEDIFF**

returns difference in days between two days

```
DATE_ADD(date, INTERVAL expr unit)
DATE_SUB(date, INTERVAL expr unit)
date arithmetic. Given a date you add or substract a time interval with units and amount specified in expr (more flexibility than DATEDIFF, you can use seconds, year etc)
SELECT DATE_ADD("2017-06-15", INTERVAL 10 DAY);
```

```
ADDTIME
SUBTIME
TIMEDIFF
```

You can also use math operators directly

`SELECT NOW() - INTERVAL 18 YEAR`

**TIMESTAMP**

is very similar to DATETIME data type, but it takes less storage, they support less range of dates (1970 to 2038). But datetime and timestamp are interchangable

**YEAR**

a data for year, for instance a column released_year can be of type YEAR instead of integer

---
**comparison & logical operators**

`!=` not equal

`NOT LIKE`

`>=`  `<=`
```
SELECT 80>40; -> 1
SELECT 80>100; -> 1
SELECT 1>null; -> null
```

**AND / OR**

`left AND right` -> `SELECT 1>0 AND 8=7;` -> returns 0

**BETWEEN**
`BETWEEN x AND y` -> inclusive both ends

`NOT BETWEEN`

**time comparison**

it is better to cast string into date /time types and then compare
```
CAST(expr AS type [ARRAY])
CAST(’12:00:00’ AS TIME)
```

**IN**

select values based on some sets of values

`SELECT col FROM <table> WHERE col1 IN (value1, value2, value3)`

`NOT IN`

**%** modulus

`SELECT * FROM <table> WHERE col % 2 !=0`

**CASE**

to make decisions based on output of values

`SELECT col CASE WHEN .. THEN .. WHEN .. THEN .. ELSE .. END AS alias FROM <table>`

**IS NULL**

`IS NOT NULL`

`WHERE col IS NULL`

---
**ALTER TABLE**

helps us change table, add column, rename column, change data type, add constraint, etc. It can actually do a lot of things within one syntax.
```
ALTER TABLE table_name ADD [COLUMN] column_name datatype;
ALTER TABLE table_name DROP [COLUMN] column_name;
ALTER TABLE table_name RENAME COLUMN col TO col_new;
ALTER TABLE table_name MODIFY col new_data_type; -> this is to modify the data type of the table not its table name
ALTER TABLE table_name ADD CONSTRAINT constraint_name CHECK expre;
```

---
**order of execution**
1. FROM, including JOINs
2. WHERE
3. GROUP BY
4. HAVING
5. WINDOW functions
6. SELECT
7. DISTINCT
8. UNION
9. ORDER BY
10. LIMIT and OFFSET

---
**one to many & joins**

Real world data is messy and interrelated. Relationship basics:
1. one to one relationship (one row in one table matches to one row in another table)
2. one to many relationship (one row in one table has many corresponding rows in another table)
3. many to many relationship (multiple records from one table are associated with multiple records in another table)

It is always better to keep your data separate to reduce amount of duplicated data. Only if you use the two tables always together and there is no other use case, it makes sense to have a single table instead.

- PRIMARY KEY is an identifier of a unique row in each table
- FOREIGN KEY is a reference to another table, and it also takes care of making sure that new keys exists in the original table.

You don't need a FOREIGN KEY, you can join arbitrary columns. But having a foreign key ensures that the join will actually succeed in finding something. Foreign key give you certain guarantees that would be extremely difficult and error prone to implement otherwise.

syntax to add a foreign key for a table which reference another column from reference table
FOREIGN KEY (col2_from_table2) REFERENCES table1(col1)

When deleting data from primary table, in case you want corresponding rows from secondary table to be deleted, you can define this in schema:
FOREIGN KEY (col2_from_table2) REFERENCES table1(col1) ON DELETE CASCADE;

If we want the information from another table to be joined to the table, we need to use join. Otherwise, for instance, a subquery may work to get us the rows we want from the table.

**CROSS JOIN**

`SELECT * FROM table1, table2;`  -> this results in a cartesian join or cross join where get every combination possible, without any logic. This is typically useless for us.  For instance, 6 rows in one table and 5 rows in another, we get 30 rows in total.

**INNER JOIN**

where data from two tables overlap

`SELECT * FROM <table2> JOIN <table1> ON <table1>.<col1> = <table2>.<col2>;`

== 

`SELECT * FROM <table2> INNER JOIN <table1> ON <table1>.<col1> = <table2>.<col2>;`

**INNER JOIN + GROUP BY**

`SELECT col2, col22, col23 FROM <table2> JOIN <table1> ON <table1>.<col1> = <table2>.<col2> GROUP BY col2, col22;`

**LEFT JOIN / RIGHT JOIN**

every row from left and enrich it with data from right table, and if there is no match, write null

`SELECT * FROM <table2> LEFT JOIN/RIGHT JOIN <table1> ON <table1>.<col1> = <table2>.<col2>;`

---
**many to many joins**

It’s just when you have two tables that are connected to each other through a third table

```
SELECT 
    title,
    rating,
    CONCAT(first_name, ' ', last_name) AS reviewer
FROM
    reviews
        LEFT JOIN reviewers ON reviewers.id = reviews.reviewer_id
        LEFT JOIN series ON series.id = reviews.series_id;
```
You don’t need a subquery here, you can just have join after join

---
**IFNULL**

if it is null, replace it `IFNULL(SUM(col), 0)`

---
`ROUND(col, 2)` -> round col to 2 decimals

---
IF statement -> `IF(condition, value_if_true, value_if_false)`

---
**view**

views are some queries that we can give a name to and store, so we don’t have to write them over and over. When we call a view, we get the result of that query which looks like a table, but it is a virtual table. This allows to write shorter queries.
```
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```
Only small portion of views are updatable and insertable, i.e. only small portion of operations can be performed on certain views. For instance deleting a row from a view is not possible if you have aggregate function or join in your view query. Read documentation for these rules [Updatable and Insertable Views](https://dev.mysql.com/doc/refman/8.0/en/view-updatability.html) . If view is updatable, when you delete a row from view, it actually gets deleted from the original table as well.

To update the view you have two syntaxes:
```
CREATE OR REPLACE VIEW view_name AS new_query
ALTER VIEW view_name AS new_query
```

To drop a view: `DROP VIEW view_name;`

**materialised VIEW**

MySQL does not have materialised views. In postgresql: A VIEW is a stored query. When you select from it, you essentially run the query. A materialised view is more or less a view that is cached (or materialised) to disk, with the added bonus that, at least in PostgresSQL, there is a command REFRESH MATERIALIZED VIEW that you can feed to it the name of the view, and it'll regenerate it from scratch:
```
CREATE MATERIALIZED VIEW [ IF NOT EXISTS ] table_name
    [ (column_name [, ...] ) ]
    [ USING method ]
    [ WITH ( storage_parameter [= value] [, ... ] ) ]
    [ TABLESPACE tablespace_name ]
    AS query
    [ WITH [ NO ] DATA ]
```
Views are generally used when data is to be accessed infrequently and data in table get updated on frequent basis. On other hand Materialised Views are used when data is to be accessed frequently and data in table not get updated on frequent basis.

---
**HAVING**

HAVING clause is used to filter groups with get back from GROUP BY 

`SELECT … FROM … GROUP BY … HAVING COUNT(col)>value`

---
**GROUP BY WITH ROLLUP**

WITH ROLLUP the summary output of GROUP BY will have extra rows the represent higher-level aggregates (super-aggregates). This helps us answer questions at multiple level of analysis. See documentation for examples[GROUP BY Modifiers](https://dev.mysql.com/doc/refman/8.0/en/group-by-modifiers.html).

---
**SQL MODES**

These are settings of the SQL server where can change the behaviour of data validation check as well as the SQL syntax MySQL supports.

We can we view the current SQL mode, and there are two scopes:
1. SELECT @@GLOBAL.sql_mode;
2. SELECT @@SESSION.sql_mode;

To change modes:

`SET SESSION sql_mode  ‘list_of_modes_without_space_separated_by_comma’`

**STRICT_TRANS_TABLE**

This is the mode that check the data type during an insert, for instance if you try to insert a str to a decimal column, and if you switch off this mode, the string will turn into 0.0 (a decimal) and the insert goes through successfuly.

---
**Window function**

Window functions perform aggregate operations on groups of rows but they produce a result for each row

**OVER**

`SELECT col1, AVG(col2) OVER() FROM ..`. The OVER clause construct a window. When it’s empty, the window will include all rows

**PARTITION BY**

We put PARTITION BY inside of the OVER(), if we want the window function to work with multiple windows/groups.

`SELECT col1, AVG(col2) OVER(PARTITION BY col3) FROM …`

**ORDER BY**

ORDER BY inside the OVER clause makes the operation to be a rolling operation, for instance rolling SUM, rolling AVG. Of course ORDER BY still orders the column inside the window for instance DESC or ASC but then does the rolling operation. Note that the ORDER BY inside the OVER sorts data for that operation. Still ORDER BY as usual can be added at the end of query.

`SUM(salary) OVER(PARTITION BY department ORDER BY salary) AS rolling_dept_salary`

**RANK()**

Aside from aggregate functions, there are some functions that are solely window functions. This is one of them. RANK will rank the rows inside that window from 1…N

`RANK() OVER(ORDER BY col DESC) as new_col``

**DENSE_RANK()**

a nuisance in definition with RANK(), check documentation for more

**ROW_NUMBER()**

return the number of rows within each partition

**NTILE(number)**

with this for instance you can have quantiles like NTILE(4), will tell for each window, columns falls in which 25%. Check documentation for more

`FIRST_VALUE(column)` 

`FIRST_VALUE(emp_no) OVER(PARTITION BY department ORDER BY salary DESC) as highest_paid_dept` -> this gives the first value for the requested column within that window.

**LEAD() and LAG()**

get the diff between two consecutive rows
