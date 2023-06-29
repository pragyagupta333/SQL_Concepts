

# Scan Operations In Execution Plans Of postgres
## Types Of Scans 

## 1. Seq Scan
- A sequential scan (table scan), is a method for retrieving rows from a table in the order they are stored on disk.
-  It involves **reading the entire table**, block by block, to find the requested rows. 

Query : 
```
EXPLAIN SELECT * FROM employee;
```

Output : 
```
QUERY PLAN
-------------------------------------------------------------------------
Seq Scan on employee  (cost=0.00..11.00 rows=500 width=43)
```

Explantion :
- PostgreSQL is scanning the table **sequentially from start to end** to retrieve all the rows.

- Used in scenarios where the **data is small**, the query conditions don't benefit from index usage, or when the query planner estimates that a full table scan is more efficient than using indexes due to the selectivity of the conditions.

It's worth noting that sequential scans **can be slower than index scans for large tables** or queries with selective conditions. In such cases, creating appropriate indexes on columns frequently used in query conditions can significantly improve query performance.

## 2. Index Scan 
Indexes are used to enhance query execution by providing faster access to specific data based on the indexed columns. 
Query : 
```
EXPLAIN SELECT * FROM employee WHERE age >= 30;
```

Output : 
```
QUERY PLAN
-------------------------------------------------------------------------
Index Scan using idx_age on employee  (cost=0.28..8.30 rows=2 width=43)
  Index Cond: (age >= 30)
```

Explantion :
- The idx_age index, which is created on the age column of the employee table, is used to efficiently locate the rows that satisfy the given condition (age >= 30).

## 3. Index only scan
The index has all columns to satisfy the query (Select Clause)
Query : 
```
EXPLAIN SELECT DISTINCT department_id FROM employee;
```

Output : 
```
QUERY PLAN
-------------------------------------------------------------------------
Unique  (cost=0.15..8.17 rows=13 width=4)
  ->  Index Only Scan using idx_department_id on employee  (cost=0.15..7.91 rows=13 width=4)
```

Explantion :
- An index-only scan in a PostgreSQL execution plan indicates that the **query can retrieve the required data directly from the index**, **without accessing the underlying table data**. 
- It's an optimization technique that can **improve query performance** when specific conditions are met, such as querying only the columns included in a covering index.
- The conditions for an index-only scan to occur include:

    - Querying only the columns that are included in the index.
    - The index being a "covering index," meaning it includes all the columns required by the query.



## 4. Bitmap Index Scan
Uses bitmap indexes to efficiently retrieve the required rows based on a set of conditions or predicates. 

Query : 
```
EXPLAIN SELECT * FROM employee WHERE age = 30 OR age = 40;
```

Output : 
```
QUERY PLAN
-------------------------------------------------------------------------
Bitmap Heap Scan on employee  (cost=4.16..8.19 rows=2 width=43)
  Recheck Cond: ((age = 30) OR (age = 40))
  ->  BitmapOr  (cost=4.16..4.16 rows=2 width=0)
        ->  Bitmap Index Scan on idx_age  (cost=0.00..2.08 rows=1 width=0)
              Index Cond: (age = 30)
        ->  Bitmap Index Scan on idx_age  (cost=0.00..2.08 rows=1 width=0)
              Index Cond: (age = 40)


```
Explanation :

- Two Bitmap Index Scans are performed on the idx_age index to identify the rows with ages 30 and 40, respectively. 
- The resulting bitmaps are combined using a BitmapOr operation, and the final bitmap is used to perform a Bitmap Heap Scan on the table to fetch the corresponding rows.

 Steps : 
- The query conditions are evaluated, and for each condition, a bitmap index scan is performed on the corresponding index.
- The index scan identifies the matching rows and marks them as "1" in a bitmap.
- Multiple bitmap indexes are combined using logical operations, such as OR or AND, to produce a final bitmap.
- The final bitmap is used to access the actual table data, known as a Bitmap Heap Scan, to retrieve the required rows efficiently.



