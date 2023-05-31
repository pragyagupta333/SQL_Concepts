# Rebuild, Reorganize Indexes

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
