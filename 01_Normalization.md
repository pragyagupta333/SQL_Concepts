# Normalization

It is a database design technique to reduce redundant data.

![redundant](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/15f30d6c-2cda-4252-92f8-6c6009f66c35)


## How Normalization is implemented in simple terms ?
By spitting tables into sub-tables such that one table has referenced data and other has transactional data.

### 1NF 
- Columns have atomic value 
- No repeating groups

![1nf](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/350c894f-a0f5-4d02-9a9b-c77ec479bea6)



### 2NF
- All non-key columns should fully dependent on primary key

<img src="https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/5e4e87ec-184a-4991-a175-642363d4a306" height= "300" width = "500"></img>

### 3NF
- Any non- key column should not depend on other non-key column i.e transient dependency should'nt exist

![3nf](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/b14da4c5-ffac-4186-b499-ea42af4e2736)


## Denormalization
- Database design technique to improve search performance 

- It is achieved by accepting reducdant data i.e by combining normalized tables of related data 

- It minimizes the number of joins needed for a query by adding redundancy to a database.

- Fetching queries can be simpler in denormalization because we need to look at fewer tables.

![denormalize](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/85d26855-e814-452b-a500-afa67d66ddc2)


## When to denormalize tables ?
- When a large number of insert/update/delete operations are performed.

- When Join operations are costly

- When queries run regularly on those tables.


## How Normalization helps in making Efficient Updates ?
It helps with quick updates in table because if database is normalized effectively only few tables will require changes for a given update

![NeedOfNormalization_01](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/5aa464c3-31b3-40cc-833f-329914ea1e7e)

If there is an update in customer name in above case, both tables will need to be update

![image](https://github.com/pragyagupta333/SQL_Concepts/assets/125549428/7a280837-71ab-47bf-a26f-aea9a552f0c3)

For change related to customer names, only customer table needs to be updated

# Useful References

[Normalization](https://dotnettutorials.net/lesson/database-normalization-in-mysql/)

[Normalization](https://www.youtube.com/watch?v=SEdAF8mSKS4&t=482s)

[Denormalization](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3DvRqzFBTgGJA&psig=AOvVaw2QgTHwUn6VG183yEQpPCjD&ust=1685495910508000&source=images&cd=vfe&ved=0CBMQjhxqFwoTCMCGztvvm_8CFQAAAAAdAAAAABBF)
