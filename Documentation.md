# **Stock Market Kafka Data Engineering - End-to-End Project**

## **Project Overview**
This project demonstrates real-time stock market data processing using **Apache Kafka** on **AWS**. It covers setting up Kafka on **EC2**, creating producers and consumers, streaming data, and integrating with **AWS S3, Glue, and Athena** for real-time analytics.

---

## **1. Setting Up Kafka on AWS EC2**

### **Step 1: Launch an EC2 Instance**
1. Log into AWS and navigate to **EC2**.
2. Click on **Launch Instance**.
3. Choose **Amazon Linux 2 (Free Tier Eligible)**.
4. Select **t2.micro** instance type.
5. Create and download a **key pair** (used for SSH access).
6. Keep all settings default and click **Launch Instance**.

### **Step 2: Connect to EC2 Instance**
1. Open a terminal and navigate to the key pair folder.
2. Run the following command to set permissions (only for Mac/Linux):
   ```sh
   chmod 400 <your-key.pem>
   ```
3. SSH into the EC2 instance:
   ```sh
   ssh -i <your-key.pem> ec2-user@<EC2-PUBLIC-IP>
   ```

---

## **2. Installing Kafka on EC2**

### **Step 1: Download and Extract Kafka**
```sh
wget https://downloads.apache.org/kafka/latest/kafka_2.13-3.0.0.tgz
tar -xzf kafka_2.13-3.0.0.tgz
cd kafka_2.13-3.0.0
```

### **Step 2: Install Java**
Kafka requires Java to run. Install Java 1.8:
```sh
sudo yum install java-1.8.0 -y
java -version
```

### **Step 3: Start Zookeeper & Kafka Server**
```sh
bin/zookeeper-server-start.sh config/zookeeper.properties &
bin/kafka-server-start.sh config/server.properties &
```

### **Step 4: Modify Kafka Configurations for Public Access**
Edit `server.properties` to allow public connections:
```sh
sudo nano config/server.properties
```
Find and update the line:
```sh
advertised.listeners=PLAINTEXT://<EC2-PUBLIC-IP>:9092
```
Save and restart Kafka:
```sh
bin/kafka-server-stop.sh
bin/kafka-server-start.sh config/server.properties &
```

---

## **3. Kafka Producer & Consumer**

### **Step 1: Create a Kafka Topic**
```sh
bin/kafka-topics.sh --create --topic stock-market --bootstrap-server <EC2-PUBLIC-IP>:9092 --partitions 3 --replication-factor 1
```

### **Step 2: Start Producer & Consumer**
- **Producer** (Sends messages to Kafka)
  ```sh
  bin/kafka-console-producer.sh --topic stock-market --bootstrap-server <EC2-PUBLIC-IP>:9092
  ```
- **Consumer** (Reads messages from Kafka)
  ```sh
  bin/kafka-console-consumer.sh --topic stock-market --from-beginning --bootstrap-server <EC2-PUBLIC-IP>:9092
  ```

---

## **4. Real-Time Stock Market Data Processing**

### **Step 1: Install Required Python Packages**
```sh
pip install kafka-python pandas boto3
```

### **Step 2: Kafka Producer (Simulating Stock Data)**
```python
from kafka import KafkaProducer
import pandas as pd
import json
import time

producer = KafkaProducer(
    bootstrap_servers=['<EC2-PUBLIC-IP>:9092'],
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

data = pd.read_csv("stock_data.csv")

while True:
    sample = data.sample(1).to_dict(orient='records')[0]
    producer.send("stock-market", value=sample)
    time.sleep(1)
```

### **Step 3: Kafka Consumer (Saving to S3)**
```python
from kafka import KafkaConsumer
import json
import boto3

consumer = KafkaConsumer(
    'stock-market',
    bootstrap_servers=['<EC2-PUBLIC-IP>:9092'],
    auto_offset_reset='earliest',
    value_deserializer=lambda x: json.loads(x.decode('utf-8'))
)

s3 = boto3.client('s3')

for message in consumer:
    data = message.value
    s3.put_object(Bucket="your-s3-bucket", Key=f"stock-data/{time.time()}.json", Body=json.dumps(data))
```

---

## **5. AWS Glue & Athena for Data Querying**

### **Step 1: Create an S3 Bucket**
1. Go to **AWS S3** and create a bucket (e.g., `stock-market-data`).

### **Step 2: Create AWS Glue Crawler**
1. Go to **AWS Glue** → **Crawlers** → **Create Crawler**.
2. Select **S3** as data source and choose your bucket.
3. Assign an **IAM Role** with S3 read permissions.
4. Run the crawler to create the Glue Data Catalog.

### **Step 3: Query Data with Athena**
1. Navigate to **AWS Athena**.
2. Set the **Query Result Location** in S3.
3. Run queries like:
   ```sql
   SELECT * FROM stock_market_data LIMIT 10;
   ```

---

## **6. Conclusion & Next Steps**
✅ **Successfully set up Kafka on AWS EC2**
✅ **Produced and consumed stock market data**
✅ **Stored data in S3 using AWS Glue & Athena**
✅ **Enabled real-time data querying**

🔹 Next Steps: Integrate AWS Lambda, Redshift, or Databricks for advanced analytics!

**Happy Streaming! 🚀**
