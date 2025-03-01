# Real-time Data Processing Pipeline with Kafka on AWS EC2  

This project sets up a real-time data pipeline using **Apache Kafka** on an **EC2 instance**, streaming data to **AWS S3** for further processing with **AWS Glue, Athena, and QuickSight**. Kafka is configured to handle **producer-consumer communication** with a secure public IP setup, enabling external data streaming.  

The pipeline is extended with **Jupyter Notebooks**, integrating **Python-based Kafka producers and consumers** for real-time data ingestion into S3. **AWS Glue crawlers** transform and catalog data, allowing seamless querying via **Athena** and visualization through **QuickSight**.  

This project demonstrates a fully functional, **cloud-based event-driven architecture** with real-time **data streaming, storage, and analytics** using AWS services.  


# **Apache Kafka - Distributed Event Streaming on AWS**

## **Overview**
Apache Kafka is an open-source **distributed event streaming platform** designed for high-throughput, real-time data processing. It allows applications to publish, process, store, and subscribe to streams of data.

## **Kafka Components**
Kafka consists of three primary components:

### **1. Producer**
- A **Producer** sends data (events/messages) to Kafka.
- Publishes data to a **Topic** (a logical container for messages).
- Can send messages **synchronously** or **asynchronously**.
- Supports **partitioning** for parallel processing.

### **2. Broker**
- A **Broker** is a Kafka server that stores and distributes messages.
- Multiple brokers form a **Kafka cluster**.
- Stores **Partitions** of Topics for scalability and fault tolerance.
- Ensures high availability via **replication**.

### **3. Consumer**
- A **Consumer** subscribes to Topics and retrieves messages.
- Consumers belong to **Consumer Groups** for parallel processing.
- Kafka supports **at least once**, **at most once**, or **exactly once** delivery.

## **ZooKeeper in Kafka**
**Apache ZooKeeper** is a distributed coordination service used by Kafka to manage metadata.

### **Role of ZooKeeper in Kafka**
- **Cluster Management** – Tracks active brokers.
- **Failure Detection & Recovery** – Detects broker failures & rebalances partitions.
- **Stores ACLs & Secrets** – Manages authentication and security settings.

💡 *Kafka is transitioning away from ZooKeeper with "KRaft" (Kafka Raft) for metadata management.*

## **Kafka Topics, Partitions, and Segments**
### **1. Topics**
- A **Topic** is a logical bucket that stores related messages.
- Topics are **immutable** and **log-based**.

### **2. Partitions**
- Topics are divided into multiple **Partitions** for parallelism.
- Each Partition is **ordered** and can be replicated for fault tolerance.

### **3. Segments**
- Partitions are further broken down into **Segments**.
- Segments improve performance by managing log data efficiently.
- Old Segments are deleted based on **retention policies**.

## **Kafka Data Flow**
1. **Producer sends messages** → Kafka Broker (Topic & Partition)
2. **Broker stores messages** → Organized in Partitions and Segments
3. **Consumer reads messages** → Processes data from the Topic

## **Kafka Use Cases**
✅ **Real-time Analytics** – Streaming website clicks, sensor readings, etc.  
✅ **Event-Driven Architectures** – Reacting to application changes.  
✅ **Log Aggregation** – Centralizing logs from multiple sources.  
✅ **Message Queues** – High-throughput messaging system.  
✅ **Machine Learning Pipelines** – Streaming data for AI model inference.
