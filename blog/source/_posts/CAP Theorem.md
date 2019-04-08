title: CAP Theorem
date: 2019-03-06 14:16:00

categories: System Design

---

The CAP Theorem says that it's impossible to build an implementation of read-write storage in an asynchronous network that satisfies all of the following three properties:

- Availability - will a request made to the data store always eventually complete?
- Consistency - will all executions of read and writes seen by all nodes by atomic or linearizably consistent?
- Partition tolerance -the network is allowed to drop any messages.

