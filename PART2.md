# Kafka EC2 Setup

## Part 1: Setting Up Kafka on EC2

### 1. Create an EC2 Instance
- Use **Linux 2 AMI** and connect to it.

### 2. Download Kafka and Java
```sh
# Download Kafka
wget https://downloads.apache.org/kafka/3.9.0/kafka_2.12-3.9.0.tgz

# Uncompress Kafka
tar -xvf kafka_2.12-3.9.0.tgz

# Install Java
sudo yum install java-1.8.0-openjdk

# Verify Java installation
java -version
```
- Move to the uncompressed Kafka directory.

### 3. Start Zookeeper
```sh
bin/zookeeper-server-start.sh config/zookeeper.properties
```

### 4. Open Another Window
- Connect to the **same EC2 instance** to start Kafka in a separate terminal window.

### 5. Start Kafka Server
```sh
export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
bin/kafka-server-start.sh config/server.properties
```

### 6. Configure Kafka for Public IP Access
- Stop both Kafka and Zookeeper.
```sh
sudo nano config/server.properties
```
- Find the line:
  ```sh
  #advertised.listeners=PLAINTEXT://your.host.name:9092
  ```
- Remove `#` and replace `your.host.name` with the **Public IP** of your EC2 instance.
- Save and exit (`Ctrl+X`, `Y`, `Enter`).

### 7. Restart Kafka and Zookeeper
```sh
bin/zookeeper-server-start.sh config/zookeeper.properties
export KAFKA_HEAP_OPTS="-Xmx512M -Xms256M"
bin/kafka-server-start.sh config/server.properties
```
- Ensure sufficient memory:
```sh
free -h
```

### 8. Configure Security Access
- On the **EC2 console**:
  - Go to **Security** → Click on **Security Groups**.
  - Edit inbound rules → Add **All Traffic** with **Source: Anywhere (IPv4)** (**Not best practice**).
  - Save rules.

### 9. Create Kafka Topic and Start Producer/Consumer
- Open a new terminal window.
```sh
bin/kafka-topics.sh --create --topic demo_testing2 --bootstrap-server <Public_IP>:9092 --replication-factor 1 --partitions 1
```
- Start Producer:
```sh
bin/kafka-console-producer.sh --topic demo_testing2 --bootstrap-server <Public_IP>:9092
```
- Start Consumer:
```sh
bin/kafka-console-consumer.sh --topic demo_testing2 --bootstrap-server <Public_IP>:9092
```
- Now, messages from the producer should appear in the consumer terminal.

---

# Part 2: Connecting Kafka to Jupyter, S3, and AWS Glue

### 1. Open Jupyter Notebook
- Create `KafkaProducer.ipynb` and `KafkaConsumer.ipynb`.

### 2. Update Kafka Server Properties
- If using EC2 instance connect:
```sh
nano config/server.properties
```
- Change:
```sh
listeners=PLAINTEXT://0.0.0.0:9092
```
- Update **Security Groups**:
  - Add **Custom TCP Rule** with **Port 9092** and **Source: My IP**.
- Restart Kafka and Zookeeper servers.

### 3. Configure Kafka Producer in Jupyter Notebook
- Set `bootstrap_servers=['<Public_IP>:9092']`.
- Sample message:
```python
producer.send('demo_testing2', value={'name': 'Vignesh'})
```

### 4. Set Up AWS S3
- Create a new **S3 bucket**.
- Install dependencies in KafkaConsumer notebook:
```sh
!pip install s3fs
```

### 5. Configure AWS CLI
```sh
aws configure
```
- Enter **AWS credentials**.
- Troubleshooting:
  - Delete AWS credentials and reconfigure.
  - Create **bucket policy** for `PutObject` and `GetObject`.
  - Attach policy to IAM user and bucket.
  - Restart Jupyter kernel.
  - Explicitly mention `AccessID`, `SecretKey`, and **S3 region**.

### 6. Use AWS Glue Crawler
- **Create a new Crawler**:
  - Set S3 bucket path (add `/` at the end).
  - Select **Add S3 data source**.
  - Create **IAM role** for Glue:
    - New Role → Select **Glue** → **Administrator Access**.
  - Create a **new database** and select it.
  - Run the crawler and wait for completion.

### 7. Query Data Using Athena
- Go to **Athena** → **Editor**.
- Set **S3 bucket** in **Settings**.
- Select the database created in Glue.
- Right-click table → **Preview** to see data.

### 8. Real-Time Data Processing
- Run **KafkaProducer.ipynb** and **KafkaConsumer.ipynb**.
- Validate real-time data updates.

### 9. Data Visualization with QuickSight
- Use **QuickSight** for interactive visualizations.

### 10. Conclusion
✅ **End-to-End Data Engineering Project Successfully Created!** 🎉
