# Data Warehouse

It is a central repository which stores historic data which can identify trends and make forecasts

# Need of Data Warehouse
RDBMS cannot efficiently store huge amount of data as fetching that data could be slow due to large number of joins which might be involved while fetching that data.


# Fact Table And Dimension Table
 The fact table stores the quantitative and measurable data (i.e., facts or measures) 
 Like : No of orders sold in a day.

![fact_TB](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/c00c402f-1f96-4ddc-9d37-1b2039c930ad)

 The dimension table provides additional attributes and context about the products
 Like : Which product has the most sales ?
 
![dimensionTB](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/a5606bfe-bd15-4659-98c4-a3edbf19a77f)


The ProductID column in the fact table is a foreign key referencing the ProductID column in the dimension table.

# Data Warehouse Schema

## Star Schema
- Fact table is linked to all the dimension tables
### Advantages : 
- Dimension tables are more denormalized to improves performance by minimizing joins and faster queries
### Disadvantages : 
- Data redundancy is high and occupies more disk space

## Snowflake Schema
- Not all dimension table are connected to fact table directly i.e This schema has fact table which connects to dimension tables and these dimension table are furthur connected to sub-dimension tables
## Advantages
- Data redundancy is less with less disk space 
- Easier to maintain as updates needs to be made on less tables due to effective normalization
## Disavantages
- Dimension tables are normalized due to which performance might be slow due to many joins


# OLAP v/s OLTP

## OLAP 
- Online Analytical Processing 
- Helps In analysis of business data
- Operates on historic data like which product sales gave most profit.
- Mostly performs select opertaions
- Uses DataWarehouse (Centeralized Repository that stores large amount of historic data)
- Tables are not fully normalized as denormalized database will have less join operations which will help in faster data retrieval process
- Different OLTP databases are source for OLAP
- Mostly Read operations only

## OLTP
- Online Transaction Processing 
- Helps in management of day to day transactions in business
- Operates with real time business operations like CRUD operations
- Insert, Update and Delete operations are performed.
- Uses Traditional DBMS
- Tables are normalized (as it can help with quick updates in table because if database is normalized effectively only few tables will require changes for a given update)
- Source is transactional data involved in business
- Read and write operations are permitted

## Discuss a scenario where write operations are performed in OLAP.
During Data Refresh period write/update opeartions could occur.
This process involves loading, transforming, and integrating data from OLTP (Online Transaction Processing) systems or other data sources into the OLAP database. 

For example : Currently a business OLAP constitutes historic data upto year 2021 then it might require an update with data upto year 2023 for further analysis.
