# SQL Tuning




## Keys of SQL performance tuning:





### [1] Query structure: 

This includes things like using proper joins, filtering data early in the query, and avoiding unnecessary subqueries.




**Appropriate type of join** (e.g. inner join, left join, etc.) and to **join on indexed columns** to improve performance.




**Filter data as early as possible** in the query so that the database server doesn’t need to retrieve and process unnecessary data.

(i.e Order where clause such that high filteration occurs as early so that processing is only on required rows or atleast lesser number of rows)





### [2] Indexing: 

Indexes can be created on columns that are frequently searched or sorted in order to speed up those operations.




Indexes work by creating a separate data structure that stores a mapping of the indexed column’s values to the corresponding rows in the table. 




**Creating indexes on frequently searched columns - WHERE Clause** 




    SELECT * FROM orders WHERE customer_id = 123; 

    index cols : customer_id

**Creating indexes on frequently sorted columns - ORDER BY or GROUP BY Clause**




    SELECT * FROM orders ORDER BY order_date;

    index cols : order_date




    SELECT * FROM orders GROUP BY catergory_name;

    index cols : catergory_name




**Creating composite indexes**

A composite index is an index that is based on multiple columns. Creating a composite index on columns that are frequently used together




    SELECT * FROM orders WHERE customer_id = 123 ORDER BY order_date;

    index cols : customer_id,order_date




**Covering index** Covering indexes are indexes that include all the columns that are being selected in the query. They can be used to improve performance by reducing the number of disk I/O operations that are required to retrieve the requested data. 




    SELECT customer_id, order_date, product_name FROM orders WHERE customer_id = 123;

    index cols : customer_id,order_date,product_name

### [3] Database configuration: 

This includes things like adjusting memory and buffer pool settings, adjusting the settings of the query optimizer, and configuring the database to use the appropriate storage engine.

(Refer)[https://medium.com/illumination/a-step-by-step-guide-to-sql-performance-tuning-indexing-partitioning-data-distribution-and-de79dcfeef89#c110]

### [4] Partitioning:

 Partitioning large tables can improve query performance by allowing the database to only scan the partitions that are relevant to a given query.

### [5] Caching: 

Caching query results can improve performance by reducing the need to repeatedly run the same query.

### [6] Data distribution: 

Data should be distributed evenly across the storage devices to reduce contention and increase performance.




## How to choose the Order of columns in composite indexes?

[1] Selective and High Cardinality Columns:




Columns with high selectivity (a large number of unique values) and are frequently used in search or filtering operations should be placed first in the composite index. This allows the database to quickly narrow down the search space and find the relevant rows faster.




[2] Frequently Used Columns:




Columns that are often used in search conditions, joins, and sorting operations should be considered for inclusion in the index. Placing these columns higher in the index can improve the overall performance of the queries that rely on them.




[3] Equality and Range Operators: 




If your queries frequently include both equality and range operators (e.g., "WHERE column1 = value AND column2 > value"), it's generally better to put the equality columns first in the index, followed by the range columns.




[4] Leftmost Prefix Rule: 




The order of columns in a composite index matters due to the "leftmost prefix rule." This means that the index can only be fully utilized if the query uses a subset of the leftmost columns in the index definition. For instance, an index on columns (A, B, C) can be used for queries that use (A), (A, B), or (A, B, C), but not for queries that only use (B, C) without specifying A.




### Use TABLOCKX hints while inserting into a table and TABLOCK while merging.




In SQL Server, TABLOCKX is a locking hint that can be used in a SELECT, UPDATE, DELETE, or MERGE statement to acquire an exclusive lock on an entire table during the execution of the query. This hint prevents other transactions from reading or modifying the locked table until the current transaction completes.




### Use WITH (NOLOCK) while querying the data from any table.




In SQL Server, WITH (NOLOCK) is a locking hint that can be used in a SELECT statement to read data from a table without acquiring shared locks. When NOLOCK is used, the query is allowed to read uncommitted data, which is also known as a "dirty read."




By default, when a SELECT statement reads data from a table, it acquires shared locks to prevent other transactions from modifying the data until the read operation is completed. These shared locks ensure data consistency and isolation but can lead to blocking and reduced concurrency in multi-user environments.




When you use the WITH (NOLOCK) hint, the query bypasses these shared locks, and the read is performed without waiting for or acquiring any locks. This can improve query performance in certain scenarios, especially when you need to prioritize read speed over data consistency. However, it comes with the risk of reading uncommitted data that might later be rolled back due to ongoing transactions.




### SET NOCOUNT ON




SET NOCOUNT ON is a T-SQL statement used in SQL Server to suppress the message indicating the number of rows affected by a Transact-SQL statement. By default, when you execute a SQL statement that modifies data (e.g., INSERT, UPDATE, DELETE), SQL Server returns a message indicating the number of rows affected by that statement.




Using SET NOCOUNT ON prevents these messages from being sent to the client, thereby reducing network traffic and improving the overall performance of the application or script.




### Avoid Cursors since cursor are very slow in performance.




Cursors involve row-by-row processing, which can lead to performance issues and increased resource consumption.




### Use Table variable in place of Temp table.

 Use of Temp tables required interaction with TempDb database which is a time taking task.
