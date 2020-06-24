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

## Retrieving Data
* To retrieve all columns for a table run 
    
    `SELECT * FROM <table name>;`

    * You would not normally use this command for large data sets, as it would get all the data for a table and cause performance issues as a result

* To retrieve specific columns for a table run 

    `SELECT <column>, <column> ... FROM <table name>;`

* E.g: 

    `SELECT title, author FROM books;`

* You can select columns in any order you like and the data will be returned in the order you specify

### Aliasing
* You can generate reports that provide aliases for column names 
* This is often done to make the reports more human-readable 
* Simply use the `AS` keyword after specifying a column name, and then specify your alias 
* E.g:

    `SELECT first_name AS "First Name" from users;`
* If you are going to use multiple words for an alias it must be in quotation marks
* This may differ depending on the specific relational database you're using, so reading documentation for your specific implementation is important
* You can also leave out the `AS` keyword: 

    `SELECT first_name "First Name" from users;`

* This syntax isn't as clear as should be avoided
* Using aliases does not change the column name in the database. It is only a way to alias column names in reports

## Filtering 
* You can filter the results of a query based on conditions you set
* One of the most common ways to do this is using the `WHERE` clause in a query:

    `SELECT <column> FROM <table> WHERE <condition>`

* E.g: 

    `SELECT book FROM books WHERE catalogue_number = 12345`

* You can use just about any operator in your where clause to perform any sort of comparison. Eg: 

    `SELECT score FROM results WHERE score >= 20;`

* You can add multiple conditions with the `AND` and `OR` keywords. E.g:

    `SELECT title FROM books WHERE author = "J. K. Rowling" AND first_published < 2000;`

    `SELECT * FROM books WHERE first_published >= 1900;`

* Comparison operators can also be used on columns that contain date types
* If you wanted to chain many `OR` conditions, your query statement could become unwieldy fast. SQL provides the `IN` keyword to filter a column using a number of values. Consider this query:

    `SELECT first_name, email FROM patrons WHERE library_id = "MCL1001" OR library_id = "MCL1100" OR library_id = "MCL1011";`

* This could be simplified to: 

    `SELECT first_name, email FROM patrons WHERE library_id IN ("MCL1001", "MCL1100", "MCL1011");`

* To perform the negation of this query, you only need to add the `NOT` keyword: 

    `SELECT first_name, email FROM patrons WHERE library_id NOT IN ("MCL1001", "MCL1100", "MCL1011");`

* This will search for first name and email for a patron where the library ID is not one of the specified IDs in parentheses
* There are times when you want to search for a record that has a column that falls within a range of possible values. Instead of using two `AND` clauses, you can use the `BETWEEN` keyword:

    `SELECT <column(s)> FROM <table> WHERE <column> BETWEEN <minimum> AND <maximum>;`

* E.g: 

    `SELECT * FROM products WHERE price BETWEEN 10.99 AND 12.99;`

* When using the `BETWEEN` keyword you must supply the minimum value first or the query will return no results

* To search for a record that is at least a partial match with a supplied value, use the `LIKE` keyword. `LIKE` allows searching that is more flexible than the equality operator, which only returns strict matches
* In order to work, the `LIKE` keyword needs at least one wildcard, which is the `%` symbol. This symbol is used to stand for any other possible character. Use it as a stand-in for anything or any number of other characters. 
* For example, `%ache` will match with all of the following: backache, headache, stomach ache 
* `swim%` will match with: swimmer and swimming
* And putting a search term between two `%` symbols will return any result that matches any part of the search term
* Using wildcards results in searches that are case-insensitive 
* E.g: 

    `SELECT title FROM books WHERE title LIKE '%design patterns%';`

    * This will search for any book with the terms "design patterns" in the title

* Make sure to remember to still use quotation marks

* Some times some columns in a row may be empty, or `null`
* To check if a column is empty, use `IS NULL` to check if the column in question is null: 

    `SELECT * FROM users WHERE last_name IS NULL;`

* Likewise, if you want to search for a record with the condition that a certain column is not empty, you can use `IS NOT NULL` instead


