## `ACID`
- Atomicity
- Consistency
- Isolation
- Durability

```html
RDBS
eg: posgres, mysql, SQLServer, Oracle
```

## `What is a Transaction?`
- A bunch/ collection of (1 or more) queries
- One unit of work
- eg. 'Account deposit' -> (SELECT + UPDATE + UPDATE)
- 3 queries == 1 transaction of account deposit

eg:
```html
ACCOUNT
ACCOUNT_ID | BALANCE $
---------------------
1		   | 1000 -> 900
2		   | 500 -> 600

Send $100 from A1 to A2

BEGIN TX1
	SELECT BALANCE FROM ACCOUNT WHERE ID = 1
	BALANCE > 100 ? T:F
		UPDATE ACCOUNT SET BALANCE = BALANCE - 100 WHERE ID = 1
		UPDATE ACCOUNT SET BALANCE = BALANCE + 100 WHERE ID = 2

COMMIT TX1 ( -> persist data)
END
ROLLBACK (something went wrong ->abort)

```

<br>

## `1. Atomicity`
```html
ONE -> 
All queries must succeed or fail (rollback) as one (the whole thing)
else the state of the data will not be accurate 
since partial queries (transaction) still succeeds while the rest fails 
eg. debit a1 but never credit a2
```

<br>

## `2. Isolation`
```html
Alone ->
Can my inflight transaction see changes made by other transactions?
(concurrency issue)
```

`Read phenomena`

### `Dirty read (UNCOMMITTED)`
- transaction is allowed to read data from a row 
that has been modified by another running transaction and UNCOMMITTED

```html
eg:
SALES
PID | QNT  | PRICE $
---------------------
P1	| 10   | 5
P2	| 20   | 4

BEGIN TX1								
	SELECT PID, QNT*PRICE FROM SALES
			P1: 50
			p2: 80
	-----------------
	(concurrently...)
BEGIN TX2
	UPDATE SALES SET QNT = QNT+5 WHERE PID = 1
			QNT: 10 -> 15

	-----------------
	SELECT SUM(QNT*PRICE) FROM SALES
			$155 instead of $130
			(TX1 read the uncommited data by TX2)
			
```

### `Non-repeatable read (COMMITTED)`
- data is read twice inside the same transaction 
cannot be guaranteed to contain the same value. 
- Depending on the isolation level, another transaction could have nipped in 
and updated the value between the two reads

```html
eg:
SALES
PID | QNT  | PRICE $
---------------------
P1	| 10   | 5
P2	| 20   | 4

BEGIN TX1								
	SELECT PID, QNT*PRICE FROM SALES
			P1: 50
			p2: 80
	-----------------
	(concurrently...)
BEGIN TX2
	UPDATE SALES SET QNT = QNT+5 WHERE PID = 1
			QNT: 10 -> 15
COMMIT TX2
	-----------------
	SELECT SUM(QNT*PRICE) FROM SALES
			$155 instead of $130
			(TX1 read the committed data by TX2)
```

### `Phantom read`
- one user is repeating a read operation on the same records, 
but has NEW records in the results set: READ UNCOMMITTED. 
- Also called a Dirty read. 
- When this isolation level is used, a transaction can read uncommitted data 
that later may be rolled back.

```html
eg:
SALES
PID | QNT  | PRICE $
---------------------
P1	| 10   | 5
P2	| 20   | 4

->
P3	| 10   | 1

BEGIN TX1								
	SELECT PID, QNT*PRICE FROM SALES
			P1: 50
			p2: 80
	-----------------
	(concurrently...)
BEGIN TX2
	INSERT INTO SALES
	VALUES ("P3",10,1)
COMMIT TX2
	-----------------
	SELECT SUM(QNT*PRICE) FROM SALES
			$140 instead of $130
			(TX1 read the committed new data by TX2)
```

### `Lost update read`
- While in transaction to commit some updates, 
another transaction came in and override -> your transaction lost the update
- A lost update occurs when two different transactions are trying 
to update the same column on the same row within a database at the same time


<br>

### `Isolation levels`

```html
low -> high
fast -> slowest
```
```html
Read uncommited
- no isolation, any changes from outside is visible to the transaction

Read committed
- each query in a transaction only see committed data at that moment in time

Repeatable Read
- each query in a transaction only see committed data at the beginning of transaction
- usage of versioning (read only from that particular/same version -> consistent data)
- Shared lock

Serializable
- transactions are serialized/ in series (cannot be executed in parallel)
- Exclusive lock

Locks
- row || table level
- shared || exclusive

Shared locks (MANY READ ONLY) -> two transactions are granted read access. 
One transaction gets the shared lock on data and 
when the second transaction requests the same data it is also given a shared lock. 
Both transactions are in a read-only mode, 
updating the data is not allowed until the shared lock is released.
A shared or read lock prohibits any other process from requesting a write lock 
on the specified part of the data.

Exclusive locks (EXCLUSIVE WRITE + BLOCK)
An exclusive or write lock gives a process exclusive access for writing to the specified part of the data. 
While a write lock is in place, no other process can lock that part of the data.
```
<br>

## `3. Consistency`
```html
I. In data
	defined by user (table schema)
	referential integrity (primary/foreign key)
	atomicity
	isolation
```

`Performance vs data consistency/ accuracy`
- Depends on nature of data
- eg. User likes vs user a/c balance

```html
II. In reads
If transaction committed a change, 
will another new transaction immediately see the change? No.
```

https://docs.pivotal.io/p-mysql/2-7/about-leader-follower.html

```html
In a cluster of db servers 
Write is done to primary node/shard then replicated (transferred) to the replica.

During this process, the data is inconsistent due to the network latency (delay).
If you read from the replica just before the transfer, you get an old value.
```

- SQL and noSQL both suffers inconsistency 

- NoSQl gave up consistency in favor for scalability
(horizonal scale -> add more machine + Leader-Follower Nodes) 

- Eventual consistency

<br>

## `4. Durability`
- Committed transactions must be persisted in a durable non-volatile storage
- The data must remain after db restart from a power outage






