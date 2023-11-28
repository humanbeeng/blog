+++
title = 'Relational Databases'
date = 2023-11-28T11:57:59+05:30
draft = false
+++

#system #interview #database #relational-database

**Normal forms**
1,2,3NF
**Transactions**
ACID


**Atomicity** 
Each transaction is a single unit of execution
**Consistency**
Database goes from one valid state to another
**Durability**
Once committed, data is recoverable even after system failure
**Isolation**
Each transaction run independently. Database does not guarantees isolation, but rather provides tools to ensure isolation.

#### Database indexes
Types of index implementations 
1. Primary index
2. Clustered index
3. Partial index (for subset of rows)
4. Composite index
5. Secondary index 
Improves lookup performance 
Decreases write performance since index table also has to be written.

_Learn about serialisation and internals of B+ Trees_ 


#### Database locking** 

**Read lock/Shared lock**
When acquired, other transactions can read the locked rows, but cannot modify.
**Write lock**
Other transactions can’t even read/write locked data
Locks can be acquired on
- Table
- Row
- Column
- Page

With locks, there exists possibility of deadlock 
If database engine detects deadlock possibility, it kills the transaction which the deadlock was detected.
Always try to lock data related stuff at database level itself. Application level locks should be the last option

_Distributed locks_ exists, they are expensive

**Syntax for read lock**
```sql
SELECT ….. FOR SHARE;
```


**Syntax for write lock**
```sql
SELECT ….. FOR UPDATE;
```


#### _Isolation levels_
- Read committed 
- Read uncommitted
- Serialisable

**Dealing with locked rows**
**NOWAIT**
Transaction does not wait for locks to be released by other transaction. It just fails itself immediately. 

**SKIP LOCKED**
Transaction just acquires lock on the unlocked rows, performs transaction on unlocked rows and skips the ones which are locked.

#### Scaling relational database**

**Read replicas**
As name suggests
What is master goes down?
Replica takes over. 
There will be replica lag if replica is present in other region.

**Sharding** 
Possible only if data is shardable.
Sharding with replicas is also possible
Example: Geographical sharding, but other region data is available through replication

**Multi master setup**
For high availability and scaling writes 
Complex, data conflicts, sync issues. 
Conflicts example: ID generation 

**Why RDMS systems do not scale**
**Read replica setup**
**Synchronous**: Slow writes since writes needs to be propagated to all replicas
**Asynchronous**: Replicas are not in sync with master

**Multi master setup**
Consistency

**Normalisation**
Normalised days won’t help.
Up to certain limit joins provide good flexibility. Then, they don’t scale. Because joins are super expensive. 
Later on, to avoid joins, we’d have to add data redundancies and move back from 3NF: Denormalise.


**Links**
[Sharding vs Partitioning](https://www.youtube.com/watch?v=wXvljefXyEo)
[Database Engineering - Arpit](https://www.youtube.com/watch?v=-YxWRxOFgRY&list=PLsdq-3Z1EPT2C-Da7Jscr7NptGcIZgQ2l&pp=iAQB)

ProxySQL is used to load balance sharded databases. 

**Pagination**
Pagination is usually done by passing the range that we require. **Limit offset pagination**
It can also be done by passing the last ID of data that was returned to the user. **Last key pagination**

_Twitter, DynamoDB_ 
_Intelligent load balancer_

**Database choice**
Postgres is great for analysis and cache handling than MySQL
General purpose: MySQL 

**Incremental adaptation**
ORM -> Raw SQL -> stored procedures


##### Hash lookups in Relational database
  
In a database, indexes are typically looked up using a data structure called a B-tree (balanced tree), rather than binary search or hash functions.

A B-tree is a self-balancing tree structure that maintains sorted data and allows for efficient insertion, deletion, and search operations. B-trees are well-suited for databases because they provide logarithmic time complexity for search operations, making it efficient to find a specific value in a large dataset.

Here's a simplified overview of how a B-tree works for index lookups:

1. **Root Node:** The topmost node of the tree contains key values and pointers to child nodes.
2. **Intermediate Nodes:** Intermediate nodes in the tree also contain key values and pointers to child nodes, forming a hierarchy.
3. **Leaf Nodes:** The bottom-level nodes (leaves) contain key values and pointers to the actual data records or additional levels of the index.

When you perform a search, the database engine starts at the root node and traverses down the tree, comparing the search key with the keys in each node. Based on the comparison, it decides whether to move to the left or right child node. This process continues until the leaf node is reached, where the actual location of the data is found.

Hash functions are more commonly used in hash indexes, which are different from B-trees. Hash indexes use a hash function to map keys to specific locations (buckets) directly. **While hash indexes can provide fast lookups, they may not perform as well for range queries or ordered traversal compared to B-trees**.

In summary, databases often use B-trees for index lookups due to their efficiency in handling range queries and maintaining order, while hash indexes may be used in specific cases where direct key-to-location mapping is advantageous.

