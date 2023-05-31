# Reindex, Rebuild, Reorganize Indexes

Both rebuild and reorganize are index maintainence techniques.

### Need of rebuild and reorganizing indexes
The solution to [fragmented indexes](https://github.com/pragyagupta333/SQL_Concepts/blob/main/04_Index_part_2.md) is to rebuild or reorganize indexes.

But, before considering maintenance of indexes, it is important to answer two main questions:

1. What is the degree of fragmentation?

Fragmentation can be easily detected by running the system function sys.dm_db_index_physical_stats which returns the size and the fragmentation information for the data and indexes of tables or views in SQL Server.

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/4d0708e9-c089-4195-94ea-ec628541e750)

The results returned after running the procedures include following information:

- **avg_fragmentation_in_percent** – average percent of incorrect pages in the index
- **fragment_count** – number of fragments in index
- **avg_fragment_size_in_pages** – average number of pages in one fragment in an index

2. What is the appropriate action? Reorganize or rebuild?

*Generally accepted solution based on the percent of fragmentation* (not always necessary)
- **Fragmentation is less than 10%** – **no de-fragmentation** is required. It is generally accepted that in majority of environments index fragmentation less than 10% in negligible and its performance impact on the SQL Server is minimal.
- **Fragmentation is between 10-30%** – it is suggested to perform **index reorganization**
- **Fragmentation is higher than 30%** – it is suggested to perform **index rebuild**

# Rebuild Index
- Rebuilding an index involves recreating the entire index structure from scratch.
- This operation drops the existing index and rebuilds it based on the underlying table's data.
- During the rebuild process, the index is unavailable for read or write operations.
- Rebuilding an index updates index statistics 
- An index rebuild simply drops and recreates the index which means that index rebuild will solve both the internal and external fragmentation. 
            
      ALTER INDEX ALL ON MyTable REBUILD;

# Reorganize Index 
- Reorganizing physically reorganizes the leaf-level pages of an index without recreating the entire index structure.
- The index remains available for read and write operations during the reorganize process.
- Reorganize operation does not update index statistics.
- An index reorganize only solves external fragmentation by moving pages around.

      ALTER INDEX ALL ON MyTable REORGANIZE;

While index reorganization is a pure cleanup operation that leaves the system state as it is without locking-out affected tables and views, the rebuild process locks the affected table for the whole rebuild period, which may result in long down-times that could not be acceptable in some environments.

# Reindex 
- Reindexing helps ensure that the index metadata is up to date and accurate, which can improve the performance of query optimization. 
- Reindexing usually does not require exclusive locks on the table being indexed. It can often be performed online, allowing concurrent read and write operations on the table during the reindex operation

# Update Statistics 
Statistics store information about the data distribution of the column value(s) in your tables as well as the total number of rows. For indexes it stores the distribution of key values. 

## How are SQL UPDATE STATISTICS used
Existing statistics are used by the query optimizer in order to generate the most efficient query plan for execution. The query optimizer uses the statistics to determine when an index should be used, how to access those indexes, how best to join tables, etc.

# References 
https://solutioncenter.apexsql.com/why-when-and-how-to-rebuild-and-reorganize-sql-server-indexes/

https://www.mssqltips.com/sqlservertip/6720/update-statistics-sql-server/
