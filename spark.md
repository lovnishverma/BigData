To perform these Spark SQL and DataFrame operations inside a Docker container, you'll need to set up a Docker environment that includes Apache Spark along with the necessary configurations. Below is a detailed guide on how to set up Docker for running Spark, loading data, and performing SQL operations as per the slides:

### **1. Install Docker**
Before you begin, make sure Docker is installed on your system. You can download and install it from [Docker's official website](https://www.docker.com/get-started).

### **2. Set Up Docker Container for Spark**
You can run Apache Spark in a Docker container by using the official Spark Docker image from the [Apache Spark Docker Hub](https://hub.docker.com/r/spark/Spark). 

**Step-by-Step Guide**:

#### Step 1: Pull the Official Spark Image
Use the following command to pull the Apache Spark Docker image:

```bash
docker pull bitnami/spark:latest
```

#### Step 2: Run Spark Container
Once the image is pulled, you can start a Spark container with the following command. This will start both Spark and the necessary Hadoop dependencies.

```bash
docker run -d --name spark-container -p 8080:8080 -p 7077:7077 bitnami/spark:latest
```

- `-d` starts the container in the background.
- `-p 8080:8080` exposes the Spark UI on port 8080.
- `-p 7077:7077` allows Spark to communicate with clients on port 7077.

#### Step 3: Access Spark in the Docker Container
To interact with Spark, you can use a Jupyter Notebook or run the Spark shell directly inside the container.

1. **Connect to the Docker container**:
   ```bash
   docker exec -it spark-container bash
   ```

2. **Start Spark Shell**:
   Inside the container, you can start the Spark shell (Scala):

   ```bash
   spark-shell
   ```

   Alternatively, you can run PySpark for Python if you prefer Python.

### **3. Load and Transform Data in Docker Spark Container**

You can now follow the steps mentioned in your slides using the Spark shell inside the Docker container.

#### **Create DataFrames from Different Sources (CSV, JSON, etc.)**

1. **CSV Example**:
   Assuming you have a CSV file `data.csv` in your local machine, copy it to the Docker container:

   ```bash
   docker cp data.csv spark-container:/opt/bitnami/spark/data.csv
   ```

   Now, inside the Spark shell, you can load the data:

   ```scala
   val df = spark.read.option("header", "true").csv("/opt/bitnami/spark/data.csv")
   df.show()
   ```

2. **JSON Example**:
   Similarly, copy a JSON file:

   ```bash
   docker cp data.json spark-container:/opt/bitnami/spark/data.json
   ```

   Load the JSON file in Spark:

   ```scala
   val dfJson = spark.read.json("/opt/bitnami/spark/data.json")
   dfJson.show()
   ```

#### **Running SQL Queries in Spark**

After loading the DataFrame, you can run SQL queries by registering the DataFrame as a temporary view.

1. **Create or Replace Temp View**:

   ```scala
   df.createOrReplaceTempView("employee")
   ```

2. **Run SQL Queries**:

   ```scala
   val sqlData = spark.sql("SELECT * FROM employee WHERE salary > 50000")
   sqlData.show()
   ```

### **4. Loading Data from Multiple Sources**
The following examples show how to load data from different sources directly in your Spark Docker container:

#### **CSV Loading Example**:
```scala
val csvData = spark.read.option("header", "true").csv("/opt/bitnami/spark/employees.csv")
csvData.show()
```

#### **JSON Loading Example**:
```scala
val jsonData = spark.read.json("/opt/bitnami/spark/employees.json")
jsonData.show()
```

#### **Parquet Loading Example**:
For Parquet data, first copy the Parquet file to the container, then use:

```scala
val parquetData = spark.read.parquet("/opt/bitnami/spark/employees.parquet")
parquetData.show()
```

#### **JDBC (MySQL) Example**:
To load data from a MySQL database, you’ll need to set up a MySQL container or a remote MySQL database. Here's how you can load data from MySQL using JDBC:

1. Copy your JDBC driver to the container or ensure it's accessible.
2. Use the following code:

```scala
val jdbcData = spark.read.format("jdbc")
  .option("url", "jdbc:mysql://localhost:3306/employees")
  .option("dbtable", "employee")
  .option("user", "username")
  .option("password", "password")
  .load()
jdbcData.show()
```

### **5. Data Transformations in Docker Spark Container**

Now that you have your DataFrames loaded, you can perform various transformations.

#### **Adding a Column**:
```scala
val newData = df.withColumn("newColumn", lit(100))
newData.show()
```

#### **Joining DataFrames**:
If you have multiple DataFrames (e.g., `df1` and `df2`), you can join them as follows:

```scala
val joinedData = df1.join(df2, df1("id") === df2("id"))
joinedData.show()
```

#### **GroupBy and Aggregation**:
```scala
val groupedData = df.groupBy("department").agg(avg("salary"))
groupedData.show()
```

### **6. Caching DataFrames**
Caching data is essential for performance when performing repeated operations on the same data:

```scala
df.cache()
df.show()
```

### **7. Persisting Data to Disk**
You can save your transformed data to disk in various formats:

#### **Save to CSV**:
```scala
df.write.option("header", "true").csv("/opt/bitnami/spark/output.csv")
```

#### **Save to Parquet**:
```scala
df.write.parquet("/opt/bitnami/spark/output.parquet")
```

#### **Save to JSON**:
```scala
df.write.json("/opt/bitnami/spark/output.json")
```

### **8. Stop the Docker Container**
After you are done, you can stop the Spark container:

```bash
docker stop spark-container
```

### **Conclusion**
Running Spark and Spark SQL in a Docker container allows you to experiment with large-scale data processing in an isolated environment. The steps above allow you to:
- Set up a Docker container for Spark.
- Load data from multiple sources (CSV, JSON, Parquet, JDBC).
- Perform transformations and SQL queries on DataFrames.
- Save the results to different file formats.

By using Docker, you can quickly set up a reproducible Spark environment for practicing the concepts in your presentation.

To configure **Apache Spark** in your existing Docker container setup (with Sqoop, Hadoop, MySQL, and Java), follow these steps to integrate Spark with your environment and perform basic operations:

### **1. Install Apache Spark in Your Docker Container**
You can manually install Apache Spark inside your existing Docker container by following these steps:

#### **a. Access the Container**
First, access the running Docker container where Hadoop, Sqoop, and MySQL are installed:
```bash
docker exec -it <container_name> bash
```
Replace `<container_name>` with your actual container name.

#### **b. Install Apache Spark**
You can download and install Apache Spark in the container:

1. **Download Apache Spark:**
   Use the `wget` command to download the latest stable version of Apache Spark:
   ```bash
   wget https://archive.apache.org/dist/spark/spark-3.5.0/spark-3.5.0-bin-hadoop3.tgz
   ```

2. **Extract the Spark Archive:**
   Extract the downloaded archive:
   ```bash
   tar -xvzf spark-3.5.0-bin-hadoop3.tgz
   ```

3. **Move Spark to /opt Directory (optional):**
   It's a good practice to move Spark to the `/opt` directory:
   ```bash
   mv spark-3.5.0-bin-hadoop3 /opt/spark
   ```

4. **Set Spark Environment Variables:**
   To make Spark available globally, set the following environment variables:
   ```bash
   echo 'export SPARK_HOME=/opt/spark' >> ~/.bashrc
   echo 'export PATH=$SPARK_HOME/bin:$PATH' >> ~/.bashrc
   source ~/.bashrc
   ```

5. **Check Spark Version:**
   Verify if Spark was installed correctly by running:
   ```bash
   spark-submit --version
   ```

### **2. Configure Spark with Hadoop**
Now that Spark is installed, you need to configure it to run on your Hadoop cluster.

#### **a. Edit `spark-defaults.conf`**
In your container, edit the `spark-defaults.conf` file to configure Spark to use Hadoop for storage and job execution:
```bash
nano /opt/spark/conf/spark-defaults.conf
```

Add the following configurations:

```properties
spark.master                    yarn
spark.eventLog.enabled           true
spark.eventLog.dir               hdfs://<hadoop_name_node_host>/spark-logs
spark.hadoop.fs.defaultFS        hdfs://<hadoop_name_node_host>:9000
```

Replace `<hadoop_name_node_host>` with the IP address or hostname of your Hadoop NameNode.

#### **b. Configure Hadoop with Spark**
Modify the Hadoop `core-site.xml` to allow Spark to communicate with HDFS:

```bash
nano /opt/hadoop/etc/hadoop/core-site.xml
```

Add the following property:

```xml
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://<hadoop_name_node_host>:9000</value>
</property>
```

Make sure to replace `<hadoop_name_node_host>` with the correct NameNode host.

#### **c. Restart Hadoop and Spark Services**
After modifying the configuration files, restart the Hadoop services:
```bash
/opt/hadoop/sbin/stop-all.sh
/opt/hadoop/sbin/start-all.sh
```

### **3. Run a Spark Job**
Now that you have configured Spark, you can run some basic Spark commands and examples.

#### **a. Start Spark Shell**
To interactively run Spark commands, you can use the Spark Shell. Use this command to start the Spark shell:
```bash
/opt/spark/bin/spark-shell --master yarn
```

Once the Spark Shell starts, you can run Spark commands.

#### **b. Simple Spark Command Example**
Here is a basic example to calculate the number of words in a file using Spark:

1. **Create a Text File (for example, `input.txt`) on HDFS**:
   ```bash
   echo "Hello Hadoop Spark" > input.txt
   hdfs dfs -put input.txt /user/hadoop/input.txt
   ```

2. **Word Count Spark Job**:
   In the Spark Shell, use the following code to count the words in the file:

   ```scala
   val textFile = sc.textFile("hdfs://<hadoop_name_node_host>:9000/user/hadoop/input.txt")
   val wordCount = textFile.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
   wordCount.collect().foreach(println)
   ```

   Replace `<hadoop_name_node_host>` with your Hadoop NameNode's host address.

#### **c. Spark Submit Job (example)**
You can also submit Spark jobs using `spark-submit`:

1. **Create a Simple Spark Job (`wordcount.py`)**:
   Here's a simple Python example for word count:

   ```python
   from pyspark import SparkContext, SparkConf

   conf = SparkConf().setAppName("WordCount")
   sc = SparkContext(conf=conf)

   lines = sc.textFile("hdfs://<hadoop_name_node_host>:9000/user/hadoop/input.txt")
   words = lines.flatMap(lambda line: line.split(" "))
   word_counts = words.map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
   
   for word, count in word_counts.collect():
       print(f"{word}: {count}")
   ```

2. **Submit the Job**:
   Run the job using `spark-submit`:
   ```bash
   /opt/spark/bin/spark-submit --master yarn wordcount.py
   ```

### **4. Monitor Spark Job (Web UI)**
You can monitor your Spark jobs using the Spark Web UI, which is available by default on port `4040` on the **Spark Master**. You can also monitor it through the **YARN ResourceManager** Web UI.

To access the Spark Web UI, visit:
- **Spark Master UI**: [http://<spark_master_host>:8080](http://<spark_master_host>:8080)
- **Spark Driver UI**: [http://<spark_master_host>:4040](http://<spark_master_host>:4040)

---

### **Summary of Basic Spark Usage**:
1. **Setup**: Install Apache Spark in your Docker container, configure it to run with Hadoop (using YARN).
2. **Run Jobs**: Use Spark Shell for interactive commands or `spark-submit` for batch jobs.
3. **Monitor**: Access Spark's Web UI to monitor your jobs and check the status of the cluster.

Let me know if you need more details on running Spark commands or troubleshooting any part of the setup!
