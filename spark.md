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
