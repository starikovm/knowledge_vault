https://www.mongodb.com/nosql-explained/nosql-vs-sql
https://docs.microsoft.com/en-us/dotnet/architecture/cloud-native/relational-vs-nosql-data

![[Screenshot 2021-08-29 at 16-29-54 NoSQL vs SQL Databases .png]]

![[Screenshot 2021-08-29 at 16.38.33.png]]

## What are the Benefits of NoSQL Databases?

NoSQL databases offer many benefits over relational databases. NoSQL databases have flexible data models, scale horizontally, have incredibly fast queries, and are easy for developers to work with.

-   **Flexible data models**
    
    NoSQL databases typically have very flexible schemas. A flexible schema allows you to easily make changes to your database as requirements change. You can iterate quickly and continuously integrate new application features to provide value to your users faster.
    
-   **Horizontal scaling**
    
    Most SQL databases require you to scale-up vertically (migrate to a larger, more expensive server) when you exceed the capacity requirements of your current server. Conversely, most NoSQL databases allow you to scale-out horizontally, meaning you can add cheaper, commodity servers whenever you need to.
    
-   **Fast queries**
    
    Queries in NoSQL databases can be faster than SQL databases. Why? Data in SQL databases is typically normalized, so queries for a single object or entity require you to join data from multiple tables. As your tables grow in size, the joins can become expensive. However, data in NoSQL databases is typically stored in a way that is optimized for queries. The rule of thumb when you use MongoDB is **Data is that is accessed together should be stored together**. Queries typically do not require joins, so the queries are very fast.
    

-   **Easy for developers**
    
    Some NoSQL databases like MongoDB map their data structures to those of popular programming languages. This mapping allows developers to store their data in the same way that they use it in their application code. While it may seem like a trivial advantage, this mapping can allow developers to write less code, leading to faster development time and fewer bugs.
	
## What are the Drawbacks of NoSQL Databases?

One of the most frequently cited drawbacks of NoSQL databases is that they don’t support [[ACID transactions]] (atomicity, consistency, isolation, durability) transactions across multiple documents. With appropriate schema design, single record atomicity is acceptable for lots of applications. However, there are still many applications that require ACID across multiple records.

To address these use cases MongoDB added support for [multi-document ACID transactions](https://www.mongodb.com/transactions) in the 4.0 release, and extended them in 4.2 to span sharded clusters.

Since data models in NoSQL databases are typically optimized for queries and not for reducing data duplication, NoSQL databases can be larger than SQL databases. Storage is currently so cheap that most consider this a minor drawback, and some NoSQL databases also support compression to reduce the storage footprint.

Depending on the NoSQL database type you select, you may not be able to achieve all of your use cases in a single database. For example, graph databases are excellent for analyzing relationships in your data but may not provide what you need for everyday retrieval of the data such as range queries. When selecting a NoSQL database, consider what your use cases will be and if a general purpose database like MongoDB would be a better option.