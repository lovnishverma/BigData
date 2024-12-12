Here is a detailed guide for setting up **Data Integration and ETL Tools: Apache Sqoop, Flume, and Spark** using **Docker**. This guide includes installation steps and sample projects for each tool.

---

## **Prerequisites**
- Install **Docker**: Ensure Docker is installed and running on your machine. If not, follow the [Docker installation guide](https://docs.docker.com/get-docker/).
- Basic understanding of HDFS, data transfer, and ETL processes.

---

### **1. Apache Sqoop**

#### **Setup Apache Sqoop in Docker**

1. **Pull the Docker Image**:
   ```bash
   docker pull cloudera/quickstart:latest
   ```

2. **Run a Docker Container**:
   ```bash
   docker run --name sqoop-container -d -it -p 8888:8888 -p 8088:8088 cloudera/quickstart /usr/bin/docker-quickstart
   ```

3. **Access the Container**:
   ```bash
   docker exec -it sqoop-container bash
   ```

4. **Verify Sqoop Installation**:
   ```bash
   sqoop version
   ```

---

#### **Project: Import Data from MySQL to HDFS**
1. **Set Up MySQL**:
   - Use a MySQL Docker container:
     ```bash
     docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=testdb -d mysql:latest
     ```
   - Access the MySQL container:
     ```bash
     docker exec -it mysql-container mysql -u root -p
     ```
   - Create a sample table:
     ```sql
     CREATE TABLE testdb.orders (id INT, product VARCHAR(50), amount INT);
     INSERT INTO testdb.orders VALUES (1, 'Laptop', 1000), (2, 'Phone', 800);
     ```

2. **Import Data with Sqoop**:
   Inside the Sqoop container:
   ```bash
   sqoop import \
   --connect jdbc:mysql://mysql-container:3306/testdb \
   --username root \
   --password root \
   --table orders \
   --target-dir /user/hadoop/orders
   ```

3. **Verify Data in HDFS**:
   ```bash
   hdfs dfs -ls /user/hadoop/orders
   hdfs dfs -cat /user/hadoop/orders/part-m-00000
   ```

---

### **2. Apache Flume**

#### **Setup Apache Flume in Docker**

1. **Pull the Apache Flume Docker Image**:
   ```bash
   docker pull sequenceiq/flume
   ```

2. **Run a Docker Container**:
   ```bash
   docker run --name flume-container -d -it sequenceiq/flume
   ```

3. **Access the Container**:
   ```bash
   docker exec -it flume-container bash
   ```

4. **Verify Flume Installation**:
   ```bash
   flume-ng version
   ```

---

#### **Project: Ingest Log Files to HDFS**

1. **Create a Flume Configuration File**:
   Inside the container, create `flume-config.conf`:
   ```properties
   agent.sources = source1
   agent.sinks = sink1
   agent.channels = channel1

   agent.sources.source1.type = exec
   agent.sources.source1.command = tail -f /var/log/syslog

   agent.sinks.sink1.type = hdfs
   agent.sinks.sink1.hdfs.path = hdfs://localhost:9000/flume/logs

   agent.channels.channel1.type = memory
   agent.sources.source1.channels = channel1
   agent.sinks.sink1.channel = channel1
   ```

2. **Start Flume**:
   ```bash
   flume-ng agent --conf ./conf \
   --conf-file flume-config.conf \
   --name agent -Dflume.root.logger=INFO,console
   ```

3. **Verify Data in HDFS**:
   ```bash
   hdfs dfs -ls /flume/logs
   ```

---

### **3. Apache Spark**

#### **Setup Apache Spark in Docker**

1. **Pull the Apache Spark Docker Image**:
   ```bash
   docker pull bitnami/spark:latest
   ```

2. **Run a Docker Container**:
   ```bash
   docker run --name spark-container -d -it -p 4040:4040 bitnami/spark:latest
   ```

3. **Access the Container**:
   ```bash
   docker exec -it spark-container bash
   ```

4. **Verify Spark Installation**:
   ```bash
   spark-shell
   ```

---

#### **Project: Analyze Clickstream Data**

1. **Create a Sample Dataset**:
   Inside the Spark container, save the following data as `clickstream.txt`:
   ```
   user1,2024-12-12,page1
   user2,2024-12-12,page2
   user1,2024-12-12,page3
   ```

2. **Run a PySpark Script**:
   Create a file `clickstream_analysis.py`:
   ```python
   from pyspark.sql import SparkSession

   spark = SparkSession.builder.appName("Clickstream Analysis").getOrCreate()
   data = spark.read.csv("clickstream.txt", header=False)
   data.createOrReplaceTempView("clickstream")
   result = spark.sql("SELECT _c0 AS user, COUNT(_c1) AS clicks FROM clickstream GROUP BY _c0")
   result.show()
   ```

3. **Submit the Script**:
   ```bash
   spark-submit clickstream_analysis.py
   ```

4. **Output**:
   The script processes and displays the number of clicks per user.

---

### **Conclusion**

Using Docker simplifies the setup and management of **Apache Sqoop**, **Flume**, and **Spark**. With these tools:
- Apache **Sqoop** enables seamless data transfer between RDBMS and Hadoop.
- Apache **Flume** ingests real-time data into HDFS.
- Apache **Spark** provides powerful ETL and analytics capabilities.

Explore these setups and projects to build a robust data integration and ETL pipeline!
