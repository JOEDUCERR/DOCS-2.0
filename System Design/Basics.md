* Designing systems to meet real world requirements and acknowledge technical boundaries for any project.
* App creation these days is much easier than before, but providing that to millions of users at the same time is the real hard part. You need System Design after that.
### System scaling:
1. **Vertical**: Scaling up: Where you buy more power for a single server. This method is costly.
2. **Horizontal**: Scaling out: Where you distribute the system over multiple smaller machines by rewriting the software's structure.
* There are many trade-offs that can occur in system design. **Performance vs Scalability, Latency vs Throughput, Consistency vs Availability.**
### URL Shortner System Design example
https://notebooklm.link.google/a2B7hz5DZ8x9

## More System Design Techniques
### Asynchronism & Message Queues
* In simple systems, when a user requests something they have to wait for the whole backend process to finish in order to get a response. If any of the other processes are slow, the response takes a long time to get to the user.
* **Message Queues**: (Kafka, RabbitMQ, SQS)These acknowledge requests almost immediately and pushes jobs to a queue where workers pull tasks from the queue and process them **Asynchronously**.
* There are also **shock absorber** queues which add buffer to huge push notifications which are to be sent to millions of users at once.
### Using Reverse proxies and Load Balancers
* To distribute traffic over multiple servers so that one does not get overburdened.
* Sits in front and acts as a ingress controller which provides **caching** and other reverse proxy features.
### Caching 
* Keeping frequently accessed data in high speed RAM to bypass slow disk speeds.
* Techniques like cache-aside, write-through, write-behind are ways to use both cache and permanent storage in such a way that it does not slow down the software speed.
## Database Scaling (Important)
* When a single DB server reaches its limits
	* Replication: Primary node handles all writes and replicated data to read-only replica DBs to let off the load
	* Sharding: Divides massive dataset across independent database nodes using a shard key.
* **Storage Engines:**
	* **B-Trees (e.g., PostgreSQL, MySQL):** Update pages directly in-place. They are heavily optimized for fast reads, point lookups, and range queries, but suffer from write amplification under heavy random writes.
	* **LSM-Trees (e.g., Cassandra, RocksDB):** Buffer writes in memory (MemTable) and sequentially append them to immutable disk files (SSTables). They convert random writes into sequential disk I/O, making them ultra-fast for write-heavy workloads.

----------------------------
# Deep Dives into topics
### Message Queues
* An asynchronous communication middelware that recieves, holds and delivers messages between software services. It is used rather than software services directly communicate with each other over synchronous HTTP connections.
* If an API endpoint has to update a database, render an email, generate a PDF receipt, and trigger a mobile push, the user is stuck waiting for hundreds or thousands of milliseconds.
* When external networks time out or return transient errors (e.g., `HTTP 429 Too Many Requests` or `503 Service Unavailable`), the message queue system enables automated **retries** with **exponential back off and randomized jitter**.

*So the basic idea is that instead of multiple services working one by one, they are put into a queue and picked up by workers to be performed asynchronously.*
### Dead Letter Queue (DLQ)
* Think of it as a separate waiting room for tasks that have repeatedly failed or cannot be processed.
* Engineer later checks DQL as to why a service fails multiple times.
### Idempotency
* This is one of the more confusing concepts, so let's use a real example. Imagine your SMS Worker sends the customer an order confirmation. The SMS provider successfully delivers it. However, the worker crashes before recording that the task has been completed. When the worker restarts, it sees the same task in the queue and sends the SMS again. The customer receives the same confirmation twice. That's a duplicate operation.
* To prevent this, we give each task a unique identifier (worker check redis again).
* This is the basic idea behind idempotency: **preventing repeated processing from causing the same effect multiple times.**
### Kafka, RabbitMQ and SQS
| Technology | Simple explanation | Common use |
|---|---|---|
| RabbitMQ | A broker that routes messages to the appropriate queues | Background jobs, task processing, routing |
| Apache Kafka | A distributed event log that retains events for consumers to read | Event streaming, analytics, large-scale data pipelines |
| Amazon SQS | A managed queue service where applications send and receive messages | Background jobs without managing your own broker |
