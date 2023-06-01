# Locks 
Locks are placed on SQL Server resources (like rows, columns, tables, etc.) to prevent multiple transactions from using the same resource at the same time.
# Some Types Of Locks 
1. Shared (S) Lock:
- Shared locks are used for read operation i.e SELECT operations
- Multiple transactions can hold shared locks on the same resource simultaneously.
- Shared locks do not block other shared locks but block exclusive locks.
- Enable other sessions to perform select operations but prevent updates 

Example : 

T1 :
```
BEGIN TRANSACTION;
SELECT Name, Salary FROM Employees WHERE EmployeeID = 1;
```
T1 holds shared lock on row with employeeID = 1 in employee table

T2:
```
BEGIN TRANSACTION;
SELECT Name, Salary FROM Employees WHERE EmployeeID = 1;
```
T2 attempts to read same row i.e attain shared lock on the row. (shared lock allows other shared locks)

T3: 
```
BEGIN TRANSACTION;
UPDATE Employees Set Name = 'Pragya' WHERE EmployeeID = 1;
```
T3 attempts to modify(attain exclusive lock) on same row which has shared lock, this won't be successful.

2. Exclusive (X) Lock:
- Exclusive locks are used for insert/update/delete operations.
- Only one transaction can hold an exclusive lock on a resource.
- Exclusive locks block both shared and exclusive locks.
- Prevents other users from accessing the resource.

3. Update (U) Lock:
- Update locks indicate that an update operation may occur and block other transactions from acquiring exclusive locks.
- Update locks are a special type of lock used during certain isolation levels (e.g., Read Committed).
- A solution to the cycle deadlock problem
- The update lock allows other transactions to read the row concurrently but prevents them from acquiring exclusive locks to modify the same row until the update lock is released.
- Prevents Phantom Reads : When a transaction reads a range of rows based on a condition, it can acquire update locks on the rows it reads, ensuring that no new rows satisfying the condition are inserted until the transaction completes.
```
-- Transaction T1
BEGIN TRANSACTION;
SELECT * FROM Employees WHERE DepartmentID = 1;
-- T1 acquires an update lock on the rows in the Employees table that satisfy the condition DepartmentID = 1.

-- Transaction T2
BEGIN TRANSACTION;
UPDATE Employees SET Salary = Salary + 1000 WHERE DepartmentID = 1;
-- T2 attempts to acquire an exclusive lock to modify the rows with DepartmentID = 1 but is blocked by T1's update locks.

-- Transaction T1
-- T1 continues to read the data, as it holds the update locks.
COMMIT;
-- T1 releases the update locks, allowing T2 to acquire an exclusive lock and proceed with the update.
```

4. Intent Lock:
- Intent locks are used to indicate the intent of a transaction to acquire locks at a higher level.
- For example, if a transaction holds an intent lock for an exclusive lock on a table, it prevents other transactions from acquiring exclusive locks on individual rows within that table. This helps maintain data integrity and prevents conflicting locks.
- Intent locks help prevent conflicts between different lock types at different levels of the lock hierarchy.
- Intent locks can be of two types: Intent Shared (IS) and Intent Exclusive (IX).

5. Schema Lock:
Schema locks are used to control access to database schemas.
Schema locks ensure that concurrent transactions do not modify the schema while another transaction is using it.
Schema locks can be of two types: Schema Stability (Sch-S) and Schema Modification (Sch-M).

# Deadlock 
Deadlock is a Concurrency Problems in SQL Server where two or more transactions hold exclusive locks on a resource and at the same time are waiting for resources 
which are locked by other transactions in cyclic manner.


# Isolation levels
It defines how one transaction is isolated from other transactions in terms of data modifications made by different transactions.

# Isolation anomalies :
They refer to inconsistent or unexpected behavior that can occur when  transactions are executing concurrently at different isolation levels. 
1. Dirty Read: 
- A transaction reads data that has been modified by another uncommitted transaction 
- Causes data inconsistency as modfying transaction can rollback 
```sql
-- Transaction T1
BEGIN TRANSACTION;
UPDATE Accounts SET Balance = Balance - 100 WHERE AccountID = 1;

-- Transaction T2
BEGIN TRANSACTION;
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SELECT Balance FROM Accounts WHERE AccountID = 1;
-- T2 reads the uncommitted balance value modified by T1.
```
2. Non-Repeatable Read: 
- A transaction re-reads the same data multiple times and sees different values because it has been modified by other committed transaction

```
-- Transaction T1
BEGIN TRANSACTION;
SELECT * FROM Employees WHERE EmployeeID = 1;
-- T1 reads the row with EmployeeID = 1

-- Transaction T2
BEGIN TRANSACTION;
UPDATE Employees SET Salary = Salary + 1000 WHERE EmployeeID = 1;
COMMIT;

-- Transaction T1
SELECT * FROM Employees WHERE EmployeeID = 1;
-- T1 reads the same row, but the Salary value has changed due to T2's update.
```

3. Phantom Read: 
A phantom read occurs when a transaction re-executes same query based on certain consition to find different set of rows 
due to changes made by other concurrent transaction that can satisfy same consition
```
-- Transaction T1
BEGIN TRANSACTION;
SELECT * FROM Products WHERE Category = 'Electronics';
-- T1 reads all products with the 'Electronics' category

-- Transaction T2
BEGIN TRANSACTION;
INSERT INTO Products (Name, Category) VALUES ('Smartphone', 'Electronics');
COMMIT;

-- Transaction T1
SELECT * FROM Products WHERE Category = 'Electronics';
-- T1 reads the same query again, but now there is an additional row ('Smartphone') due to T2's insertion.
```


# Transaction Isolation Levels
1. Read Uncommitted: 
- A transaction can read data written by other uncommited transaction 
-  It allows Dirty reads, non-repeatable read, phantom read. 
- Allows high level of concurrency but risks data inconsistency
```sql
-- Transaction 1
BEGIN TRANSACTION;
UPDATE Customers SET Balance = Balance - 100 WHERE CustomerID = 1;

-- Transaction 2
BEGIN TRANSACTION;
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SELECT Balance FROM Customers WHERE CustomerID = 1;
-- Transaction 2 reads the updated balance even though Transaction 1 has not committed yet.
```

2. Read Committed: 
- This isolation level ensures that a transaction reads only committed data. 
- It prevents dirty reads but allows non-repeatable reads and phantom reads.

```sql
-- Transaction 1
BEGIN TRANSACTION;
UPDATE Customers SET Balance = Balance - 100 WHERE CustomerID = 1;

-- Transaction 2
BEGIN TRANSACTION;
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SELECT Balance FROM Customers WHERE CustomerID = 1;
-- Transaction 2 reads the original balance until Transaction 1 commits.
```

3. Repeatable Read: 
- This isolation level ensures that a transaction sees a consistent snapshot of data throughout the transaction. 
- In other words, same read query of a transaction will see same results in that transaction
- It prevents dirty reads and non-repeatable reads but allows phantom reads. 

```sql
-- Transaction 1
BEGIN TRANSACTION;
SELECT Balance FROM Customers WHERE CustomerID = 1; -- Reads original balance

-- Transaction 2
BEGIN TRANSACTION;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
UPDATE Customers SET Balance = Balance - 100 WHERE CustomerID = 1;
COMMIT;

-- Transaction 1
SELECT Balance FROM Customers WHERE CustomerID = 1; -- Still reads the original balance
```

4. Snapshot
 It ensures that a transaction sees a consistent snapshot of the database, even if other transactions are concurrently modifying the data.
 
5. Serializable: 
- This isolation level provides the highest level of isolation. 
- It ensures that transactions execute as if they were serialized, one after the other. 
- It prevents dirty reads, non-repeatable reads, and phantom reads. For example:
- Allows limited concurrency but guarantees data consistency
```sql
-- Transaction 1
BEGIN TRANSACTION;
SELECT Balance FROM Customers WHERE CustomerID = 1; -- Reads original balance

-- Transaction 2
BEGIN TRANSACTION;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
UPDATE Customers SET Balance = Balance - 100 WHERE CustomerID = 1;
COMMIT;

-- Transaction 1
SELECT Balance FROM Customers WHERE CustomerID = 1; -- Still reads the original balance
```

## Snapshot V/S Read repeatable 
"Snapshot" guarantees that all queries within the transaction will see the data as it was at the start of the transaction. 
"Repeatable read" guarantees only that if multiple queries within the transaction read the same rows, then they will see the same data each time

## why phantom read is possible in read repeatable?
Read Repeatable allows insertion of new data into the database, as a result, it does not prevent Phantom Read Concurrency Problem.
whereas in snapshot a new row insertion untill transaction ends is not possible.

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/cebba255-e7a0-4408-a162-dd167d8b99c1)

[Refer](https://dotnettutorials.net/lesson/difference-between-snapshot-isolation-and-read-committed-snapshot/)

## What is the difference between serializable and snapshot isolation levels in SQL Server?
The Serializable Isolation Level in SQL Server is implemented by acquiring a range lock on the data returned by the transaction. That means the resources are being locked for the duration of the current transaction. This isolation level does not have any concurrency problems but the problem is it decreases the number of concurrent transactions. 

On the other hand, the Snapshot Isolation Level in SQL Server doesn’t acquire any lock on the data returned by the transaction instead it maintains versioning in the tempdb system database. As the snapshot isolation level does not lock the resources, it can significantly increase the number of concurrent transactions while providing the same level of data consistency as the serializable isolation level provides.

For SQL Server :
![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/ad1b59d2-a5f0-481e-9bbd-43d30199e0fd)
