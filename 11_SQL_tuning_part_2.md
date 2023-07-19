# SQL Tuning Part 2
### STATISTICS TIME ON
Displays the number of milliseconds required to parse, compile, and execute each statement.


- #### Parse and Compile Time :
 
 The time taken to parse and compile the query to check the syntax of the query is termed  Parse and Compile time.

- #### Execution Time :

The CPU time used by the query to fetch the data is termed Execution time.

- #### Completion time : 
The exact time at which the query returned the result is termed Completion time.

### SET STATISTICS IO ON
Displays Physical IO and logical IO 

Physical IO is related to accessing data pages on disk and logical IO is related to accessing data pages in memory (data cache).

Logical reads: Number of reads that were performed from the buffer cache.

Physical reads: Number of reads that were performed from the storage device as they were not available in the cache.

### Inner joins vs WHERE clause
We should use inner join for merging two or more tables rather than using the WHERE clause. WHERE clause creates the CROSS join/ CARTESIAN product for merging tables. CARTESIAN product of two tables takes a lot of time.

        SET STATISTICS IO ON
        SELECT p.Name, Color, ListPrice 
        FROM SalesLT.Product p, SalesLT.ProductCategory pc
        WHERE P.ProductCategoryID = pc.ProductCategoryID



        SET STATISTICS TIME ON
        SELECT p.Name, Color, ListPrice FROM SalesLT.Product p
        INNER JOIN  SalesLT.ProductCategory pc
        ON P.ProductCategoryID = pc.ProductCategoryID
        
So, we can see from the above outputs that inner join takes almost half time as compared to join using WHERE clause.
