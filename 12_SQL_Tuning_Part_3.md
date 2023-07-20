
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

### In which senarios database won't use defined index ?

- Outdated Statistics: 

If the statistics on the table and index are outdated or inaccurate, the optimizer may not make an optimal decision. Regularly updating statistics is crucial to ensure the optimizer has the necessary information for query optimization.

- Small Tables: 

For very small tables, using an index might result in additional overhead, and the optimizer may choose to perform a full table scan instead

- Index Hint Absence: 

If there is no index hint provided in the query, the optimizer will make its own decision based on cost estimates. Even if an index would improve performance, the optimizer might choose a different execution plan.

- Functions or Type Conversion: 

If the columns in the query are wrapped inside functions or type conversions, the optimizer might not be able to use the index effectively.

Ex : Cast(), lower(), Round(), etc

Solution to this : 
-  Avoid wrapping the indexed columns inside functions or type conversions

- Create a Computed Column: If you have control over the database schema, you can create a computed column that stores the result of the function or type conversion. For example, you can create a new column called "rounded_salary" that stores the rounded values of the "salary" column. Then, you can create an index on this new computed column. This way, the optimizer can use the index on "rounded_salary" more effectively.

- Use Materialized Views: Some database systems support materialized views, which are precomputed results of a query. If the query with the function or type conversion is used frequently, you can create a materialized view with the query results and then create an index on the materialized view to improve query performance.

- Indexing Expressions: In some database systems, you may be able to create an index on an expression, which allows you to index the result of a function or type conversion directly. Check if your database system supports this feature, and if so, create an index on the expression used in the query
- Database Tuning: Ensure that your database server is properly tuned for performance. Properly configuring memory, disk I/O, and other database settings can have a significant impact on query performance.



- Hints:
 If some hints are used in the query, they may override the optimizer's decision to use an index.

 [Read](https://serhatcelik.wordpress.com/2022/05/29/why-oracle-not-using-index/#:~:text=Because%20if%20this%20first%20column,won%27t%20use%20the%20index.&text=Another%20of%20the%20major%20reasons,supply%20in%20the%20where%20clause)


 # Full-text indexing

 In Oracle, full-text indexing is implemented using Oracle Text, which is an Oracle-supplied feature that enables powerful text search and indexing capabilities. Oracle Text allows you to create and manage indexes on text columns in a table, which significantly improves the performance of text-based searches.

Here's a step-by-step guide on how to create and use a basic full-text index in Oracle using Oracle Text:

Step 1: Enable Oracle Text
Before you can use Oracle Text, ensure that it is installed and enabled in your Oracle database. In most cases, Oracle Text is included by default in the Oracle database installation.

Step 2: Create a Table with a Text Column
Let's create a sample table called "documents" that contains a text column named "content." This column will store the textual data you want to index and search.

```sql
CREATE TABLE documents (
  doc_id NUMBER PRIMARY KEY,
  title VARCHAR2(100),
  content CLOB
);
```

Step 3: Enable the Text Indexing for the Text Column
To enable full-text indexing for the "content" column, you need to define a special column called "CTXSYS.CONTEXT" in the table. This column will be used to create and maintain the text index.

```sql
ALTER TABLE documents ADD (content_index CTXSYS.CONTEXT);
```

Step 4: Create the Full-Text Index
Now, you can create the full-text index on the "content" column using the CTX_DDL.CREATE_INDEX procedure.

```sql
BEGIN
  CTX_DDL.CREATE_INDEX(
    index_name   => 'documents_content_idx',
    table_name   => 'documents',
    column_name  => 'content_index',
    datastore    => 'my_datastore'
  );
END;
/
```

In this example, we named the index "documents_content_idx." You can use any appropriate name for your index. The "datastore" parameter specifies a logical name for the index's storage. You can use the default datastore or create a custom one to specify storage options.

Step 5: Populate the Index with Existing Data
If you already have data in the "content" column, you need to populate the full-text index with this data. You can use the CTX_DDL.SYNC_INDEX procedure for this purpose.

```sql
BEGIN
  CTX_DDL.SYNC_INDEX(index_name => 'documents_content_idx');
END;
/
```

Step 6: Perform Full-Text Searches
Now that the index is created and populated, you can perform full-text searches using the CONTAINS operator in your SQL queries.

```sql
SELECT doc_id, title
FROM documents
WHERE CONTAINS(content, 'search_text', 1) > 0;
```

In this query, "search_text" is the text you want to search for in the "content" column. The CONTAINS function will use the full-text index to efficiently retrieve the matching rows.

Remember that full-text indexing and searching can be quite complex, and Oracle Text provides various advanced features to customize and optimize text search. You can explore the Oracle documentation for Oracle Text to learn more about its capabilities and how to use them effectively in your applications.

### How to use cache execution plan for future use in sql server

In SQL Server, you can use the Query Store feature to capture and use cached execution plans for future use. The Query Store tracks query execution plans and performance metrics, allowing you to force a specific plan or allow SQL Server to automatically select the best plan based on historical data. Here's how you can use the Query Store to leverage cached execution plans:

1. **Enable Query Store** (if not already enabled):
   Before using the Query Store, you need to ensure that it is enabled for your database. You can enable it at the database level using the following SQL:

   ```sql
   ALTER DATABASE YourDatabase SET QUERY_STORE = ON;
   ```

   Enabling the Query Store starts capturing query information for future analysis.

2. **Query Store Configuration** (optional):
   By default, the Query Store captures query information using the default configuration settings. However, you can modify the configuration to suit your requirements. For example, you can change the data retention period, capture mode, or query store size. Refer to the SQL Server documentation for more details on Query Store configuration options.

3. **Identify the Query of Interest**:
   Determine the specific query for which you want to leverage a cached execution plan. You can obtain this query from your application or through monitoring tools.

4. **Force a Specific Execution Plan**:
   To force a specific execution plan for a query, you can use the following SQL:

   ```sql
   USE YourDatabase;
   EXEC sp_query_store_force_plan @query_id = <QueryID>, @plan_id = <PlanID>;
   ```

   Replace `<QueryID>` and `<PlanID>` with the respective identifiers of the query and plan you want to force. You can find these identifiers by querying the Query Store system views, such as `sys.query_store_query` and `sys.query_store_plan`.

   Forcing a plan ensures that SQL Server uses the specified plan for the query, even if the optimizer suggests a different plan based on its cost-based decisions.

5. **Automatic Plan Correction** (optional):
   SQL Server's Query Store also offers the Automatic Plan Correction feature, which automatically corrects performance regressions by forcing a previous known good plan. You can enable this feature to let SQL Server handle the plan selection automatically.

   ```sql
   ALTER DATABASE YourDatabase SET QUERY_STORE (OPERATION_MODE = READ_WRITE, AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON));
   ```

   With this setting enabled, SQL Server will monitor query performance and automatically switch to a known good plan if a regression is detected.

By leveraging the Query Store in SQL Server, you can capture, analyze, and control the execution plans for your queries. This enables you to address performance issues, force specific plans when necessary, and benefit from the Automatic Plan Correction feature for efficient plan management.

### How to use cache execution plan for future use IN SQL Server

In SQL Server, you can use the Query Store feature to capture and use cached execution plans for future use. The Query Store tracks query execution plans and performance metrics, allowing you to force a specific plan or allow SQL Server to automatically select the best plan based on historical data. Here's how you can use the Query Store to leverage cached execution plans:

1. **Enable Query Store** (if not already enabled):
   Before using the Query Store, you need to ensure that it is enabled for your database. You can enable it at the database level using the following SQL:

   ```sql
   ALTER DATABASE YourDatabase SET QUERY_STORE = ON;
   ```

   Enabling the Query Store starts capturing query information for future analysis.

2. **Query Store Configuration** (optional):
   By default, the Query Store captures query information using the default configuration settings. However, you can modify the configuration to suit your requirements. For example, you can change the data retention period, capture mode, or query store size. Refer to the SQL Server documentation for more details on Query Store configuration options.

3. **Identify the Query of Interest**:
   Determine the specific query for which you want to leverage a cached execution plan. You can obtain this query from your application or through monitoring tools.

4. **Force a Specific Execution Plan**:
   To force a specific execution plan for a query, you can use the following SQL:

   ```sql
   USE YourDatabase;
   EXEC sp_query_store_force_plan @query_id = <QueryID>, @plan_id = <PlanID>;
   ```

   Replace `<QueryID>` and `<PlanID>` with the respective identifiers of the query and plan you want to force. You can find these identifiers by querying the Query Store system views, such as `sys.query_store_query` and `sys.query_store_plan`.

   Forcing a plan ensures that SQL Server uses the specified plan for the query, even if the optimizer suggests a different plan based on its cost-based decisions.

5. **Automatic Plan Correction** (optional):
   SQL Server's Query Store also offers the Automatic Plan Correction feature, which automatically corrects performance regressions by forcing a previous known good plan. You can enable this feature to let SQL Server handle the plan selection automatically.

   ```sql
   ALTER DATABASE YourDatabase SET QUERY_STORE (OPERATION_MODE = READ_WRITE, AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON));
   ```

   With this setting enabled, SQL Server will monitor query performance and automatically switch to a known good plan if a regression is detected.

By leveraging the Query Store in SQL Server, you can capture, analyze, and control the execution plans for your queries. This enables you to address performance issues, force specific plans when necessary, and benefit from the Automatic Plan Correction feature for efficient plan management.
