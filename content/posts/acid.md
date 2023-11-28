+++
title = 'ACID'
date = 2023-11-28T11:46:32+05:30
draft = false
+++

#database #system

#### Atomicity
A transaction is a single unit of execution. Statements within a transaction are either all executed or none. 

#### Isolation
Transactions are executed independently of one another.

##### Isolation levels

- **Read uncommitted** 
- **Read committed**
- **Repeatable read**: transaction will make sure that when a query reads a value, it remains unchanged throughout the transaction. This is to prevent non repeatable reads 
- **Snapshot**: Repeatable read **locks the rows** it reads until the end of the transaction, while snapshot isolation **allows other transactions to read and write**, but each transaction gets a consistent snapshot. In Postgres, repeatable read is implemented using snapshot, so they both are the same in Postgres. Hence there is no phantom reads in RR level in Postgres. But in MySQL, in repeatable read, phantom reads problem will exist.
- **Serialisable**: Each transaction run as if they are run one after the other. Slowest of all. No concurrency. But usually database engines does serialisable in Optimistic way or else it’d have been so slow. We can also do it pessimistically

Non repeatable reads: Happens when other transaction **updates an already read rows.**

Phantom reads: Happens when new row appears after reading all rows before. Due to insert

**Database implementation of isolation**

**Pessimistic**
Through locks. To avoid lost updates. Expensive

**Optimistic**
No locks. Just let the transaction continue. But, it’s going to fail the transaction once the transactions start to step over each other. It’s going to fail the transaction with serialisation error along the user to retry. 
In an optimistic isolation model, transactions are allowed to proceed without acquiring locks on the data they intend to modify. Instead, potential conflicts are checked only at the time of commit. If a conflict is detected during this check, the transaction fails.

There are two main types of conflicts that can cause a transaction to fail in an optimistic isolation model:
1. Read-Write Conflict:
   - If a transaction reads a piece of data, and before it commits, another transaction writes to the same data, a read-write conflict occurs.
   - The first transaction, having based its changes on an outdated read, is now in conflict with the changes made by the second transaction.
2. Write-Write Conflict:
   - If two transactions independently modify the same piece of data and attempt to commit their changes simultaneously, a write-write conflict occurs.
   - Both transactions are trying to write conflicting changes to the same data, and only one can succeed.
  
In either case, when the conflict is detected during the commit phase, the optimistic isolation model requires one of the transactions to fail. Typically, the failing transaction is rolled back, allowing the other transaction to commit its changes successfully.
This optimistic approach allows for greater concurrency, as transactions are not blocked during their execution. However, it requires careful handling of conflicts at the time of commit to ensure data consistency.
In the case of a write-write conflict in an optimistic concurrency control model, where two transactions independently modify the same piece of data and attempt to commit their changes simultaneously, one of the transactions is chosen to be rolled back.

The decision on which transaction to roll back is typically based on a predefined conflict resolution policy. Common conflict resolution strategies include:
- **First-Commit Wins (FCW):** The first transaction that attempts to commit successfully wins, and the subsequent transaction is rolled back. This approach is based on the order of commit attempts.
- **Last-Commit Wins (LCW):** The last transaction to commit successfully wins, and any preceding transactions are rolled back. This is based on the principle that the latest change should prevail.
- **Priority-Based:** Transactions may have priority levels assigned to them, and the transaction with higher priority may be allowed to commit while the lower-priority transaction is rolled back.
- **Random Selection:** A random selection mechanism might be employed to choose which transaction to roll back.

The specific choice of a conflict resolution policy depends on the application's requirements and the desired behaviour in case of conflicts. Each policy has its own trade-offs, and choosing the most appropriate one often involves considering factors such as fairness, performance, and the nature of the application's data and business logic.


#### Consistency

There is

1. Consistency in data: Data is just persisted wrong.
2. Consistency in reads: Data is just read wrong. 

In Data

- Referential integrity 
- Data during transactions 
- During failure and recovery 

In Reads

- If a transaction writes something, is it available instantly for read?
- Affects system as a whole.

#### Durability
- Committed transactions should be persisted.
Techniques 
- WAL (Write ahead log): Gets flushed to disk immediately 
- Async Snapshot: We keep everything in memory. Then write all to the disk 
- Usually write requests goes to OS cache. OS cache then batches the writes. Susceptible to OS crash and machine restart. To bypass this, there is fsync command to force. This of course will be expensive and slow. Crashes rarely happen, but its gonna happen.
