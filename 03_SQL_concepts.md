## Postgres V/s SQL Server

- #### Postgres has [JSON](https://www.postgresql.org/docs/current/datatype.html) as DataType but SQL Server does not support [JSON](https://learn.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql?view=sql-server-ver16) as DataType
  - However, SQL server has Built in Functions For JSON
 
| Function  | Description |
| ------------- | ------------- |
| ISJSON  | Tests whether a string contains valid JSON.  |
| JSON_VALUE|	Extracts a scalar value from a JSON string. |
| JSON_QUERY |	Extracts an object or an array from a JSON string.  |
|JSON_MODIFY |	Updates the value of a property in a JSON string and returns the updated JSON string. |

        # This Will Give error in sql server
        CREATE TABLE test(
                a JSON
        )


        # This Will ensure all insertion in column 'a' is of JSON type
        CREATE TABLE test(
        a varchar,
        CHECK (isJSON(a)=1)
        ) 
- #### Postgres Has ARRAY as DataType, SQL does not 

        # Works In postgres, but not SQL server                
        create table test(
        a text[] )

## What is Schema ?
A schema is a logical collection of database objects such as tables, views, stored procedures, indexes, triggers, functions.

## Need of schema in database
- Facilitate security management i.e who can access what database objects,you can grant someone access to a schema without exposing your entire database to them.
- Serves as a namespace, that is preventing name clashes between objects from different schemas.
- They partition your database to make management easier.

## Unique Keys V/S Primary Keys 
- Unique key can have NULLs but primary key cannot
- Many unique keys but only one primary key
Note : In SQL Server, Unique Key Columns have only one null value allowed per column

The uniqueness constraint guarantees that non-null values are unique, and it treats null values as a single distinct value. As a result, SQL Server allows only one row with a null value in a column with a unique constraint.

# Functions V/S Stored Procedure
## Functions
- Functions are used for computations. Ex : A Function that calculates Total Revenue for a product.
- Functions can  return a value  using "returns" clause
- Functions are typically used within SQL statements, such as SELECT, WHERE, and JOIN clauses
- Functions do not include transaction control statements like BEGIN TRANSACTION, COMMIT, and ROLLBACK
- User-defined functions cannot be used to perform actions that modify the database state. i.e. INSERT,UPDATE,DELETE not possible using Functions

## Stored Procedures
- Stored procedures encapsulate a set of SQL statements (like INSERT INTO) that perform a set of or a specific task.
- Stored procedures do not have a return value by default i.e No Return clause. But they include output parameters that allow them to return values.
- Stored procedures need to be explicitly called by name using the EXECUTE or CALL statement.
- Stored Procedures include transaction control statements like BEGIN TRANSACTION, COMMIT, and ROLLBACK
-  INSERT,UPDATE,DELETE can be used in Stored procedures

## Triggers 
Logics which are implemented when events like insert,update or delete occurs

Types in sql server :
- After trigger : After event(insert,update,delete) has happened logic will be implemented
- Before trigger : Before event(insert,update,delete) occurs logic will be implemented
- Instead Of Trigger : Instead of event, logic will be implemented

## Identity Column in SQL Server
Helps to declare an auto-incrementing columns

## Transactions
Helps us to treat series of activity(insert,update,etc) as one logical unit i.e either everything will be sucessfull (will commit) or everything  will be rollbacked

## Cross Join 
Cartesian Product i.e Every record of table is joined with other table records. No "ON" Clause

## Self Referencing Table
- Self reference tables are those tables who have primary key and foreign key in the same table.
-  a self-referenced table is a table that has a foreign key column that references another column within the same table

In example below,
The ManagerID column is a foreign key referencing the EmployeeID column within the same table.

![self_referencing ](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/8255ab07-f47a-4523-9c50-9cd35fd3eefe)


Self-referenced tables are useful when dealing with hierarchical data structures such as organizational charts, product categories, or file systems, where each entity can have a parent or child relationship within the same table.

## Self Join 
When you make joins ( inner,left,right) with same table it’s called as Self join.

## Between clause
Helps in specifing Range of values (Inclusive)
                
                SELECT EmployeeID
                FROM Employees 
                WHERE EmployeeID between 2 and 4
                # returns EmployeeID 2,3,4
## Subquery
Subquery is a query inside a query (nested query).In Subquery first the inner query gets evaluated
and then outer query.

## Can inner Subquery return multiple results?
Yes , Inner query can return multiple results but then in where clause you will need to us the “IN”
keyword.

## Co-related Subquery
In co-related query first the outer query sends records to the inner query , inner query then
evaluates and sends its back to the outer query.

## Performance : Joins V/s Subquery 

Joins work on tables, tables have indices, and indexed queries are faster.
In case of subquery, subquery will return only data set ( which has no index). So if the data returned by subquery is large, then of course it will degrade performance













## char V/S varchar
- char is fixed length
- varchar is variable lenght

## char V/S nchar
- for simple english characters use char
- for unicode characters, multilingual languages(non english languages) use nchar (for languages like chiniese,tamalian,etc)
- char = 1 bytes, nchar = 2 bytes
