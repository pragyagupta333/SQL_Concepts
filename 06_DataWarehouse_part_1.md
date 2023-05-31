# Data Warehouse

It is a central repository which stores historic data which can identify trends and make forecasts

# Need of Data Warehouse
RDBMS cannot efficiently store huge amount of data as fetching that data could be slow due to large number of joins which might be involved while fetching that data.

# Data Mart
A data mart is a subset of a data warehouse that focuses on a specific business area. Such as marketing, sales, finance.

# Need of Data Mart
- Using data mart organizations can improve data accessibility, enhance query performance,etc
- Helps to apply specific security measures to the subset of data within a particular business. This ensures that sensitive data is appropriately protected.


# Fact Table And Dimension Table
 The fact table stores the quantitative and measurable data (i.e., facts or measures) 
 Like : No of orders sold in a day.

![fact_TB](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/c00c402f-1f96-4ddc-9d37-1b2039c930ad)

 The dimension table provides additional attributes and context about the products
 Like : Which product has the most sales ?
 
![dimensionTB](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/a5606bfe-bd15-4659-98c4-a3edbf19a77f)


The ProductID column in the fact table is a foreign key referencing the ProductID column in the dimension table.

## What is factless fact table ?
Factless fact tables primarily focus on capturing the relationships between dimensions without storing any measurable quantities. 

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/a98c3154-98af-42ab-9f4e-5bf44f1d24c7)

# Data Warehouse Schema

## Star Schema
- Fact table is linked to all the dimension tables

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/8b62b16c-3c44-401d-982e-630b2bcc6be1)

### Advantages : 
- Dimension tables are more denormalized to improves performance by minimizing joins and faster queries

### Disadvantages : 
- Data redundancy is high and occupies more disk space

## When to use Star Schema ?
It is well-suited for reporting and analysis scenarios where centralized fact table contains the measures or numerical values, and dimension tables surrounding it, users can easily navigate the data and perform aggregations, slicing and dicing, and drill-down analysis with less joins and faster data retreival

## Snowflake Schema
- Not all dimension table are connected to fact table directly i.e This schema has fact table which connects to dimension tables and these dimension table are furthur connected to sub-dimension tables

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/7f9513aa-35cd-4782-ade9-6ed4215efaee)

## Advantages
- Data redundancy is less with less disk space 
- Easier to maintain as updates needs to be made on less tables due to effective normalization

## Disavantages
- Dimension tables are normalized due to which performance might be slow due to many joins

## When to use Snowflake Schema ?
When there is hierarchical relationships among dimension attributes like in E-commerce where separate dimension tables for each level of the hierarchy (e.g., category, subcategory, product) needs to be created

## Galaxy Schema / Fact Constellation
Galaxy Schema uses multiple fact tables connected with shared normalized dimension tables.

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/1a69b412-d3ee-49d1-b129-975fef12c250)

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

## Real Life example for OLTP and OLAP
#### OLTP :
ATMs allow users to transfer funds between accounts. When a fund transfer request is made, the OLTP system validates the transaction, deducts the transferred amount from the sender's account, and updates the balances of both the sender and recipient accounts in real-time.

#### OLAP
Retailers can use OLAP to analyze sales data to identify top-selling products, analyze sales trends over time, compare performance across different store locations,etc

## Discuss a scenario where write operations are performed in OLAP.
During Data Refresh period write/update opeartions could occur.
This process involves loading, transforming, and integrating data from OLTP (Online Transaction Processing) systems or other data sources into the OLAP database. 

For example : Currently a business OLAP constitutes historic data upto year 2021 then it might require an update with data upto year 2023 for further analysis.
