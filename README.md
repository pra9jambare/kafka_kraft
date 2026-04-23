# Apache Kafka – README

## 📌 What is Kafka?

Apache Kafka is a **distributed event streaming platform** used to build real-time data pipelines and streaming applications. It allows systems to publish, store, and process streams of records (events) in a scalable and fault-tolerant way.

---

## ❓ Why is Kafka Required?

Kafka is used when:

* You need **real-time data processing**
* Systems must communicate **asynchronously**
* High-throughput and **low-latency messaging** is required
* You want to decouple microservices
* You need **reliable and durable message storage**

---

## 🏗️ Basic Kafka Architecture

Kafka follows a distributed architecture:

* **Producers** send data
* **Brokers** store data
* **Consumers** read data
* Data is organized into **topics and partitions**
* Multiple brokers form a **cluster**

---

## ⚡ What is Event-Driven Architecture?

Event-driven architecture (EDA) is a design pattern where:

* Systems communicate via **events**
* Producers generate events
* Consumers react to events

Example:
Order placed → Payment processed → Notification sent

---

## ✅ Benefits of Kafka

* High throughput (millions of messages/sec)
* Scalability (horizontal scaling)
* Fault tolerance (replication)
* Durability (data persists on disk)
* Real-time processing
* Loose coupling between services

---

## 🧩 Components of Kafka

### 1. Broker

A Kafka broker is a server that stores data and serves client requests.
Each broker manages topics and partitions
A Kafka cluster is made up of multiple brokers
Brokers handle read/write operations from producers and consumers

### 2. Topic

A topic is a category or stream of data.
Think of it like a table or folder
Producers write messages to topics
Consumers read messages from topics
Example: user-signups, orders, logs

### 3. Partition

Each topic is split into partitions.
Partitions allow parallel processing
Each partition is an ordered, immutable sequence of records
Messages are assigned an offset within a partition

### 4. Cluster

A Kafka cluster is a group of brokers working together.
Provides fault tolerance (if one broker fails, others take over)
Data is replicated across brokers
Managed as a single system

### 5. Producer

A producer sends data to Kafka.
Publishes messages to topics
Can choose which partition to send data to
Supports batching and compression for efficiency

### 6. Consumer

A consumer reads data from Kafka topics.
Subscribes to one or more topics
Processes data in real-time or batch
Keeps track of what it has read using offsets

### 7. Kafka Offset
An offset is a unique ID for each message within a partition.
Acts like a position marker
Helps consumers track progress
Stored either automatically or manually by consumers

### 8. Kafka Keys

A key determines how messages are distributed across partitions.
Messages with the same key go to the same partition
Helps maintain ordering for related data
Example: all events for a specific user go to one partition

### 9. Kafka Serialization

Kafka stores data as bytes, so you need serialization.

Converts objects → byte arrays
Common formats:
JSON
Avro
Protobuf
Deserialization happens on the consumer side
### 10. Consumer Group

A consumer group is a set of consumers working together.
Each partition is consumed by only one consumer in a group
Enables parallel processing
Different groups can read the same data independently

👉 Example:

Group A processes logs for analytics
Group B processes logs for monitoring

---

## 🔐 Strategies for High Availability

* Replication of partitions across brokers
* Multiple brokers in a cluster
* Use of **leader and follower replicas**
* Proper ISR (In-Sync Replica) configuration
* Load balancing across partitions

---

## 🐘 What is Zookeeper?
Apache ZooKeeper is an open-source system used to manage and coordinate distributed applications basically, it helps multiple machines (servers) work together reliably.

## ⚙️ What ZooKeeper Does
1. Configuration Management
Stores shared configuration data so all servers use the same settings.

2. Naming Service
Helps identify and locate services in a distributed system.

3. Synchronization
Ensures processes don’t conflict (like two servers trying to update the same data at once).

4. Leader Election
Selects one server as the “leader” to coordinate tasks.

5. Distributed Locking
Prevents multiple systems from accessing the same resource at the same time.

---

## ⚙️ What is KRaft?

KRaft (Kafka Raft Metadata mode):

* Removes dependency on Zookeeper
* Uses Raft consensus protocol
* Simplifies Kafka architecture
* Recommended for newer Kafka versions

---

# 🧪 Practical Session

## ☕ Java Installation

```bash
wget -O - https://apt.corretto.aws/corretto.key | sudo gpg --dearmor -o /usr/share/keyrings/corretto-keyring.gpg && \
echo "deb [signed-by=/usr/share/keyrings/corretto-keyring.gpg] https://apt.corretto.aws stable main" | sudo tee /etc/apt/sources.list.d/corretto.list

sudo apt-get update; sudo apt-get install -y java-11-amazon-corretto-jdk

java -version
```

---

## 📥 Kafka Download & Installation

```bash
wget https://www.apache.org/dyn/closer.lua/kafka/3.9.2/kafka_2.13-3.9.2.tgz?action=download

tar -xvf kafka_2.13-3.9.2.tgz
cd kafka_2.13-3.9.2
```

---

## ▶️ Start Kafka with Zookeeper

### Start Zookeeper

```bash
./bin/zookeeper-server-start.sh ./config/zookeeper.properties
```

### Start Kafka Server

```bash
./bin/kafka-server-start.sh ./config/server.properties
```

---

## ▶️ Start Kafka with KRaft Mode

### Step 1: Generate Cluster ID

```bash
bin/kafka-storage.sh random-uuid
```

### Step 2: Format Storage

```bash
./bin/kafka-storage.sh format -t <CLUSTER_ID> -c ./config/kraft/server.properties
```

### Step 3: Start Kafka

```bash
./bin/kafka-server-start.sh ./config/kraft/server.properties
```

---

## 📝 Create a Topic

```bash
./bin/kafka-topics.sh --create \
--topic test-topic \
--bootstrap-server localhost:9092 \
--partitions 1 \
--replication-factor 1
```

---

## 📤 Create Producer

```bash
./bin/kafka-console-producer.sh \
--bootstrap-server localhost:9092 \
--topic test-topic
```

---

## 📥 Create Consumer

```bash
./bin/kafka-console-consumer.sh \
--bootstrap-server localhost:9092 \
--topic test-topic \
--from-beginning
```

---

## 🧪 Hands-on Scenario

### Create Topic with 2 Partitions

```bash
./bin/kafka-topics.sh --create \
--topic orders \
--bootstrap-server localhost:9092 \
--partitions 2 \
--replication-factor 1
```

---

### Producer with Key Support

```bash
./bin/kafka-console-producer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--property parse.key=true \
--property key.separator=:
```

### Input Example

```
KeyA:Consumer 1 is here
KeyB:Consumer 2 is here
KeyC:Consumer 1 is here
```

---

### Create Consumer Group (2 Consumers)

```bash
./bin/kafka-console-consumer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--group test-group \
--from-beginning
```

Run the same command in another terminal.

📌 Note:
Messages with the same key go to the **same partition**, hence handled by the same consumer.

---

## 🔍 Useful Commands

### List Topics

```bash
./bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```

---

### Describe Topic

```bash
./bin/kafka-topics.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--describe
```

---

### List Consumer Groups

```bash
./bin/kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--list
```

---

### Describe Consumer Group

```bash
./bin/kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--describe \
--group test-group
```

---

## 🎯 Summary

Kafka is a powerful distributed streaming platform that enables:

* Real-time communication
* High scalability
* Fault tolerance
* Event-driven system design

It is widely used in modern architectures like **microservices, data pipelines, and streaming analytics**.

---
