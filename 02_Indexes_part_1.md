
## Heap Table v/s Clustered Table
- A **Heap table** is a table in which, the data rows are not stored in any particular order.
- The heap table contains **no clustered index** (which causes sorting of table) but might **have one or more non clustered index**(causes no sorting of table).
- A **clustered table** is a table that has a predefined **clustered index** on one column or multiple columns of the table that defines the storing order of the rows.
- 
Heap Table : 

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/140691d3-cf2f-45c5-9484-11be1036f235)

Clustered Table :

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/b82bd9a2-168d-404a-ad34-080827c0183d)

## When to use a heap
- A heap can be used as a staging table for large and unordered insert operations. Because data is inserted without enforcing a strict order, the insert operation is usually faster than the equivalent insert into a table with clustered index.
- The table is small with just a few rows.
- Data is always accessed through nonclustered indexes.

## Why it is not recommended to create indexes on small tables?
It takes the SQL Server Engine less time scanning the underlying table than traversing the index when searching for specific data. In this case, the index will not be used but it will still negatively affect the performance of data modification operations, as it will be always adjusted when modifying the underlying 
table’s data.


## Index 
Indexes Helps to speed up the data retrieval and the query processing operations from a database table or view, by providing quick access to the database table rows, without the need to scan all the table’s data, in order to retrieve the requested data.

## How Indexes Helps ? With Example
- In query shown below, Without having an index on the EmployeeID column, SQL Server will scan all the table rows to retrieve the requested data. 

        SELECT * FROM Employees WHERE EmployeeID = 101
        # No index : Table scan
        # With index : Index Seek

- With index on EmployeeID along with performing a search based on the EmployeeID value, the SQL Server Engine will seek for the requested EmployeeID values in the index and use that index to locate the rest of the employees’ information from the related rows in the source table, providing a significant performance enhancement and reducing the effort required to locate the requested data

- This rapid search capabilities provided by the index is achieved due to the fact that, the SQL Server index is created using the shape of **B-Tree structure** which provides the SQL Server Engine with a fast way to move through the table rows based on index key, that decides to navigate left or right, to retrieve the requested values directly, without scanning all the underlying table rows. 

OR 

When data is not indexed, sequential search will be used which is slow.
but when the column/data is indexed then b-tree data structure is used using which scanning through each cell is not required.

# Clustered index v/s Non-Clustered index
- Both indexes have B-tree structure
## Clustered index
- Clustered Index leaf nodes points to actual data.
- In a Table, Only one clustered index 
- Determines physical order of table and sorts it
- Clustered index is stored along with the table (primary key of that table creates clustered index)

## Non-Clustered index
- Non-clustered index leaf nodes uses pointers to point towards actual data ( i.e it points to leaf node of clustered index which stores actual data)
- Many non-clustered indexes possible for single table
- Does'nt affects physical order of table
- Non-clustered index is stored at one place and actual table is stored at another place

## When to use Clustered index :
Queries that involve range-based or sequential access of data can benefit from a clustered index.

## When to use Non-Clustered index :
Queries that involve searching, filtering, or sorting based on specific columns can benefit from a non-clustered index.

## B-Tree structure for clustered index :

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/3cd4ac2d-5db3-47d9-a929-4f0264b601a3)


## B-Tree structure for Non-clustered index :

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/0d86b9bb-d5da-4c55-bb7d-4ae88fc23ddb)


## Index Efficiency Depends Upon :
### Index Depth 
The index depth is the number of levels from the index root node to the leaf nodes. An index that is quite **deep** will suffer from **performance degradation** problem due to many levels of traversing to reach to search value.

However, SQL Server indexes can have **large number of nodes in each level**. This helps in improving the efficiency of the created index by avoiding the need for excessive depth within the index.

### Index Density 
It is a measure of the lack of uniqueness of the data in a table. A dense column is one that has a high number of duplicates.

### Index selectivity
It is a measure of how many rows scanned compared to the total number of rows. An index with high selectivity means a small number of rows scanned when related to the total number of rows.


