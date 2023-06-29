# Join Operators of PostgreSQL Execution Plans
## 1. Hash Joins
- Hash Join is performed by creating an IN-MEMORY HASH TABLE for ONE of the tables involved in the join operation. 
- The other table is then scanned, and matching rows are found by HASHING JOIN KEYS and probing the hash table.
- It is suitable when joining large tables on an equality condition.

- PostgreSQL scans the inner relation sequentially and builds a hash table, where the hash key consists of all join keys that use the = operator. 
- Then it scans the outer relation sequentially and probes the hash for each row found to find matching join keys.

Query :
```
EXPLAIN  SELECT c.name
FROM customers c
JOIN orders o ON c.id = o.customer_id 
```

Output :
```
QUERY PLAN
-------------------------------------------------------------------------
Hash Join  (cost=12.03..26.47 rows=158 width=118)
  Hash Cond: (o.customer_id = c.id)
  ->  Seq Scan on orders o  (cost=0.00..13.50 rows=350 width=4)
  ->  Hash  (cost=10.90..10.90 rows=90 width=122)
        ->  Seq Scan on customers c  (cost=0.00..10.90 rows=90 width=122)

```

## 2. Nested Loop Joins 
- PostgreSQL scans the outer relation sequentially, and for each result row it scans the inner relation for matching rows.
- Nested loop joins are preferred if one of the sides of the join has few rows. 
- Nested loop joins are also used as the only option if the join condition does not use the equality operator
- For each row in the outer loop, the inner loop is executed to find matching rows.

Query : 
```
EXPLAIN 
SELECT *
FROM employee e
JOIN departments d ON e.department_id = d.id
```

Output :
```
QUERY PLAN
-------------------------------------------------------------------------
Nested Loop  (cost=0.14..4.79 rows=37 width=42)
  ->  Seq Scan on employee e  (cost=0.00..1.37 rows=37 width=30)
  ->  Memoize  (cost=0.14..0.48 rows=1 width=12)
        Cache Key: e.department_id
        Cache Mode: logical
        ->  Index Scan using departments_pkey on departments d  (cost=0.13..0.47 rows=1 width=12)
             Index Cond: (id = e.department_id)

```

## 3. Merge Join
- Merge Join combines pre-sorted input from both tables by comparing values of the join keys.
- It requires that both input tables are sorted on the join keys.
-  It is suitable when joining large sorted tables or when joining tables on multiple join keys.

Query :
```
EXPLAIN ANALYZE
SELECT o.order_id, c.customer_name
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
ORDER BY o.order_id;


```

Output :
```
QUERY PLAN
-------------------------------------------------------------------------
Merge Join (cost=XX.XX..YY.YY rows=ZZZZ width=WW)
  Merge Cond: (o.customer_id = c.customer_id)
  ->  Index Scan using orders_customer_id_idx on orders o (cost=AA.AA..BB.BB rows=XXXX width=UU)
  ->  Index Scan using customers_customer_id_idx on customers c (cost=CC.CC..DD.DD rows=YYYY width=VV)

```


# What Is materialize in a query plan?

Query :
```
EXPLAIN  SELECT c.name
FROM customers c
JOIN orders o ON c.id < o.customer_id
```

Output :
```
Nested Loop Inner Join (rows=10 loops=1)
  Join Filter: (o.customer_id > c.id)
Seq Scan on customers as c (rows=5 loops=1)	
 Materialize (rows=5 loops=5)	
 Seq Scan on orders as o (rows=5 loops=1)
```

- In this specific query, the materialize step is used to store the result of the "Seq Scan on customers as c" step.
- The reason for materializing the result is that it allows the materialized result set to be accessed multiple times within the subsequent Nested Loop Inner Join step.
- The materialized result can be efficiently used without re-computing it for each iteration.
- By materializing the result, the query optimizer avoids redundant computations and improves the query's overall performance.
