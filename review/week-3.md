# Week 3 Review

* Database
    - An organized collection of structured information stored in a computer system such as a server
    - 2 types of databases
        - Relational databases (SQL-based)
            - Data is organized into rows and columns in tables
                - Row: an individual record within a table
                - Column: a single attribute of a row in a table
            - Tables can have relationships with each other
                - Note: this however is not why a relational database is called relational
                - Multiplicity is the technical term for the relationships between tables (one-to-one, one-to-many,
                many-to-many)
        - Non-relational databases (NoSQL)
            - Document based (ex. MongoDB)
            - Graph based (ex. Neo4J)
* Schema
    - Within a database, a schema is like a "folder" for different tables
    - At a higher level, a schema is a formal structure of data defined within a relational database
        - Tables
            - Relationships (multiplicity)
            - Columns
                - Datatypes
                - Constraints
    - The schema is defined using DDL (data definition language)
    - The schema of a database is visualized using an ERD (entity relationship diagram)
* SQL Sublanguages
    - DDL (Data definition language)
        - CREATE
            - `CREATE TABLE <table_name> (<column name> <datatype> <constraints>, ...)`
            - Create the table
        - ALTER
            - `ALTER TABLE <table_name> [ADD | MODIFY | DROP] <column name>`
            - Modify the table
        - DROP
            - `DROP TABLE <table_name>`
            - Delete the table
        - TRUNCATE
            - `TRUNCATE <table_name>`
            - Delete all data inside table, but keep the table
    - DML (Data manipulation language)
        - CRUD operations (Create Read Update Delete)
            - General term for operations on records of information
        - INSERT
            - `INSERT INTO <table_name> (c1, c2, c3) VALUES (..., ..., ...), (..., ..., ...)`
        - SELECT
            - `SELECT c1, c2, c3`
                - `*` for all columns
            - `FROM <table_name>`
            - `<join_type> JOIN <table_name_2> ON ... = ...`
            - `WHERE ... = ...`
                - Filter rows (before grouping)
            - `GROUP BY c1, c2`
                - Create groups to utilize **aggregate** functions
                - You can have multiple columns you are grouping by (or just one)
            - `HAVING ... = ...`
                - Filter groups
            - `ORDER BY c3, c4`
                - Sorting
                    - Descending or ascending order
                        - Ascending by default (ASC)
                        - If we want descending order, then add `DESC` at the end of the clause
                            - ex. `ORDER BY first_name DESC`
                - You can have multiple columns you are ordering by (or just one) 
        - UPDATE
            - Modify existing records
                - `UPDATE <table_name> SET c1 = ..., c2 = ..., c3 = ... WHERE ... = ...`
        - DELETE
            - Delete existing records
                - `DELETE FROM <table_name> WHERE ... = ...`
    - DQL (Data query language)
        - Same thing as `SELECT` from DML
        - There's just a different sublanguage for it, since some people disagree that SELECT is part of DML
    - DCL (Data control language)
        - Used to manage the permissions and security of the database
            - GRANT
                - `GRANT <permission> to <username>`
            - REVOKE
                - `REVOKE <permission> to <username>`
    - TCL (Transaction control language)
        - Used to manage transactions
        - COMMIT
            - Any DML operations that were executed before COMMIT will be persisted permanently
        - ROLLBACK
            - Any DML operations that were executed between two commits will be erased when executing ROLLBACK
            - However, keep in mind that committed transactions cannot be rollback
        - SAVEPOINT
            - Create a checkpoint that you can rollback to
                - Keep in mind that once you commit though, you cannot rollback
        - Rollback and commit is not used in place of database backups
            - That's something that database admins would need to be doing to keep older archives of the database information
* Constraints
    - When creating tables, we need to define columns for the table
        - Column name
        - Datatype
        - Constraint(s)
    - PRIMARY KEY
        - Uniquely identifies a record in a table
        - Implicitly `NOT NULL` and `UNIQUE`
        - Composite Keys
            - Sometimes you can have a PRIMARY KEY made up of 2 or more columns
                - ex. songs table
                    - `album_id` and `track_no`
    - FOREIGN KEY
        - Represents a reference to the primary key of another table
        - ex. We have a todos table with a `user_id` column that points to the `id` column in the users table
            - In this case, used to establish a one-to-many between users and todos
                - "We have a one-to-many between users and todos"
                - "We have a many-to-one between todos and users"
    - UNIQUE
        - We cannot insert a record if that particular value for a UNIQUE enforced column already exists
            - ex. username might be a column that should be UNIQUE
            - ex. email might be a column that should be UNIQUE
    - NOT NULL
        - A NOT NULL enforced column must always have a value associated
        - Cannot insert a record that is NULL for that particular column
    - CHECK
        - Perform validation on the database side when trying to insert a record
    - DEFAULT
        - If you insert a record but don't specify a value for that column, then the DEFAULT value specified will be the value utilized
* Joins
    - Combine rows from two or more tables
        - You can have multiple joins chained together
    - Types of joins
        - LEFT / RIGHT JOIN
            - LEFT JOIN will keep all records in the "left" table, and if there are no matches for a particular "left record" in the right table, then the right side will be all null values
            - Vice versa for RIGHT join
            - LEFT and RIGHT are interchangeable depending on the order in which you specify your tables
                - FROM table1 is the left table
                - The ... JOIN table2 is the right table
                - By convention, everybody just uses LEFT join and not RIGHT join
        - INNER JOIN
            - Default join (if we don't specify the join type)
            - Records where both the left and right side match are the only ones displayed
        - OUTER JOIN (FULL OUTER JOIN / FULL JOIN)
            - LEFT and RIGHT records are all displayed, even if there's no match
                - If there's no match, then the other side will be all null
    - Syntax
        - `SELECT ..., ..., ...`
        - `FROM <left table name>`
        - `<join type> JOIN <right table name>`
        - `ON <condition>`
            - Usually FK = PK
* Transaction
    - A series of CRUD operations (DML) that will be persisted to the database
        - INSERT
        - UPDATE
        - DELETE
    - By default, SQL queries are set to autocommit
        - Meaning each DML command is committed immediately after
    - However, if we turn off autocommit, we can utilize TCL in order to either
        - `COMMIT` the transaction
        - `ROLLBACK` the transaction
    - Note: keep in mind, psycopg does not autocommit, so we do need to call the `.commit()` method on the connection object
    - ACID properties (for transactions)
        - Atomicity
            - A transaction is either completely successful or not at all
            - If an issue occurs in the middle of a transaction, any change prior to failure within that transaction will be rolled back
            - ex. Setting up a bank transfer as a single transaction
                - Need to UPDATE the record of the sending account to be lower
                - Need to UPDATE the record of the receiving account to be higher
                - Need both UPDATEs to be totally successful for a transfer to occur
        - Consistency
            - Data is in a consistent format prior to the transaction as well as after the transaction
            - The RDBMS (relational database management system) should uphold
                - Datatypes
                - Constraints
                - Referential integrity
        - Isolation
            - If you have two concurrent (at the same time) transactions, they should ideally not interfere with each other
            - Each transaction should have a result that is the same regardless of whether another transaction is happening or not at the time
        - Durability
            - Once you commit a transaction, it is permanently persisted to the hard drive (permanent memory)
            - If you are in the middle of a transaction and have not yet committed, those changes will not be permanent (you could lose that transaction if the database lost power, as an example)
* Referential Integrity
    - You shouldn't have values for a foreign key that refer to primary key values that don't exist
        - If you did, this would imply that there is the existence of **orphan records**
        - SQL databases do not allow this to happen
        - ex. in the todos table, there is a record with user_id = 6, but there is no id = 6 in the users table
            - NOT ALLOWED
    - You are guaranteed to have the corresponding record that you are referring to
    - The reason to be aware of this is that you cannot delete records if another record's FK is referring to that record
        - If you want to delete a user that has todos, then you must also delete the todos
        - For project-0, if you want to delete a customer, you must delete all of their bank accounts first
* Multiplicity
    - Relationships between tables
    - Types
        - One-to-one
            - Doesn't matter which table the foreign key is contained in
            - The foreign key must have the `UNIQUE` contraint
                - This is how you know it's one-to-one instead of one-to-many
            - ex. one user has one user_profile
        - One-to-many
            - Foreign key must be on the many table
                - ex. foreign key must be on the todos table for us to have a one-to-many between users and todos
        - Many-to-many
            - Need a junction table to establish a many-to-many
                - The junction table will have two columns
                    - One column will be a foreign key pointing to one of the table, one column will be a foreign key pointing to the other table
                    - The primary key will be a composite key composed of these two columns
            - ex. many students have many teachers
                - A student can have many teachers
                - A teacher can have many students
* Aggregate v. Scalar functions
    - Scalar function: acts on individual rows, input from a row gives a single output for that row
        - ex. 10 rows, 10 outputs
        - examples
            - Arithmetic (not really a function, but still "scalar" in nature)
            - `LENGTH()`
            - `UPPER()`
            - `LOWER()`
            - `CONCAT()`
            - `ABS()`
    - Aggregate function: acts on groups, takes all of the values for the rows in a group and gives a single value for that group
        - ex. 3 groups, 3 outputs
        - Use GROUP BY <column name> to create groups based on the rows in that group having the same value for that column
        - examples
            - `MIN()`
            - `MAX()`
            - `AVG()`
            - `COUNT()`
            - `SUM()`

## Junction table example

| student_id | teacher_id |
| :--------- | :--------- |
| 1 | 1 |
| 1 | 2 |
| 1 | 3 |
| 2 | 1 |
| 2 | 3 |

## Read phenomena
- Related to transactions and the isolation property (from ACID)
- There are certain issues that have been described that could occur when two transactions occur concurrently

* Dirty Reads (uncommitted reads)
    - A transaction can read the changes made by another transaction even when that other transaction has not committed the change yet
    - "Dirty" because if the other transaction's changes were rolled back, our first transaction would've been "dirtied" by that change that didn't actually get committed
* Non-repeatable Reads
    - A transaction reads the data once and then reads it again, but the data changed between the two reads
        - This could occur even if we solved the dirty read problem
        - The other transaction might have committed a change while our current transaction is doing multiple reads
    - "Non-repeatable results": the results changed
* Phantom Reads
    - Where we query multiple rows of data that match a certain condition, and when we query it again, then the number of records changed
        - Almost like a ghost disappeared or appeared (hence phantom)
* Solution
    - Isolation levels
        - Read Uncommitted
            - Speed: fastest
            - All 3 read phenomena occur
                - Dirty reads
                - Non-repeatable reads
                - Phantom reads
        - Read Committed
            - Speed: fast
            - Solves dirty read problem
            - 2 read phenomena occur
                - Non-repeatable reads
                - Phantom reads
        - Repeatable Reads
            - Speed: medium
            - Solves dirty read and non-repeatable read problem
            - 1 read phenomena occurs
                - Phantom reads
        - Serializable
            - Speed: slow
            - No read phenomena occur

## Normalization
- The process of reducing data redundancy and increasing data integrity
- Helps to better organize data in the database
- There are many different levels of normalization
    - 0NF
        - Total chaos in the database, no particular guidelines for how data looks
    - 1NF
        - Must have a primary key
        - Data is "atomic" (not to be confused with atomicity for transactions)
            - There should only be a single phone number in the phone_number column
                - ex. `512-826-0001, 512-826-0002, 512-826-0003` inside of a single column is bad
                - ex. `512-826-0001` is good
            - What if you do want multiple phone numbers?
                - Delete the phone_number column, and create another table called phone_numbers
                - The phone_numbers table should then have a foreign key pointing to the id in the users table
                    - one-to-many between users and phone_numbers
            - 1NF can guide you towards utilizing one-to-many relationships
    - 2NF
        - 1NF + Cannot have partial dependencies
            - If you don't have a composite primary key, then you are automatically in 2NF if you meet the requirements for 1NF
        - Partial dependency is where we have a column that is only describing a single candidate key of the composite key
            - Candidate key: a single column making up the composite key
        - ex. songs table
            - Composite key w/ 2 candidate keys
                - `album_id`
                - `track_no`
            - We cannot have a column called `album_description`, because that only describes the album_id candidate key
            - However, `song_description` is a valid column, because it is describing the entire composite key
                - `album_id` + `track_no` together identify a particular song
    - 3NF
        - 2NF + Cannot have transitive dependencies
        - Transitive dependency is where we have a column B describing the primary key, and then we have column C describing column B
            - C ---describes---> B ---describes---> PK
            - Not allowed for 3NF
        - ex. songs table
            - Composite key w/ 2 candidate keys
                - `album_id`
                - `track_no`
            - We can have a column `song_genre`, but not `genre_description`
                - genre_description would be describing song_genre which then desribes the primary key
                    - NOT ALLOWED
- Summary normalization
    - "You must have a key (and data must be atomic)" (1NF)
    - "Describe the entire key" (2NF)
    - "Nothing but the key" (3NF)