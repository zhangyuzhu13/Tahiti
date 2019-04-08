title: System Design Note
date: 2018-10-20 14:16:00
categories: System Design

---



# Web failure rate analysis（divide and conquer ）

| failure rate | DNS  | Web  | Player | MP3  |
| ------------ | ---- | :--- | ------ | ---- |
| 17           | 1    | 7    | 3      | 6    |
| 15           | 1    | 4    | 3      | 6    |

## web

 ### 1. Too many users and requests to handle

Solution: 

* Reverse proxy with more servers(dispatcher, cache, package)
* Reduce the size of webpage (simplify content 10%, compress/merge images 40%, lazy load 70%)
* More cacheable pages (change dynamic webpage into static webpage) 

**there will still some rate of failure on web that we could not reduce. Reason: Crazy user are not patient to wait and click too often**

**CDN(Content Delivery Network)**

## rate limit

### 1. Algorithm of gap

make sure the gap between two request >= 0.2, then we could tell that ups will less than 5.

### 2. Algorithm of time-bucket

make sure every second have n request.

* Use database
* not use database(set a counter) 

### 3. Algorithm of requestList

Find the fifth old packet and caculate the time diff, it should be large than 1s.

put each request in Queue,

Optimsize: circle queue

**Follow up:**

How to save space with 10^9 query per hour? 

Batch queries

how to support multiple threads?

lock

Producer/conductor

how tp support limiter on users?

<uid, requestList>

how to support query with different quotas?

**Token Bucket: space complexity O(1)**??????????

# 秒杀

* 时间 
* items
* qps

Target: keep working in good suitation

传统四层代理：

browser, ISP proxy, reverse proxy, load balance 

解法二： 

二级队列： 一级队列放行一些人，顶冲击流量，二级队列排序

how to reduce traffic?

* reduce page size: no image
* cache more: static page
* Proxy: batch, connection
* Limit request: JS, balancer

how to keep it simple, sweetie?

* no DB: memory + log
* no lock

how to isolate itself?

* new server
* Asynchronous

how to defend?

* IP
* captcha

Lottery algorithm

# Distributed System

* Design distributed file system and database(bigtable)
* Calculate word appearance/ inverted index/ anagrams with map reduce 

### design search engine 

Google three key: map reduce / Bigtable/ GFS

**not recite details of papers, but find a suitable solution under a scenario** 

how to save file?

* Disk: (metadata(fileinfo, index), bolcks:())

how to save a large file?

* blocks 变大

how to save an extra-large file?

* master save metadata(only server, not offset), many chunk servers(take care offset itself)

单点失败解决方法：热备份， master任务减少（GFS only one master）

How to find file is broken?

* Checksum put in memory.

how to avoid the loss of data when a chunkserver is down?

* Replicate the chunks, 2 more copy.

how to select chunkservers of a chunk? (启发式)

* Server with below-average disk space utilization

* limited number of "recent" creation

* Across racks:2+1

how to recover when a chunk is broken?

* ask master for help(just to ask for the chunkerver number)

how to find whether a chunkserver is down?

* master keep a chunkservers live log(chunkserver send live message to master, heartbeat, master update record) 

how to recover when a chunkserver is down?

* master keep a repair procedure list. (Repair priority is based on the number of replications)

how to avoid hot spot?

* master have a rebalance procedure, record chunk access frequency, replicate a chunk into more replications when it busy
* fill the chunkserver with more space and more bandwidth.

how to read from file?

* ask master for the file chunkserver
* then go to the chunkserver to read the file

how to write into a file?

* ask master where is chunk, master tell the primary chunkserver and replicas chunkserver
* Cache file data from user into chunkserver then chunkservers send data to each other.
* Primary chunkserver tell each replicas to write data into disk.



### BigTable

how to save a large table?

* metadata of table-> table

how to save a extra-large table?

* Metadata of table -> table -> ssTable

how to write into a table? (Task: add(b, yeah))

* write into memory first (sstable could not be modified)

| Memory | GFS/Disk |
| ------ | -------- |
| table0 | sstable0 |

what if men table is too large?

* When men is full, dump it into disk.

how to avoid system failure?

* wirte log

how to read data?

* Data is ordered inside a sstable
* data is disordered outside sstables

how to read fast?

* build index, put index into memory
* bloomfilter(), can tell data not in this block for sure, tell data in this block possibly, extra check needed.

**how to build bloom filter?**

how to save a table into GFS?

***key, value is the core of database***

Key = (row, column, time)

## Design Twitter/Instagram/Facebook

*product hunt* web

### design twitter

Q: User case

A: post twtte/ read feed line/ repost / follow/ comment/ like/

Q: Necessaries

A: 

*  read timeline: QPS 300K/ 

* write twtte: QPS = 5K
* Notify: 1 M followers within 1s
* Concurrent users = 150m
* Daily tweets 400m

##### Application 

Q: how to write a tweet

A: push model

Social graph ——|

Write API -> find out -> timeline list

Q: how to read timeline

A: timeline service read O(1)

Q: how to deal with the storm of lady gaga?

 A: A big star write a tweet and you will need to send very much data to many follower. **We could only notify online users** or we could use **pull mode** 

Q: pull mode

A: write in O(1) -> social graph -> read timeline

Q: how to deal with Architect of the MATRIX 

A: push & pull mode

Q: how to choose pull and push

A: 

* Understand specific scenario
* Balance between your latency, computation & storage
* O(n) is not workable
* Real time with terrible QPS needs pull
* Simplicity asks to focus on one

Q: speed up

A: 

* put data into memory

Q: save space

A: 

* only cache for the users who is active within 1 week
* only cache the latest 800 tweets
* only save twtte id in memory

Q: how to deal with cache miss

A: timeline builder(load from disk and append to memory)

Q: how to save the tweetlisttable on disk

A: split data into chunk and save by time decreasing. 

Q: how to support search?

### chat

Q: data structure

A: user table/ friend table/ Channel table / Message table

Q: how to choose the storage

A: SQL: RelationShip/ Edit/ correct/ Transaction/ Combination state

NoSQL: K/V pair, Append, tolerant/ performance/ Sequential record

#### Chat architecture:

Batch message/ compress message 

Delay change/ buffer

## Typeahead

Necessaries：

* average latency < 100ms
* 95% < 50ms

Algorithm： binary tree/ trie tree/ 

Select * from table where name like '".$query." %' order by name ASC

#### Architecture: 

Browser <- Aggregator <- many {personalized ranking/ Global ranking}

##### speed up

* memory
* multi-level cache
* save content id not detail
* 



























