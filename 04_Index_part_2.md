# Fragmentation In SQL Indexes
Index Fragmentation reduces index performance over time as data is inserted, updated, or deleted in a table.

There are two types of fragmentation :
#### 1. Internal Fragmentation 
- Internal fragmentation is when there is free space on a page, due to deletes, updates and inserts, and there is more free space on a page than required.
- Ex : Let's pretend at the beginning of the day we have a table with 40 pages that are 100% full, 
but by the end of the day we have a table with 50 pages that are only 80% full because of various delete and insert statements throughout the day.
This causes an issue because now when we need to read from this table we have to scan 50 pages instead of 40 which should may result in a decrease in performance. 

#### Reasons For Internal Fragmentation 
- Insertion of variable-length data: If an index allows variable-length data types (such as VARCHAR), and the length of the inserted value is smaller than the maximum length defined for that column, it can create empty space within the data pages.
- Updates or deletes: When an existing value within an indexed column is updated or deleted, the new value might be smaller than the previous value, leaving empty space within the data page.

Every index page can hold a certain number of records based on the size of the index, but that does not guaranteed that the page always hold maximum number records

Initially : 

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/54c58a27-bbf0-49e8-b038-0aa13a86c816)

After Many Inserts,updates and deletes :

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/e6b60a5e-762d-48e3-b007-5f66a32e44b4)

#### Why Internal Fragmentation is bad ?
- It increase the size of the database file. It need more disk space to store the additional pages and reduce the performance of Backup and Restore.
-  When you run queries that scan part or complete table/index, if you have internal fragmentation on that table/index, it causes additional page reads. 
 In our example, the entire data can be stored in 5 pages. When the query needs to do index scan it has to read 10 pages instead of 5 pages. Which means 50% more I/O.

#### 2. External Fragmentation
 - When the logical order of the index key values is not in sync with the physical order of the index pages.
 - External Fragmentation is caused by pages that are out of order. 
 - Ex : Let's pretend at the beginning of the day we have a perfectly ordered table. During the day we issue hundreds of update statements possibly leaving some empty space on one page and 
 trying to fit space into other pages. This means our storage has to jump around to obtain the data needed instead of reading in one direction.
 
 Initially : 
 
 ![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/077c10b1-5329-44bd-b5aa-bbb9c5d4748d)

After inserting value 4 :

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/106c9543-254f-459a-a743-e52c9bc4fd4b)

Explanation Of above figure : 

While inserting the value 4 into the table it has to place in the Page 1 between value 3 and 5 but unfortunately Page 1 does not have any free space to occupy one more record. The only option is perform a page split by dividing the Page 1 evenly by leaving half of the data in Page 1 and moving half of the data to new  page (Page 4). From Fig 4 we can understand that the logical order of the Page 4 is not matching with the physical order.

#### Reasons For external Fragmentation 
- While allocating pages for new table , SQL server allocate pages from mixed extend till it reaches the 8 pages. There is possibility of having the first 8 pages from 8 different extents. 
- When all records are deleted from a page, the page will be de-allocated from the index(The de-allocation  of pages will not happen immediately) which create gap and increase the fragmentation.
- Once object reached 8 pages size, SQL server will start allocating uniform extent to the objects.While allocation uniform extent to an index, next sequential extent to the current extent might be already allocated to other objects/indexes.
 
#### Why external fragmentation is bad ?
 In the case of ordered index scan ,external fragmentation might become a degrading factor for performance. The degradation of the performance is because the disk drive's heads have to jump around on the physical disk, rather than performing just contiguous read operations.Also note that external fragmentation will not affect the performance once the pages are loaded into the buffer pool.
 
 
 
 **The solution to fragmented indexes is to rebuild or reorganize indexes** discussed in part_3 of indexes
 
 # References 
 [SQL Server central](https://www.sqlservercentral.com/blogs/sql-server-index-fragmentation-understanding-fragmentation)
 
 [MSSQLtips](https://www.mssqltips.com/sqlservertip/4331/sql-server-index-fragmentation-overview/)
