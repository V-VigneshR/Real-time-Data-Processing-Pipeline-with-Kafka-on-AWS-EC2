# Setting up Kafka on an EC2 Instance

## PART 1: Kafka Installation and Setup

### 1. Create an EC2 Instance
- Use **Amazon Linux 2 AMI**
- Connect to the instance via SSH

> **Windows Layout for Different Services**
> - **Kafka Window** → Window `K`
> - **Zookeeper Window** → Window `Z`
> - **Topic & Producer** → Window `P`
> - **Consumer** → Window `C`

---

### 2. Download Kafka and Java

```sh
# Download Kafka
wget https://downloads.apache.org/kafka/3.9.0/kafka_2.12-3.9.0.tgz

# Uncompress Kafka
tar -xvf kafka_2.12-3.9.0.tgz

# Install Java
sudo yum install java-1.8.0-openjdk -y

# Check Java version
java -version
```

Navigate to the uncompressed Kafka directory:
```sh
cd kafka_2.12-3.9.0
```

---

### 3. Start Zookeeper
```sh
bin/zookeeper-server-start.sh config/zookeeper.properties
```

---

### 4. Start Kafka Server

- Open a **new terminal window** (Window `K`)

```sh
# Set Kafka Heap Size (Optional but recommended)
export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"

# Move to Kafka directory
cd kafka_2.12-3.9.0

# Start Kafka Server
bin/kafka-server-start.sh config/server.properties
```

---

### 5. Configure Kafka to Use Public IP

- If you are connecting **from a local machine**, modify `server.properties`:

```sh
sudo nano config/server.properties
```

- Locate the following line:
  ```plaintext
  #advertised.listeners=PLAINTEXT://your.host.name:9092
  ```
- **Modify it** by replacing `your.host.name` with **your EC2 Public IP**:
  ```plaintext
  advertised.listeners=PLAINTEXT://<EC2_PUBLIC_IP>:9092
  ```
- Save and exit (`Ctrl + X`, then `Y`, then `Enter`)

- Restart both **Zookeeper** and **Kafka**:

```sh
# Stop both servers
pkill -f kafka
pkill -f zookeeper

# Restart Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

# Restart Kafka (Ensure Heap Size is set)
export KAFKA_HEAP_OPTS="-Xmx512M -Xms256M"
bin/kafka-server-start.sh config/server.properties
```

> **Check RAM Usage (Optional)**
```sh
free -h
```

---

### 6. Configure EC2 Security Group for Kafka

1. Go to **EC2 Console**
2. Navigate to **Security Groups**
3. Select your instance's security group
4. Click **Edit Inbound Rules**
5. Add a new rule:
   - **Type**: `All Traffic`
   - **Source**: `Anywhere IPv4 (0.0.0.0/0)` _(Not best practice, but allows global access)_
6. **Save** the rules

---

### 7. Create a Kafka Topic

#### **From Local Machine:**
```sh
bin/kafka-topics.sh --create --topic demo_testing2 --bootstrap-server <EC2_PUBLIC_IP>:9092 --replication-factor 1 --partitions 1
```

#### **From EC2 Instance:**
```sh
bin/kafka-topics.sh --create --topic demo_testing2 --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1
```

> **Wait a few moments for the topic to be created.**
> If you encounter errors, ensure the inbound rule for EC2 is properly configured (Refer to Step 6).

---

### 8. Start a Kafka Producer

#### **From Local Machine:**
```sh
bin/kafka-console-producer.sh --topic demo_testing2 --bootstrap-server <EC2_PUBLIC_IP>:9092
```

#### **From EC2 Instance:**
```sh
bin/kafka-console-producer.sh --topic demo_testing2 --bootstrap-server localhost:9092
```

---

### 9. Start a Kafka Consumer

- Open a **new terminal window (Window `C`)**
- Navigate to the **Kafka directory**

#### **From Local Machine:**
```sh
bin/kafka-console-consumer.sh --topic demo_testing2 --bootstrap-server <EC2_PUBLIC_IP>:9092
```

#### **From EC2 Instance:**
```sh
bin/kafka-console-consumer.sh --topic demo_testing2 --bootstrap-server localhost:9092
```

---

### 10. Verify Producer-Consumer Communication
- Type a message in the **Producer Terminal**
- The message should be displayed in the **Consumer Terminal**

🎉 **Kafka is successfully set up on EC2!**