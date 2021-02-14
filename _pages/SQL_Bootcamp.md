---
title: "SQL Bootcamp"
date: 2021-02-14T10:00:00-05:00
permalink: /SQL_Bootcamp/
author_profile: true
sitemap: true
---

**Notes from Udemy Complete SQL Bootcamp. Course covers a good general overview of SQL and use PostgreSQL as it's main database.**


SELECT

    SELECT colum_name FROM table_name
    
    SELECT c1,c2 FROM table
    
    SELECT * selects all the columns from a table
    
    SELECT DISTINCT column FROM table to only pull distinct entries. This shows you the distinct values for the column.
    
    SELECT COUNT(column) FROM table;
        This simply returns the number of rows in the table.
    SELECT COUNT (*) FROM table; <- gives the same result.
    SELECT COUNT (DISTINCT name) FROM table; This gives you the number of distinct names. This shows how many distinct vales but not the actual values.
    
    ------------------
SELECT WHERE
    
    SELECT column1, column2
    FROM table
    WHERE conditions;
    
    uses usual comparison operators such as +, <, >. 
    <> or !=  desigate not equal to
    
    Logical operators are AND, OR, NOT
    
    SELECT name,choice FROM table
    WHERE name = 'David';
    
    SELECT name,choice FROM table
    WHERE name ='David' AND choice = 'Red';
    
    
ORDER BY
    
    SELECT column_1,column_2
    FROM table
    ORDER BY column_1 ASC/DESC
    
    - ascending is the default
    - technically you can order by a column that you have not selected.
    
    LIMIT
    allows us to limit the nuber of rows retured in a query
    
    BETWEEN
    Match a value against a range of values
    e.g. BETWEEN 5 AND 15
    
    - inclusive of the high and low values
    - can also do NOT BETWEEN, NOT version is not inclusive
    - can be used with dates, format is YYYY-MM-DD (inclusiveness is system dependent - OR may be a better option)
    
    IN
    used to check a value from a series in a list
    value IN (option1,option2,...,option_n)
    
    eg.
    SELECT color FROM table
    WHERE color IN ('red','blue')
    
    can also do NOT IN

-------------------

LIKE and ILIKE

    string pattern matching with the use of wild cards
    %   Matches any sequence of characters
    _   Matches any single character
    
    WHERE name LIKE 'A%'
        all names that begin with "a"
    WHERE nme LIKE '%a'
        all names that end with "a"
    
    LIKE is case-sensitive but ILIKE is case-insensitive
    
    WHERE title LIKE 'Mission Impossibel _'
    would find:
        Mission Impossible 1
        Mission Impossible 2
        Mission Impossible 3
        ...
        
    You can use multiple multiple underscores
        
    WHERE name LIKE '_her%'
    finds:
        Cheryl
        Theresa
        Sherri
    
    Postgresl does support regex
    https://www.postgresql.org/ docs
    
------------
    
GROUP BY

        - Aggregate function - takes multiple inputs and generates a single output.
        - Most common are
            - AVG()
                - floating point so use ROUND() to specify number of digits
            - COUNT()
            - MAX()
            - MIN()
            - SUM()
        - Aggregate functions happen only in the SELECT or HAVING clauses.
        
    SELECT ROUND(AVG(replacement_cost),2)
        FROM film;
        
        This will round the average and limit the precision to 2 decimal places.
        
    SUM - sums all the values in a column
    
    With GROUP BY you need to choose a categorical column to GROUP BY
    
    SELECT category_col, AGG(data_col)
    FROM table
    GROUP BY category_col
    
    OR
    
    SELECT category_col, AGG(data_col)
    FROM table
    WHERE category_col != 'A'
    GROUP BY category_col
    
    GROUP BY clause must appear directly after a FROM or WHERE statement
    
    SELECT customer_id,staff_id,SUM(amount) FROM payment
    GROUP BY staff_id,customer_id
    
    If a column is a date you should do:
        SELECT DATE(payment_date) FROM payment
        This pulls just the date from the time stamp.
        
-------------------

HAVING

    Allows filtering after an aggregation has already taken place
    
    SELECT company, SUM(sales)
    FROM finance_table
    WHERE company != 'Google'
    GROUP BY company
    HAVING SUM(sales)>1000
    
--------------------

JOIN
    
    A JOIN allows you to combine multiple tables

    AS
    Allows you to create an alias for a column or result
    
        SELECT column AS new_name
        FROM table
    
        SELECT SUM(ammount) AS net_revenue
        FROM payment
    
    This just changes the name in the result. Can't use the alias name in a WHERE or GROUP BY
    
    INNER JOIN
        An INNER JOIN will result with the set of records that match in both tables.
        
       SELECT * FROM TableA
       INNER JOIN TableB
       ON TableA.col_match=TableB.col_match
    
        SELECT * FROM Registrations
        INNER JOIN Logins
        ON Registrations.name = Logins.name
        
        PostgreSQL treats JOIN as an INNER JOIN
        If a column only appears in one table you don't need to specify what table it comes from.
        
        SELECT payment_id,payment.customer_id, first_name FROM payment
        INNER JOIN customer
        ON payment.customer_id = customer.customer_id
        
    FULL OUTER JOIN
        Allow us to specify how to deal with values only present in one table
        
       Sample tables

| REGISTRATIONS || LOGINS |
|-|-|-|-|-|
| reg_id | name |log_id | name |
| 1 | Andrew | 1 | Xavier |
| 2 | Bob | 2 | Andrew |
| 3 | Charlie | 3 | Yoland |
| 4 | David | 4 | Bob |
    
    
    SELECT * FROM TableB
    FULL OUTER JOIN TableA
    ON TableA.col_match = TableB.col_match
    
    SELECT * FROM Registrations FULL OUTER JOIN Logins
    ON Registraions.name = Logins.name
    
    Results

| reg_id | name |log_id | name |
|-|-|-|-|-|
| 1 | Andrew | 2 | Xavier |
| 2 | Bob | 4 | Andrew |
| 3 | Charlie | null | nul |
| 4 | David | null | null |
| null | null | 1 | Xavier |
| null | null | 3 | Yolanda |

    FULL OUTER JOIN with WHERE
        Get rows uniquie to either table
        This is the opposite of a FULL INNER JOIN
    
    SELECT * FROM TableB
    FULL OUTER JOIN TableA
    ON TableA.col_match = TableB.col_match
    WHERE TableA.id is null OR TableB.id is null
    
    Results
| reg_id | name |log_id | name |
|-|-|-|-|-|
| 3 | Charlie | null | nul |
| 4 | David | null | null |
| null | null | 1 | Xavier |
| null | null | 3 | Yolanda |

    LEFT OUTER JOIN
     Results in the records that are in the left table, if there are no match with the right table, theresults are null.
     
     Records that are exclusive to the left table or in both the left and right tables.
     
     SELECT * FROM TableA
     LEFT OUTER JOIN TableB
     ON TableA.col_match = TableB.col_match
     
     Can use LEFT JOIN or LEFT OUTER JOIN
     
     SELECT * FROM Reistrations
     LEFT OUTER JOIN Logins
     ON Registrations.name = Logins.name
     
         Results

| reg_id | name |log_id | name |
|-|-|-|-|-|
| 1 | Andrew | 2 | Andrew |
| 2 | Bob | 4 | Bob |
| 3 | Charlie | null | nul |
| 4 | David | null | null |

     SELECT * FROM Reistrations
     LEFT OUTER JOIN Logins
     ON Registrations.name = Logins.name
     WHERE Logins.log_id IS null
     
     Only show records that are in Table A and have no corresponding record in Table B
     
     Results
| reg_id | name |log_id | name |
|-|-|-|-|-|
| 3 | Charlie | null | nul |
| 4 | David | null | null |


    RIGHT JOIN
    Essentially the same as a LEFT JOIN but the tables are switched.
    
    
    UNION
    The UNION operator is used to combine the result-set of two or more SELECT statements
    
    SELECT column_name(s) FROM table1
    UNION
    SELECT column_name(S) FROM table2
    
Sales2021_Q1

| name | amount |
|-|-|
| David | 100 |
| Claire | 50 |

Sales2021_Q2

| name | amount |
|-|-|
| David | 200 |
| Claire | 100 |

    SELECT * FROM Sales2021_Q1
    UNION
    SELECT * FROM Sales2021Q2;
    
| name | amount |
|-|-|
| David | 100 |
| Claire | 50 |
| David | 200 |
| Claire | 100 |


Timestamps

    TIME - Contains only time
    Date - Contains only date
    TIMESTAMP - Contains time and date
    TIMESTAMPTZ - Contains time, date, and time zone
    
    SHOW - used to show various parameters.
    SHOW TIMEZONE;
    
    SELECT NOW()   - Shows current time stamp and time zone. No ";"
    SELECT TIMEOFDAY()  - Basically same info but in an easier to read format.
    SELECT CURRENT_TIME  - Time with time zone
    SELECT CURRENT_DATE  - Date info
    
EXTRACT
    EXTRACT - Allows you to extract a sub-component of a date value
    
    EXTRACT(YEAR FROM date_col)

AGE
    AGE - Calculates and returns the current age given a time stamp
    
    AGE(date_col)
    
TO_CHAR
    General function to convert data types to text.Useful for time stamp formatting.
    
    
    TO_CHAR(date_col, 'mm-dd-yyyy')
    
    SELECT TO_CHAR(payment_date,'MONTH-YYYY')
    FROM payment
    
Math Functions

    Many available, see docs
    
    SELECT  ROUND(rental_rate/replacement_cost,4)*100 AS percent_cost FROM film
    

String Functions and Operators

    See docs - https://www.postgresql.org/docs/current/functions-string.html
    
    SELECT upper(first_name) || ' ' || upper(last_name) FROM customer
    
        Returns first and last names in upper case separated by a space - BILL BUMKIN
    
    SELECT LOWER(LEFT(first_name,1)) || LOWER(last_name) || '@gmail.com' FROM customer
    
        Returns first initial in lowercase concatenated to lastg name in lowercase concatenated to @gmail.com - bbumkin@gmail.com
        
Subquery

    SELECT student,grade
    FROM test_scores
    WHERE grade > (SELECT AVG(grade)
    FROM test_scores)
    
    SELECT student,grade
    FROM test_scores
    WHERE student IN
    (SELECT student
    FROM honor_roll_table)


    SELECT film_id,title
    FROM film
    WHERE film_id IN
    (SELECT inventory.film_id
    FROM rental
    INNER JOIN inventory ON inventory.inventory_id = rental.inventory_id
    WHERE return_date BETWEEN '2005-05-29' AND '2005-05-3)
    ORDER BY title;


EXISTS

    Tests for the existence of rows in a subquery
    
    SELECT column_name
    FROM table_name
    WHERE EXISTS
    (SELECT column_name FROM 
    table_name WHERE condition);
    
    
    SELECT first_name,last_name
    FROM customer AS c
    WHERE EXISTS
    (SELECT * FROM payment AS p
    WHERE p.customer_id = c.customer_id
    AND amount > 11)
    
SELF JOIN

    A query in which a table is joined to itself. Must use an alias for the table to clear up any ambiguity.
    
    SELECT tableA.col, tableB,col
    FROM table AS tableA
    JOIN table AS tableB ON
    tableA.some_col = tableB.other_col
    
    SELECT f1.title, f2.title, f1.length
    from film AS f1
    INNER JOIN film AS f2 ON
    f1.film_id != f2.film_id
    AND f1.length = f2.length
    

Creating Databases and Tables
---------------------------------

Data Types

    - Boolean - true or false
    - Character - char, varchar, text
    - Numeric - integer, floating point
    - Temporal - date, time, timestamp, interval
    - UUID - Universally Unique Identifiers
    - Array
    - JSON
    - Hstore key-value pair
    - Special types such as network addresses and geometric data
    
    postgresql.org/docs/current/datatype.html


Primary and Foreign Keys

    Primary - is a column or group of columns used to identify a row uniquely in a table. Also important for joining tables together.
    
    Foreign - is a field or group of fields used to uniquely identify a row in another table.
    The table that contains the foreign key is called the referencing or child table. There can be multiple foreign keys.
    The table that the foreign key refernces is called the referencing or parent table.
    
    
Constraints

    - Rules on data columns in tables
    - Prevent invalid data from being entered.
    
    - Column constraints
        - Applied to a column
        
       
    - NOT NULL ensures column cannot have a NULL value
    
    - UNIQUE ensures all values are different
    - PRIMARY Key and Foreign Key
    - CHECK ensures that all values in a column satisfy certain conditions
    - EXCLUSION ensures that if any two rows are compared on the specified column or expression using the specified operator, not all of these comparisons will return TRUE
    
    
    - Table constraints
        - Applied to the entire table
    
    - CHECK (condition)  - to check a condition when inserting or updating data
    
    - REFERENCES  - to constrain the value stored in a column that must exist in a column in another table
    
    - UNIQUE (column_list)
    - PRIMARY KEY (column_list)  note that it is possible to have multiple primary keys per table


CREATE

    Create a table

    CREATE TABLE table_name (
       column_name TYPE column_constraint, 
       column_name TYPE column_constraint, 
       table_constraint table_constraint) 
       INHERITS existing_table_name;
    
    
    CREATE TABLE players(
    player_id SERIAL PRIMARY KEY,
    age SMALLINT NOT NULL
    );
    

INSERT

    Allows you to aff rows to a table.

    INSERT INTO table (column1, column2, ...)
    VALUES
        (value1, value2, ...)
        (value2, value2, ...) ,...;
    
    
    INSERT INTO table (column1, column2,...)
    SELECT column1,column2
    FROM another_table
    WHERE condition;
    
        When inserting from another table constraints must be met. i.e. if NOT NULL then data must be provided. Also SERIAL columns do not need to have data provided.
    
UPDATE

    Allows for changing values in columns
    
    UPDATE table
    SET column = value1,
      column2 = value2,...
    WHERE
      condition;
      
      UPDATE account
      SET last_login = CURRENT_TIMESTAMP
        WHERE last_login IS NULL;
    
            WHERE can be left off. If i is allrows would be updated with CURRENT_TIMESTAMP
    
    UPDATE join
    
    UPDATE TableA
    SET original_col = TableB.new_col
    FROM tableB
    WHERE tableA.id = TableB.id
    
    Return affected rows
    
    UPDATE account
    SET last_login = created_on
    RETURNING account_id,last_login
    
    UPDATE account_job
    SET hire_date =account.created_on
    FROM account
    WHERE account_job.user_id = account.user_id;


DELETE

    DELETE FROMM table
    WHERE row_id = 1
    
    DELETE FROM tableA
    USING tableB
    WHERE tableA.id = tableB.id
    
    DELETE FROM table <- these deletes all rows from the table
    
    Can add a RETURNING call at the end to show what has been deleted.
    

ALTER

    Allows for changes to a table's structure.
    
    ALTER TABLE table_name action
    
    e.g.
    
    ALTER TABLE table_name
    ADD COLUMN new_col TYPE
    
    DROP COLUMN col_name
    
    ALTER COLUMN col_name
    
    SET DEFAULT value
    
    DROP DEFAULT
    
    SET NOT NULL
    DROP NOT NULL
    
    ADD CONSTRAINT constraint_name
    
    https://www.postgresql.org/docs/current/sql-altertable.html
    
    
DROP 
    
    Allows you to remove a column from a table.
    
    Does not remove columns which are depended upon by otgher things such as views, triggers, or storage procedures without the additional CASCADE clause.
    
    ALTER TABLE table_name
    DROP COLUMN col_name CASCADE
    
    Commonly writtrn as:
    
    ALTER TABLE table_name
    DROP COLUMN IF EXISTS col_name
    
    Can do multiple DROPS:
    
    DROP COLUMN col_name1
    DROP COLUMN col_name2
