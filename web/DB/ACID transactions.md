https://databricks.com/glossary/acid-transactions

## A.C.I.D. properties: Atomicity, Consistency, Isolation, and Durability

ACID is an acronym that refers to the set of 4 key properties that define a transaction: **Atomicity, Consistency, Reliability,** and **Durability.** If a database operation has these ACID properties, it can be called an ACID transaction, and data storage systems that apply these operations are called transactional systems. ACID transactions guarantee that each read, write, or modification of a table has the following properties:

-   **Atomicity** - each statement in a transaction (to read, write, update or delete data) is treated as a single unit. Either the entire statement is executed, or none of it is executed. This property prevents data loss and corruption from occurring if, for example, if your streaming data source fails mid-stream.
-   **Consistency** - ensures that transactions only make changes to tables in predefined, predictable ways. Transactional consistency ensures that corruption or errors in your data do not create unintended consequences for the integrity of your table.
-   **Isolation** - when multiple users are reading and writing from the same table all at once, isolation of their transactions ensures that the concurrent transactions don’t interfere with or affect one another. Each request can occur as though they were occurring one by one, even though they're actually occurring simultaneously.
-   **Durability** - ensures that changes to your data made by successfully executed transactions will be saved, even in the event of system failure.