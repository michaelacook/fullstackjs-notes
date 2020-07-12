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