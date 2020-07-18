# SQL Notes 
* This set of notes will discuss the course material from Treehouse's SQL courses 
* For a quick SQL reference see Tania Rascia's [Overview of SQL Commands](https://www.taniarascia.com/overview-of-sql-commands-and-pdo-operations/) article

## SQL Basics 
* A summary and review of basic SQL commands

### Data Types 
* There are a number of SQL data types, but three of the most common are 
    * Text 
        - names, descriptions etc
    * Numeric 
        - prices, ages, quantities, etc
    * Date
        - anything time related
* Different data types allow for the proper treatment of data, especially when it comes to sorting
* The database schema enforces data types 

### Retrieving Data
* To retrieve all columns for a table run 
    
    ```sql
    SELECT * FROM <table name>;
    ```

    * You would not normally use this command for large data sets, as it would get all the data for a table and cause performance issues as a result

* To retrieve specific columns for a table run 

    ```sql
    SELECT <column>, <column> ... FROM <table name>;
    ```

* E.g: 

    ```sql
    SELECT title, author FROM books;
    ```

* You can select columns in any order you like and the data will be returned in the order you specify

### Aliasing
* You can generate reports that provide aliases for column names 
* This is often done to make the reports more human-readable 
* Simply use the `AS` keyword after specifying a column name, and then specify your alias 
* E.g:

    ```sql
    SELECT first_name AS "First Name" from users;
    ```
* If you are going to use multiple words for an alias it must be in quotation marks
* This may differ depending on the specific relational database you're using, so reading documentation for your specific implementation is important
* You can also leave out the `AS` keyword: 

    ```sql
    SELECT first_name "First Name" from users;
    ```

* This syntax isn't as clear as should be avoided
* Using aliases does not change the column name in the database. It is only a way to alias column names in reports

### Filtering 
* You can filter the results of a query based on conditions you set
* One of the most common ways to do this is using the `WHERE` clause in a query:

    ```sql
    SELECT <column> FROM <table> WHERE <condition>
    ```

* E.g: 

    ```sql
    SELECT book FROM books WHERE catalogue_number = 12345;
    ```

* You can use just about any operator in your where clause to perform any sort of comparison. Eg: 

    ```sql
    SELECT score FROM results WHERE score >= 20;
    ```

* You can add multiple conditions with the `AND` and `OR` keywords. E.g:

    ```sql 
    SELECT title FROM books WHERE author = "J. K. Rowling" AND first_published < 2000;
    ```

    ```sql 
    SELECT * FROM books WHERE first_published >= 1900;
    ```

* Comparison operators can also be used on columns that contain date types
* If you wanted to chain many `OR` conditions, your query statement could become unwieldy fast. SQL provides the `IN` keyword to filter a column using a number of values. Consider this query:

    ```sql
    SELECT first_name, email FROM patrons WHERE library_id = "MCL1001" OR library_id = "MCL1100" OR library_id = "MCL1011";
    ```

* This could be simplified to: 

    ```sql
    SELECT first_name, email FROM patrons WHERE library_id IN ("MCL1001", "MCL1100", "MCL1011";```

* To perform the negation of this query, you only need to add the `NOT` keyword: 

    ```sql
    SELECT first_name, email FROM patrons WHERE library_id NOT IN ("MCL1001", "MCL1100", "MCL1011");
    ```

* This will search for first name and email for a patron where the library ID is not one of the specified IDs in parentheses
* There are times when you want to search for a record that has a column that falls within a range of possible values. Instead of using two `AND` clauses, you can use the `BETWEEN` keyword:

    ```sql
    SELECT <column(s)> FROM <table> WHERE <column> BETWEEN <minimum> AND <maximum>;
    ```

* E.g: 

    ```sql
    SELECT * FROM products WHERE price BETWEEN 10.99 AND 12.99;
    ```

* When using the `BETWEEN` keyword you must supply the minimum value first or the query will return no results

* To search for a record that is at least a partial match with a supplied value, use the `LIKE` keyword. `LIKE` allows searching that is more flexible than the equality operator, which only returns strict matches
* In order to work, the `LIKE` keyword needs at least one wildcard, which is the `%` symbol. This symbol is used to stand for any other possible character. Use it as a stand-in for anything or any number of other characters. 
* For example, `%ache` will match with all of the following: backache, headache, stomach ache 
* `swim%` will match with: swimmer and swimming
* And putting a search term between two `%` symbols will return any result that matches any part of the search term
* Using wildcards results in searches that are case-insensitive 
* E.g: 

    ```sql
    SELECT title FROM books WHERE title LIKE '%design patterns%';
    ```

    * This will search for any book with the terms "design patterns" in the title

* Make sure to remember to still use quotation marks

* Some times some columns in a row may be empty, or `null`
* To check if a column is empty, use `IS NULL` to check if the column in question is null: 

    ```sql 
    SELECT * FROM users WHERE last_name IS NULL;
    ```

* Likewise, if you want to search for a record with the condition that a certain column is not empty, you can use `IS NOT NULL` instead



## Modifying Data with SQL
* The last section summarized methods of retrieving data. This section will summarize creating, updating, and deleting records

### Inserting Rows 
* To insert a row, use: 

    ```sql
    INSERT INTO <table> VALUES (<val 1>, < val 2>, <val 3>);
    ```

* The order of your values is important, as they must be inserted into the database in the same order that the scheme defines their corresponding columns
* If you want a certain column in the row you are inserting to be empty, use the keyword `NULL`
* This is what you will often do for auto-incrementing ID columns, or if you are using an ORM it may do it for you (such as Sequelize)
* If you want to insert data into a row without worrying about the order your schema specifies, you can specify the order you want before specifying your values 
* E.g: 

    ```sql
    INSERT INTO loans (id, book_id, patron_id, loaned_on, return_by, returned_on) 
    VALUES (NULL, 2, 4, "2020-12-14", "2020-12-21", NULL);
    ```

* Simply specify your columns in any order, then specify your values to be inserted in that order
* When it comes to specifying `NULL` values, you can just leave out the column names and the values if you wish, unless the schema specifies that those columns must not be null

* You can insert more than one row at a time. E.g: 

    ```sql
    INSERT INTO books 
        (title, author, genre, first_published)
    VALUES 
        ("The Circle", "Dave Eggers", "Science Fiction", 2013),
        ("Contact", "Carl Sagan", "Science Fiction", 1985),
        ("Animal Farm", "George Orwell", NULL, 1945);
    ```
* Separating your sets of values with commas allows multiple inserts in a single query


### Updating Rows
* To update or edit a row, use:

    ```sql
    UPDATE <table> SET <column> = <value>;
    ```
* However, this will update the specified column for every row in the table specified, so it isn't used often
* You can update multiple columns like this: 

    ```sql 
    UPDATE <table> SET <col 1> = <val 1>, <col 2> = <val 2>, ... ;
    ```
* To update a specific column, simply use a `WHERE` clause to target the individual record you wish to edit: 

    ```sql 
    UPDATE <table> 
    SET <column> = <value> 
    WHERE <condition>;
    ```

* E.g: 

    ```sql
    UPDATE books 
    SET author_name = "Victor Hugo" 
    WHERE title = "Les Miserables" 
    AND book_id = 24601;
    ```
* `WHERE` conditions work the exact same way in `UPDATE` queries as they do in `SELECT` queries


### Deleting Records
* To delete all data from a table, use: 

    ```sql
    DELETE FROM <table>;
    ```
* To delete a specific row, use a `WHERE` clause
* Deleting is permanent, so exercise caution


### Transactions
* Transactions allow you to switch off autocommit (meaning the statements you write are automatically saved to disk) and then "commit" the statements once you decide. This would normally be done when writing batch operations (like seeding a database for instance)
* To use a transaction write: 

    ```sql 
    BEGIN TRANSACTION;
    ```
* To save all the results of statements written after the start of the transaction write: 

    ```sql 
    COMMIT;
    ```
* Use the `ROLLBACK` keyword to undo uncommitted queries in a transaction


## Reporting with SQL
* This set of notes summarizes the Reporting with SQL course
* This material addresses common ways to manage data in more human-readable and useful ways

### Ordering, Limiting and Paging 
* You can order the results of a query with the `ORDER BY` keyword 
* E.g: 

    ```sql 
    SELECT * FROM books ORDER BY title
    ```
* This will order the results by title ascending alphebetical order by default 
* You can specify whether you want the results to be in ascending or descending order by adding either `ASC` or `DESC`

* You can limit the number of results of a query by using the `LIMIT` keyword:

    ```sql 
    SELECT * FROM books ORDER BY title DESC LIMIT 10;
    ```
* The above query will return the first 10 books ordered by title in descending order
* To page through results, you can add the `OFFSET` keyword in combination with `LIMIT` to create pagination 
* `OFFSET` specifies how many results to skip. An offset of 0 skips 0 rows, an offset of 1 skips 1 row, and so on 
* E.g: 

    ```sql 
    SELECT * FROM books ORDER BY title DESC LIMIT 10 offset 10;
    ```
* The above query will show the second page of 10 rows as it will show rows from 11 to 20


### Working with Text
* This section covers methods of manipulating text including functions 

* To concatenate text from queries, use the `||` operator: 

    ```sql 
    SELECT first_name || last_name AS "Full Name" FROM customers;
    ``` 
* This will output the text from both columns joined together
* To put a space between the first and last name, this is what you would do: 

    ```sql 
    SELECT first_name || " " || last_name AS "Full Name" FROM customers;
    ```

#### A note about single and double quotes
* There is a difference between single and double quotes in SQL 
* You should use single quotes for string literals, and use double quotes for identifiers like column aliases 
* E.g: 

    ```sql 
    SELECT maximum_weight || 'lbs' AS "Max Weight" FROM ELEVATOR_DATA;
    ```
* Some database management systems don't enforce this rule though, while some do. It's important to consult documentation

* To get the length of a string, use the `LENGTH()` function: 

    ```sql 
    SELECT username, LENGTH(username) AS LENGTH FROM customers;
    ```
* The above query will return rows showing usernames and the number of characters in each username
* To uppercase a string, use the `UPPER()`; to lowercase a string, use `LOWER()`
* E.g: 

    ```sql 
    SELECT * FROM customers WHERE LOWER(email) = "email@email.com";
    ```
* This query would be useful when searching for an email that may contain capital letters
* Another e.g: 

    ```sql 
    SELECT UPPER(zip) FROM addresses WHERE country = "Canada";
    ```
* To get a substring from a string, use the `SUBSTR(<value or column>, <start>, <length>)` function. E.g: 

    ```sql
    SELECT SUBSTR(description, 1, 30) || "..." AS short_description FROM products;
    ```

* To replace text, use the `REPLACE(<value or column>, <target>, <replacement>)` function. E.g:

    ```sql
    SELECT * FROM addresses WHERE REPLACE(state, "California", "CA") = "CA";
    ```

### Aggregate and Numeric Functions
* `COUNT(<value or column>)` is used to count the number of rows for some condition or simply without a condition to identify the number of rows in a table
* Counts all non-null values unless you use an asterisk
* E.g:

    ```sql
    SELECT COUNT(DISTINCT category) FROM products;
    ```

* This query will return the number of distinct category names 
* Use the `GROUP BY <column>` keyword to generate a report where rows are grouped together by the column you specify
* E.g: 

    ```sql
    SELECT category, COUNT(name) FROM products GROUP BY category;
    ```

* `GROUP BY` is different from the `DISTINCT` keyword. `DISTINCT` returns only distinct or unique values, but `GROUP BY` returns a report in which rows are simply grouped together
* To get the sum of all columns with or without a filter condition, use the `SUM(<column>)` function. E.g: 

    ```sql
    SELECT SUM(cost) FROM orders;
    ```

* To see the sum total amount spent by each customer, for instance, you could use the `GROUP BY` keyword and group results by the user id. E.g:

    ```sql
    SELECT SUM(cost), user_id FROM orders GROUP BY user_id;
    ```

* The above query will return a report that shows the sum of the cost for all orders for each user id
* If you want to specify conditions when using the `GROUP BY` keyword, you cannot use a `WHERE` clause
* To specify conditions when using `GROUP BY`, use the `HAVING` keyword and then specify your condition. The `HAVING` clause must appear in the query after the `GROUP BY` clause. E.g: 

    ```sql
    SELECT SUM(cost) AS total_spend, user_id FROM orders 
                        GROUP BY user_id 
                        HAVING total_spend > 250
                        ORDER BY total_spend DESC;
    ```
* `HAVING` must always appear after `GROUP BY` and before `ORDER BY`    
* Since `total_spend` is an aggregation, specifying conditions based on it requires using `HAVING` and not `WHERE`
* `HAVING` instead of `WHERE` must be used when you are grouping by columns and/or using aggregate functions
* `HAVING` filters groups but `WHERE` filters individual records
* The only time `WHERE` can be used when you are using aggregate functions is when you are not filtering based on an aggregated value. E.g: 

    ```sql
    SELECT AVG(cost) as average, 
           MAX(cost) as maximum, 
           MIN(cost) as minimum, 
           user_id
    FROM orders 
    WHERE user_id > 5
    GROUP BY user_id
    HAVING average > 30;
    ```

* The above complex query is valid and will work. The query filters individual rows based on user_id, and filters aggregated values based on the aggregate average

* The `AVG(<column>)` aggregate function returns the average for the column passed
* The `MAX(<numeric column>)` and `MIN(<numeric column>)` aggregate functions are used to find the maximum and minimum values for a particular column. These work just like `AVG`
* E.g: 

    ```sql
    SELECT AVG(cost) as average, 
           MAX(cost) as maximum, 
           MIN(cost) as minimum, 
           user_id
    FROM orders 
    GROUP BY user_id;
    ```

* The above query will return a report showing the average, largest and smallest order cost for each user id

* The `ROUND(<value>, <decimal places>)` function will round floating point numbers to the number of decimal places you specify. This is useful for doing math in queries, since often the computer will return unwieldy floating point numbers by default. E.g:

    ```sql
    SELECT ROUND(price * 1.14, 2) as "Price in Ontario" FROM products;
    ```


### Dates and Time Functions
* The specifics of date and time functions differs between different datebases. It is always necessary to check documentation. Examples here are for SQLite
* To run a query based on the current date you can use the `DATE()` function and pass it the string "now"
* E.g: 

    ```sql
    SELECT * FROM orders WHERE ordered_on = DATE("now");
    ```

* "now" passed to the `DATE()` function is called a time string. A date string can be "now" or it could be a date, like "2020-07-13"
* You can also pass what are callled **modifiers** to the `DATE()` function, such as "-21 days" 
* E.g: 

    ```sql 
    SELECT COUNT(*) FROM orders WHERE ordered_on BETWEEN DATE("now") AND DATE("now", "-31 days");
    ```

* The example query would return all orders placed between today and 31 days ago
* You can add as many modifier arguments to the `DATE()` function as you want

#### Date Datatypes 
* Date type `YYYY-MM-DD`
* Time type `hour (24 hr):minute:second`
* Datetime `YYYY-MM-DD hour:minute:second`

* SQL databases provide functions for formatting these datatypes in more human-readable ways
* To trim the time off a `datetime` type, pass the datetime to the `DATE()` function
* If you want to trim the date off a `datetime` and only show the time, pass the `datetime` to the `TIME()` function
* The `STRFTIME(<format string>, <date/time/datetime string>, <modifer>)` function can be used to format dates and times in even more human readable ways by allowing you to specify a way you want to the string to appear 
* You can also add as many optional modifiers as you want just like you can with `DATE()`
* E.g: 

    ```sql
    STRFTIME("%d/%m/%Y", "2020-07-13", "+ 1 day");
    ```


## Querying Relational Databases
* This set of notes covers more intermediate and theoretical topics related to SQL databases

### Database Normalization
* In relational databases, tables of data are linked together, or related, by shared attributes - relationships between tables
* the schema describing the structure of relationships between tables has a major impact on how well the database scales as it grows and how queries on that database will perform
* relational databases organize data and give data context and meaning to the words and numbers in those tables
* the process of designing a schema that serves as a model for how data should be organized and relate to each other is called **normalization**
* reading: 
    * [Database Normalization (Explained in Simple English)](http://www.essentialsql.com/get-ready-to-learn-sql-database-normalization-explained-in-simple-english/)
    * [Normalization of Databases](https://www.studytonight.com/dbms/database-normalization.php)
* Normalization, or organizing data into relational tables, makes relational databases so performant because it significantly reduces disk space by making data more efficient
* Normalization also allows efficient updates to data without **update anomalies** which is where data gets missed when trying to update 
* When data are broken into separate relational tables with IDs, data only need to be changed once rather than hundreds, thousands or even millions of times 

#### Set Theory 
* [Set Theory resource](https://www.mathsisfun.com/sets/)
* Data in databases can be conceptualized in terms of set theory
* **set** - **Grouping of similar things together** 
* in set theory, sets are represented with {} curly braces
    * the **union** of two sets is the set resulting from adding all elements in both sets together 
    * the **intersection** of two sets is the set resulting from putting the common elements from both sets together
    * the **except** of two sets is the set resulting from putting all elements that only belong to one set but not both together 
    * These are common set operations 
* queries in relational databases are actually set operations
* Good database normalization constructs schemas in ways that make set operations on the data easy and efficient
* data can also be represented in Venn Diagrams, which are ways to visually represent sets and set operations

### Database Keys
* Database keys are columns that act as common attributes between different tables, linking them together
* Keys can also play other roles beyond acting as the links between tables 
* **Unique keys**
    * They must be unique or you will get an error
    * They can be null 
    * There can be multiple unique keys per table 
    * Unique keys can be updated as long as they remain unique
* **Primary keys** 
    * Guarantees data is unique within a column, similar to a unique key
    * Primary keys can never be null
    * A table can only have a single primary key
    * Primary keys cannot be updated
    * Primary keys do not have to be numeric, they but usually are and serve as an id column for relating data in one table to another
    * Primary keys don't usually contain any business-relevant data, they simply relate data in one table to others
    * Using numeric values is simple and helps keep disk usage low for faster database performance
    * Primary keys are often set to auto increment so that the programmer does not have to manually add it in queries
* **Foreign keys** 
    * Foreign keys are columns that relate back to the primary key in another table 
    * For example, consider a Users table and a Posts table. The Users table has a primary key called userID. The Posts table also has a column called userID, but it is a foreign key. It acts as the link between Users and Posts. Each Posts table row will have a column that contains the userID primary key from Users
    * When designing a schema during normalization, primary and foreign keys need to be matched correctly. The database does not know by itself which data should be related. These decisions have to be made based on what makes sense from a business point of view
    * **Foreign key contraint**: databases enforce the relationship between primary and foreign keys by only allowing data to be added to a table with a foreign key if that same data also exists in a table containing the matching primary key
    * Creating a foreign key to match a primary key in another table creates the foreign key constraint
    * **Referential integrity**: Foreign key values must also exist as a primary key value in the reference table. Referential integrity means that the data relationships between tables is valid
    * Referential integrity is one of the most important aspects of good relational database schema design
    * To achieve this, a foreign key must be explicitly indicated during schema design otherwise the database will not know 

### Table Relationships
* Three types of relationships between tables: one-to-one, one-to-many, and many-to-many
* These are described by how many rows can be related to each other on either side of the relationship
* **One to Many** 
    * Most common
    * One row in a table is related (through a foreign key constraint) to many rows in another table, but not vice-versa
    * E.g: In a database for a blog site, there is a Users table and a Posts table. Users has a PK USER_ID and Posts has a foreign key USER_ID. Each user in Users can have many references in the Posts table, but each Posts row can only refer back to a single row in the Users table. In simple terms, each user can have many posts, but each post can only have one user
* **Many to Many**
    * Given two tables, each row in each table could potentially relate to many rows in the opposite table 
    * Many to many relationships in a database are usually a mirror of the real life relationship between the objects the two tables represent
    * Many to Many relationships between data in two tables require a third table called a **junction table**
    * In the junction table, both primary keys from the tables with the many-to-many tables are made foreign keys
    * Therefore, many-to-many relationships never actually exist in a database. They are instead handled by two separate tables each having a one-to-many relationship with the junction table
    * This is because it is impossible to create a real many-to-many relationship between two tables since it would force you to duplicate the PK in the table you added the foreign key to, and the database will not allow you to do this
    * E.g: A table Orders and a table Parts for an auto mechanic would have a many-to-many relationship: each order could have multiple parts, and each part could be on multiple orders. To represent the relationship you need a junction table called Order_Part that has the primary keys of both Orders and Parts as foreign keys. Order_Part could then contain as many rows as needed containing partIDs and orderIDs 
    * Another example of a many-to-many relationship would be students and courses. A student can be in many courses at once, and a course can have many students in it. So aside from a Students table and a Courses table, we would need a Student_Course table containing the foreign keys studentID and courseID
* **One to One**
    * Least common type of relationship
    * A row in one table can only relate to only one row in another table and vice versa
    * In most cases, two tables with a one-to-one relationship should actually just be a single table 
    * However, separating frequently accessed columns from infrequently accessed columns could create a boost in performance by eliminating the overhead of the infrequently accessed columns
    * In concrete terms, if you had a proprietary database design that could not be modified, but you wanted to add more columns for a table, you could add an extension table that uses the PK of the first table as the FK, and simply adds more columns that you would have otherwise added to the first table if you could. In this case each PK in the first table refers only to one record in the extension table, and vice versa. They really could just be one table
    * Every database table has a one-to-one relationship with it's own data: each row has a relationship with only one PK, and each PK has only a single row--this is trivial but helps to understand the relationship
* Database designers use diagrams called Entity Relationship Diagrams to model data relationships during the design process
* Tables are referred to as entities 
* "Crow's feet" symbols are used for the many side of a relationship, and single lines are used for the one side of a relationship
* [Chen Notation](https://www.vertabelo.com/blog/chen-erd-notation/)
* [UML Class Diagrams with Cardinality](https://en.wikipedia.org/wiki/Cardinality_(data_modeling))
* [Design Elements](https://www.conceptdraw.com/solution-park/resource/images/solutions/entity-relationship-diagram-(erd)/Design_Elements(Crows-Foot-ERD).png)


### Joining Table Data
* Joins tells the database to merge data from two or more tables together in a query, leaving each table intact
* The most common joins are inner joins and outer joins

#### Inner Joins
* most common type of join 
* inner joins match records together **where values are equal on both sides of the join statement**
* E.g: 

    ```sql
    SELECT Orders.OrderID, Customers.CustomerName 
           FROM ORDERS 
           INNER JOIN Customers ON
           Orders.CustomerID = Customers.CustomerID;
    ```

    ```sql
    SELECT * FROM make 
           INNER JOIN model ON make.MakeId = model.MakeId;
    ```

* The query will only select rows from both tables that have a match between the primary and foreign keys
* It is often necessary and usually best to specify the table name and then the column name being selected with dot syntax, especially when referencing primary and foreign keys
* It is also possible to alias table names just as you can alias column names. E.g: 

    ```sql 
    SELECT mk.MakeName, md.ModelName FROM make AS mk
           INNER JOIN model AS md ON mk.MakeId = md.MakeId;
    ```

* You can also add `WHERE` clauses: 

    ```sql 
    SELECT mk.MakeName, md.ModelName FROM make AS mk
           INNER JOIN model AS md ON mk.MakeId = md.MakeId
           WHERE mk.MakeName = "Chevy";
    ```

* (note: you can still specify `WHERE` conditions and use `ORDER BY` and other keywords the same as you would in a non-complex query. You specify these in the same order, after specifying your join. The join clause always follows the `FROM` keyword in a query.)

#### Outer Joins
* not as common as inner joins, but come in handy for certain complex queries
* Outer joins return data from two or more tables whether or not the data match
* Three types: right outer join, left outer join, and full outer join
* Left outer joins return all data specified on the left, and only matching data from the table on the right
* Right outer joins return all data from the table on the right, and only matching data from the table on the left. Full outer joins return all data whether or not they match
* Some databases do not support all types of joins. SQLite for instance only supports the left outer join
* Uses the same syntax as inner joins


### Set Operations
* [Learn to use Union, Intersect, and Except Clauses](https://www.essentialsql.com/learn-to-use-union-intersect-and-except-clauses/)
* Set operations allow you to perform different tasks on separate tables
* Use to combine two or more data sets in various ways or limit one data set with another
* Four set operations: UNION, UNION ALL, INTERSECT, EXCEPT
* **UNION** 
    * [What Is the Difference Between a Join and a UNION?](https://www.essentialsql.com/what-is-the-difference-between-a-join-and-a-union/)
    * Different from an INNER JOIN
    * Union operations do not depend on a PK-FK relationship at all
    * While joins put columns together, Unions put rows together--stacks data vertically, as opposed to horizontally with joins  
    * The number of columns and their data types must match in order to do a union
    * E.g consider a database for a car dealership. There is a table DomesticMakes and a table ForeignMakes. You could get a report of all makes with the following query:

        ```sql
        SELECT name FROM DomesticMakes
        UNION
        SELECT name FROM ForeignMakes;
        ```

    * This will stack all make names vertically, assuming they have the same data type
    * WHERE clauses, other conditions and keywords are used the same as they normally are. You do not put them after the UNION keyword. The keyword just goes between two otherwise separate queries
* **UNION ALL** 
    * Very similar to UNION, except that it returns all data from both sides of the union operation, even if there are duplicates, whereas a regular union returns distinct results
* **INTERSECT** 
    * Returns only rows that exist in both sides of the set operation
    * Returns vertical rows just like the other set operations
    * Like other set operations, an intersect operation includes two separate queries with the keyword INTERSECT between them
    * E.g:

        ```sql
        SELECT name FROM DomesticMakes 
            INTERSECT
        SELECT name FROM ForeignMakes
        ORDER BY name DESC;
        ```
    * If you include PKs or any other column in your query that is likely to introduce non-identical data, you will probably get no results because an INTERSECT will ***only*** work if the data queried match identically 
* **EXCEPT** 
    * Handy to know
    * Opposite of INTERSECT
    * Like other set operations, queries must have the same number of columns and data types
    * Uses the same format as other set operations
    * Returns all records from a query **except** those that are a match with data in the second query. In other words, it returns only the data that are not in both tables
    * E.g: 

        ```sql
        SELECT MakeName FROM ForeignMake
        EXCEPT 
        SELECT MakeName FROM Make;
        ```
    * Given two tables Make and ForeignMake that have some overlapping data, the above query will return makes that are foreign only, and will leave out, or **except** data that are in both tables

### Subqueries
* A subquery is a query used in conjunction with another query, usually for the purposes of defining or narrowing the results of the data returned by the main query
* Reasons for using a subquery:
    1. Criteria for a WHERE clause is not specifically known
    2. You need a temporary dataset to join with other tables in your database
* Subqueries can be used when you want to select a data column in a set or range of data using the IN keyword:

    ```sql
    SELECT * FROM Sale WHERE CarID IN (
        SELECT CarID FROM Car WHERE ModelYear = 2015;
    );
    ```

* This is useful if you don't exactly know which CarIDs you really need, so you perform a subquery to get them, and use the results of that subquery for the IN clause in the parent query
* You can only select one column in your subquery

#### Derived tables
* Derived tables are useful when the data we want is not stored in the format that we want
* You can use a subquery to create a temporary or "derived" table to join to other data for your specific purposes
* This can come in useful when doing a lot of aggregation in your queries
* E.g:

    ```sql
    SELECT * FROM Sale AS s 
        INNER JOIN (
            SELECT CarID FROM Car WHERE ModelYear = 2015 AS t;
        )
        ON s.CarID = t.CarID;
    ```
* You must alias derived tables so you have a reference to it in the JOIN criteria. Derived tables need to be joined to data from a parent query
* Aliasing the SELECT statement in the subquery creates a temporary table (notice it has been aliased to 't') and then we join that temporary table to the columns returned by the parent query
* You can add as many columns as you want to the temporary table 

    ```sql
     SELECT * FROM Sale AS s 
        INNER JOIN (
            SELECT CarID, ModelYear, Make FROM Car WHERE ModelYear = 2015;
        ) AS t
        ON s.CarID = t.CarID;
    ```
