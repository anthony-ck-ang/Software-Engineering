# `What is Redis?`
- Open src in-memory data structure store
- NoSQL key value store
- Can be used as db (disk) /or cache (mem) and message broker
- Support multiple data structure eg list set..
- Single threaded
- Built in replication, leader-follower replication

<br>

## `Advantages:`
- Flexible
- No schemas & column names
- Very fast
- perform ~110k SETS/s, ~81k GETS/s
- Good datatype support
- Can be a caching && || persistent db to disk

<br>

### `Datatype supported:`
```html
Strings
Lists
Sets
Sorted Sets
Hashes <k,v> js obj/ JSON
Bitmap
Hyperlogs
Geospatial indexes
```

<br>

### `Security:`
```html
To be accessed by trusted clients (internally)
Do not allow external / internet access or exposure
Simple auth can be setup
Can be restricted to certain interfaces
Data encryption not supported
```

<br>


### `Optional Durability:`
```html
- Journaling: write data to mem + log event to disk (append only log)
- Snapshot
- Both happen asynch in the background
```

<br>

### `Transport Protocol:`
```html
- Uses TCP (bi-directional communication)
- Req/ Res model like HTTP 
- Message format is RESP (REdis Serialization Protocol)
```

<br>

### `PUB/SUB:`
```html
- Supports Publish/ Subscribe model
- client that sub to channel will be notified when data is "push" to channel
- in that case, will switch from TCP to Push model
```

<br>

### `Replication:`
```html

- leader-follower model; 1 to many
- Write to one and replicate to many
```

### `Clustering:`
```html
- Shard (segment) data across multiple nodes (machine)
```

<br>

## `Using Docker:`

- Spin up Redis docker instance (persist)
- Set kv
- Set kv with expiry

<br>

### `Install docker (MacOS, Linux or winOS Pro/Enterprise)`

`Shell 1`
```html
docker
docker run --name <container name> -p <host machine port>:<container port> <docker image>
docker run --name rdb -p 6379:6379 redis
```

`Shell 2`
```html
docker exec -it rdb redis-cli

docker start rdb
docker exec -it rdb redis-cli
```

<br>

## `Ubuntu:`

```html
Sudo apt-get update
Sudo apt -get upgrade

Sudo apt-get install redis-server
Sudo cp /etc/redis/redis.conf /etc/redis/redis.conf.default
Redis-cli
```

<br>

## `Enable Ubuntu in Windows:`

- Install UBUNTU from windows store
- Enable 'Windows Subsystem for Linux' in windows feature
- Create username and password

```html
sudo apt update && sudo apt upgrade

sudo apt install redis-server
redis-cli -v
sudo service redis-server restart
sudo service redis-server stop
sudo service redis-server start
redis-cli
```

or...

```html
Redis msi file -> installer  
```

<br>

## `Some common commands`

`Any connection?`
```html
Ping
ECHO ‘hello’
```
`Set and Get`
```html
set <key> <"value">
get <key>

{k, v}
SET key1 100
GET key1
```

`Exist?`
```html
EXIST key1
```
`Delete`
```html
DEL key1
FLUSHALL
```

```html
SET server:name some server
GET server:name

SET server:port 8000
GET server:port
```
`Set with expiry`
```html
SETEX greeting 30 “Hello”
TTL greeting
PERSIST greeting
```

`Set > 1 key value`
```html
MSET key1 “hi” key2 “world”
```

`Snapshots that will be saved to disk:`
Var/lib/redis/dump.rdb
```html
SAVE
```


`Config file can be found at:`
```html
cd /etc/redis
ls
sudo nano redis.conf
```

```html
subscribe <channel>
publish <channel> "hello"
```

## `Other Rsc:`
-  https://redis.io/documentation
-  https://redis.io/commands#
-  https://redis.io/topics/persistence
-  https://redislabs.com/ebook/part-2-core-concepts/chapter-4-keeping-data-safe-and-ensuring-performance/4-1-persistence-options/4-1-2-append-only-file-persistence/
-  https://networkengineering.stackexchange.com/questions/24068/why-do-we-need-a-3-way-handshake-why-not-just-2-way

    



