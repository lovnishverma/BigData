### 1. **You Need to Specify Ports While Creating the Container?**

You need to specify the ports when creating the container, especially if you want to expose certain services to your host machine. For example, Spark has a web UI that runs on port `8080` by default, and the Spark master is accessible via port `7077`. 

Specifying the ports during the container creation ensures that you can access these services from your host machine. Here's why you need to do it:

- **8080** is the Spark UI (used to monitor Spark jobs).
- **7077** is the default port for the Spark master.
- **Additional ports** (like `2181`, `50070`, `50075`, etc.) are related to Hadoop ecosystem services (like HDFS, YARN, etc.), and you should specify them if you're running Hadoop alongside Spark in the container.

If you are working with **Spark-only** in the container (without Hadoop), then you might not need to specify Hadoop-related ports, but the Spark ports are necessary for communication with your Spark instance.

### 2. **You Don't Need to Install Hadoop?**

- **You don't need to install Hadoop if you're only using Spark**. 
  - Apache Spark can run independently of Hadoop and does not require a Hadoop cluster to operate. In fact, the Spark container can run in "standalone mode," where it doesn’t require Hadoop’s distributed storage (HDFS) or YARN resource manager.

- **However**, if you plan to run Spark with Hadoop (for example, using HDFS for distributed file storage or YARN for resource management), you'll need to install and configure Hadoop as well. 

Here’s a breakdown of the two options:
- **Spark in standalone mode (without Hadoop)**: You only need the Spark container, no need for Hadoop.
- **Spark with Hadoop**: You’ll need to run Hadoop services (like HDFS and YARN) alongside Spark, either within the same container or separate containers. In this case, you'll need to set up both Hadoop and Spark services together.

### Running Spark in Standalone Mode (without Hadoop):
If you just want to use Spark without Hadoop, the following steps should work:

1. **Pull Spark Docker image**:
   ```bash
   docker pull bitnami/spark:latest
   ```

2. **Run Spark container**:
   ```bash
   docker run -d --name spark-container -p 8080:8080 -p 7077:7077 bitnami/spark:latest
   ```

   Here, ports `8080` (Spark UI) and `7077` (Spark master port) are exposed.

3. **Access the Spark container**:
   ```bash
   docker exec -it spark-container bash
   ```

4. **Run Spark Shell or PySpark**:
   - Scala: `spark-shell`
   - Python: `pyspark`

### Running Spark with Hadoop (for HDFS and YARN):

If you intend to integrate Spark with Hadoop (for example, to use HDFS for file storage), you can use a pre-built Docker image that includes both Hadoop and Spark, or you can set up your own multi-container environment using Docker Compose. Here’s how you could go about this:

1. **Pull the Hadoop + Spark image** (e.g., `bde2020/hadoop-spark`).
   ```bash
   docker pull bde2020/hadoop-spark:latest
   ```

2. **Run the Hadoop + Spark container**:
   This container will have all the necessary services (Hadoop HDFS, YARN, and Spark).
   ```bash
   docker run -d --name hadoop-spark-container -p 8080:8080 -p 7077:7077 -p 9000:9000 -p 50070:50070 bde2020/hadoop-spark:latest
   ```

3. **Start the services** inside the container (Hadoop NameNode, ResourceManager, Spark, etc.):
   You might need to initialize HDFS or start the YARN ResourceManager manually depending on the image.

### Conclusion:
- **If you're just using Spark**, no need to install Hadoop. You can run Spark in standalone mode with just the Spark container and relevant ports.
- **If you want Spark to work with Hadoop**, then you'll need to set up Hadoop (HDFS, YARN) as well, either in a separate container or within the same container.




![image](https://github.com/user-attachments/assets/14ad1811-2f27-464c-a387-aaa46f0c8aa7)

To perform practicals with Spark and Scala, you first need to set up a directory structure and create sample files (such as CSV files) to load into Spark. Below is a step-by-step guide to create a directory, generate some sample data files, and then use them in your Spark SQL operations.

### Step 1: Create a Directory and Files

1. **Create a directory for your Spark project**:
   You can create a directory where your Spark project will reside. Let’s name it `spark-practicals`.

   **Linux/macOS**:
   ```bash
   mkdir spark-practicals
   cd spark-practicals
   ```

   **Windows (Command Prompt)**:
   ```cmd
   mkdir spark-practicals
   cd spark-practicals
   ```

2. **Create a CSV file inside the directory**:
   Next, let's create a CSV file with some sample data. You can use any text editor or even the terminal/command prompt to create a file named `employees.csv` in the `spark-practicals` directory.

   **Example of creating a CSV file:**

   **Linux/macOS**:
   ```bash
   echo -e "id,name,age,department,salary\n1,John,30,HR,3000\n2,Jane,35,Finance,4000\n3,Sam,28,Engineering,5000\n4,Lisa,40,Marketing,6000" > employees.csv
   ```

   **Windows (Command Prompt)**:
   ```cmd
   echo id,name,age,department,salary > employees.csv
   echo 1,John,30,HR,3000 >> employees.csv
   echo 2,Jane,35,Finance,4000 >> employees.csv
   echo 3,Sam,28,Engineering,5000 >> employees.csv
   echo 4,Lisa,40,Marketing,6000 >> employees.csv
   ```

   This will create a CSV file named `employees.csv` with the following data:
   ```
   id,name,age,department,salary
   1,John,30,HR,3000
   2,Jane,35,Finance,4000
   3,Sam,28,Engineering,5000
   4,Lisa,40,Marketing,6000
   ```

### Step 2: Run Spark in Scala

Now, let’s run the code to load the CSV file into a Spark DataFrame, transform it, and perform operations using Spark SQL.

1. **Start Spark Shell**:
   Make sure you have `Spark` installed, and then start the Spark shell in Scala.

   **In your terminal**:
   ```bash
   spark-shell
   ```

2. **Load the CSV file into a DataFrame**:

   In the Spark shell, run the following code:

   ```scala
   import org.apache.spark.sql.SparkSession
   val spark = SparkSession.builder()
     .appName("Spark SQL Example")
     .master("local[*]")
     .getOrCreate()
   val df = spark.read.option("header", "true").csv("/spark-practicals/employees.csv")
   df.show()
   ```

   This will display the contents of the CSV file as a DataFrame:

   ```
   +---+----+---+-----------+------+
   | id|name|age| department|salary|
   +---+----+---+-----------+------+
   |  1|John| 30|         HR|  3000|
   |  2|Jane| 35|    Finance|  4000|
   |  3| Sam| 28|Engineering|  5000|
   |  4|Lisa| 40|  Marketing|  6000|
   +---+----+---+-----------+------+
   ```

3. **Performing SQL Operations**:

   Once the data is loaded into a DataFrame, you can run SQL queries. First, register the DataFrame as a temporary SQL table.

   ```scala
   // Register the DataFrame as a temporary table
   df.createOrReplaceTempView("employees")

   // Run an SQL query to select employees with a salary greater than 4000
   val sqlData = spark.sql("SELECT * FROM employees WHERE salary > 4000")
   
   // Show the results
   sqlData.show()
   ```

   The output will look like this:

   ```
   +---+----+---+-----------+------+
   | id|name|age| department|salary|
   +---+----+---+-----------+------+
   |  3| Sam| 28|Engineering|  5000|
   |  4|Lisa| 40|  Marketing|  6000|
   +---+----+---+-----------+------+
   ```

4. **Saving the Data**:

   After transforming or processing the data, you can save the results in different formats such as CSV, Parquet, or JSON.

   To save the transformed DataFrame as a new CSV file:

   ```scala
   sqlData.write.option("header", "true").csv("output_employees.csv")
   ```

   This will save the result into a file called `output_employees.csv` in the current directory.



### You can use this same process to load and analyze data from different file formats (CSV, JSON, Parquet) in Spark, and perform various transformations and SQL operations.

![image](https://github.com/user-attachments/assets/21f50d78-31bd-4db9-8ea4-6ce762bde45a)


Here's the content in markdown format for the Docker command and the Scala WordCount program.

```markdown
### Docker Command to Copy File

Use the following command to copy the `data.txt` file from your local system to the Docker container:

```bash
docker cp data.txt spark-container:/opt/bitnami/spark/spark-practicals/data.txt
```

### WordCount Program in Scala

The following Scala code performs a WordCount operation using Apache Spark:

```scala
import org.apache.spark.{SparkConf, SparkContext}

val conf = new SparkConf().setAppName("WordCountExample").setMaster("local")
val sc = new SparkContext(conf)

val input = sc.textFile("/opt/bitnami/spark/spark-practicals/data.txt")

val wordPairs = input.flatMap(line => line.split(" ")).map(word => (word, 1))

val wordCounts = wordPairs.reduceByKey((a, b) => a + b)

wordCounts.collect().foreach { case (word, count) =>
  println(s"$word: $count")
}

sc.stop()
```


![image](https://github.com/user-attachments/assets/2ced2923-4192-4359-aa47-81c7e7608d32)


### Steps:

1. **Copy File**: Use `docker cp` to move or create the file inside the Docker container.
2. **WordCount Program**: The program reads the file, splits it into words, and counts the occurrences of each word.
3. **Output**: The word counts will be printed to the console when the program is executed.
```



![image](https://github.com/user-attachments/assets/e1787f84-a89d-4a14-a71f-3fe5ccaa9323)



**##Closing the Spark Session**

Once you are done with your operations, don’t forget to stop the Spark session.

```scala
spark.stop()
```

Running **Apache Spark in Local Mode with Hadoop** involves configuring Spark to run on your local machine while still leveraging Hadoop's components, like HDFS (Hadoop Distributed File System) for storage and possibly YARN (Yet Another Resource Negotiator) for managing resources, although the overall execution will be single-node (locally). In this configuration, Spark runs on your local machine, but you can still access and utilize Hadoop's storage and resource management features.

### Key Points about Local Mode (with Hadoop):
- **Single Node Setup**: While Spark runs locally on a single machine (like in "local mode"), you can still utilize Hadoop's HDFS for data storage, and optionally YARN for resource management.
- **HDFS for Storage**: You can use HDFS for distributed storage, but Spark still operates in a non-distributed manner (using only local resources).
- **Hadoop Integration**: Hadoop components (like HDFS and YARN) are available for managing files and resources, though Spark itself won’t be running on a distributed cluster.

### Steps to Set Up Spark in Local Mode with Hadoop

#### Step 1: **Install Hadoop and Spark**
1. **Install Hadoop**: 
   - You can install Hadoop locally on your machine (you can use [Apache Hadoop](https://hadoop.apache.org/releases.html) for installation).
   - You’ll need to configure HDFS (Hadoop Distributed File System) and, optionally, YARN (for resource management).
   
2. **Install Spark**: 
   - Download Apache Spark and install it (as explained previously).
   - Ensure that Spark is configured to integrate with Hadoop (typically by setting the correct environment variables and configurations).

#### Step 2: **Configure Hadoop for Local Mode**
To use Hadoop with Spark in local mode, you need to set up HDFS and optionally configure YARN.

1. **Configure HDFS**:
   Hadoop’s HDFS is typically set up in a distributed fashion, but it can also run on a single node (local mode). You’ll need to modify the Hadoop configuration files:

   - **hdfs-site.xml**: Set the directory for storing HDFS files locally:
     ```xml
     <configuration>
       <property>
         <name>dfs.namenode.name.dir</name>
         <value>file:/path/to/hdfs/namenode</value>
       </property>
       <property>
         <name>dfs.datanode.data.dir</name>
         <value>file:/path/to/hdfs/datanode</value>
       </property>
     </configuration>
     ```

   - **core-site.xml**: Set the Hadoop filesystem URI:
     ```xml
     <configuration>
       <property>
         <name>fs.defaultFS</name>
         <value>hdfs://localhost:9000</value>
       </property>
     </configuration>
     ```

2. **Configure YARN** (Optional):
   - If you want to use YARN for resource management (though Spark will still run locally), you’ll need to set up the `yarn-site.xml` file to configure it to work with your local machine.
   
   - **yarn-site.xml** (example configuration for local mode):
     ```xml
     <configuration>
       <property>
         <name>yarn.resourcemanager.hostname</name>
         <value>localhost</value>
       </property>
     </configuration>
     ```

#### Step 3: **Configure Spark to Use Hadoop**
Spark needs to be configured to use the Hadoop setup for local mode.

1. **Set `spark.master` to Local**:
   When running Spark in local mode, the `spark.master` property should be set to `"local[*]"` in the Spark configuration, which ensures Spark runs on a single machine using all available cores.

   ```bash
   --master local[*]
   ```

2. **Set `spark.hadoop.fs.defaultFS`**:
   This configuration allows Spark to use HDFS for storage.
   ```bash
   --conf "spark.hadoop.fs.defaultFS=hdfs://localhost:9000"
   ```

3. **Set HDFS configuration paths**:
   Ensure that Spark can find Hadoop’s configuration files (e.g., `core-site.xml` and `hdfs-site.xml`).
   ```bash
   --conf "spark.hadoop.hadoop.home.dir=/path/to/hadoop"
   ```

#### Step 4: **Start Spark in Local Mode with Hadoop**

1. **Start the Hadoop NameNode and DataNode**:
   If you’ve configured HDFS, you need to start Hadoop’s NameNode and DataNode.

   Start the NameNode (HDFS master):
   ```bash
   start-dfs.sh
   ```

2. **Start the Spark shell in local mode**:
   Now, you can start the Spark shell or run a Spark job with the Hadoop configurations enabled.

   **For Scala Spark shell**:
   ```bash
   spark-shell --master local[*] --conf "spark.hadoop.fs.defaultFS=hdfs://localhost:9000" --conf "spark.hadoop.hadoop.home.dir=/path/to/hadoop"
   ```

   **For PySpark**:
   ```bash
   pyspark --master local[*] --conf "spark.hadoop.fs.defaultFS=hdfs://localhost:9000" --conf "spark.hadoop.hadoop.home.dir=/path/to/hadoop"
   ```

   This will launch Spark in local mode but with Hadoop's HDFS as the storage backend.

#### Step 5: **Load Data from HDFS and Run Operations**

Once Spark is running in local mode with Hadoop, you can load data from HDFS and perform operations. Here are a few examples:

1. **Loading Data from HDFS (CSV)**:
   ```scala
   val df = spark.read.option("header", "true").csv("hdfs://localhost:9000/path/to/data.csv")
   df.show()
   ```

2. **Running SQL Queries**:
   You can register the DataFrame as a temporary view and run SQL queries on it:
   ```scala
   df.createOrReplaceTempView("employees")
   val sqlData = spark.sql("SELECT * FROM employees WHERE salary > 50000")
   sqlData.show()
   ```

3. **Persisting Data back to HDFS**:
   Save the transformed data back to HDFS:
   ```scala
   df.write.option("header", "true").csv("hdfs://localhost:9000/path/to/output.csv")
   ```

#### Step 6: **Stop Spark and Hadoop Services**
When you're done, you can stop the Spark and Hadoop services.

1. **Stop Hadoop**:
   ```bash
   stop-dfs.sh
   ```

2. **Stop Spark**:
   You can stop the Spark shell or job:
   ```scala
   spark.stop()
   ```

### Conclusion

Running **Spark in Local Mode with Hadoop** allows you to utilize Hadoop's HDFS for storage while running Spark locally on your machine. This setup is useful when you want to integrate Spark with Hadoop (for distributed storage) but don't need a full-fledged Hadoop cluster. It’s great for testing and development before scaling up to a multi-node cluster.
