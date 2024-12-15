### **Apache Hive Setup and Project Creation (Step-by-Step Guide)**

---

#### **🐝 Part 1: Installing Apache Hive using Docker**

---

##### **🔧 Prerequisites**
1. **Install Docker**:
   - Download Docker from [Docker's official website](https://www.docker.com/).
   - Follow the installation steps for your OS and ensure Docker is running.

2. **Verify Docker Installation**:
   - Open a terminal or Command Prompt and run:
     ```bash
     docker --version
     ```
     Ensure Docker is installed correctly and running.

---

##### **📥 Step 1: Pull the Hive Docker Image**
1. Open **Command Prompt** and enter:
   ```bash
   docker pull apache/hive:4.0.0-alpha-2
   ```
2. This will take some time .....This will download the Hive Docker image to your system.  
   📝 *The `4.0.0-alpha-2` is the version we are using.*

---

##### **⚙️ Step 2: Set Environment Variable (Windows PowerShell)**
1. Open **PowerShell** and set the Hive version environment variable:
   ```powershell
   $env:HIVE_VERSION="4.0.0-alpha-2"
   ```
   📝 *This helps us avoid hardcoding the version repeatedly.*

---

##### **🚀 Step 3: Run HiveServer2 in a Docker Container**
1. Back in **Command Prompt**, run the following command to start the HiveServer2 container:
   ```bash
   docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 --name hive4 apache/hive:4.0.0-alpha-2
   ```
2. This command:
   - Runs the container in detached mode (`-d`).
   - Maps ports `10000` (HiveServer2) and `10002` (web interface) to your system.
   - Names the container `hive4`.

---

##### **🖥️ Step 4: Connect to Hive using Beeline**
1. Run the following in **Command Prompt** to connect to Hive through Beeline:
   ```bash
   docker exec -it hive4 beeline -u 'jdbc:hive2://localhost:10000/'
   ```
2. You should see a successful connection message:
   ```
   Connected to: Apache Hive (version 4.0.0-alpha-2)
   Beeline version 4.0.0-alpha-2 by Apache Hive
   ```

---

#### **📂 Part 2: Creating a Simple Hive Project**

---

##### **🎯 Objective**
We will:
1. Create a database.
2. Create a table inside the database.
3. Load data into the table.
4. Run queries to retrieve data.

---

##### **💾 Step 1: Create a Database**
In the Beeline CLI:
```sql
CREATE DATABASE mydb;
USE mydb;
```
- 📝 *`mydb` is the name of the database. Replace it with your preferred name.*

---

##### **📋 Step 2: Create a Table**
Still in the Beeline CLI, create a simple table:
```sql
CREATE TABLE employees (
    id INT,
    name STRING,
    age INT
);
```
- This creates a table named `employees` with columns `id`, `name`, and `age`.

---

##### **📥 Step 3: Insert Data into the Table**
Insert sample data into your table:
```sql
INSERT INTO employees VALUES (1, 'Prince', 30);
INSERT INTO employees VALUES (2, 'Ram Singh', 25);
```

---

##### **🔍 Step 4: Query the Table**
Retrieve data from your table:
```sql
SELECT * FROM employees;
```
- Output:
  ```
  +----+----------+-----+
  | id |   name   | age |
  +----+----------+-----+
  | 1  | John Doe |  30 |
  | 2  | Jane Smith | 25 |
  +----+----------+-----+
  ```

---

#### **🌟 Tips & Knowledge**

1. **What is Hive?**
   - Hive is a data warehouse tool on top of Hadoop.
   - It allows SQL-like querying over large datasets.

2. **Why Docker for Hive?**
   - Simplifies setup by avoiding manual configurations.
   - Provides a pre-configured environment for running Hive.

3. **Beeline CLI**:
   - A lightweight command-line tool for running Hive queries.

4. **Use Cases**:
   - **Data Analysis**: Run analytics on large datasets.
   - **ETL**: Extract, Transform, and Load data into your Hadoop ecosystem.

---

#### **🎉 You're Ready!**
You’ve successfully:
1. Set up Apache Hive.
2. Created and queried a sample project.  🐝

### **🐝 Apache Hive Basic Commands**

Here is a collection of basic Apache Hive commands with explanations that can help you while working with Hive:

---

#### **1. Database Commands**

- **Show Databases:**
  Displays all the databases available in your Hive environment.
  ```sql
  SHOW DATABASES;
  ```

- **Create a Database:**
  Create a new database.
  ```sql
  CREATE DATABASE <database_name>;
  ```
  Example:
  ```sql
  CREATE DATABASE mydb;
  ```

- **Use a Database:**
  Switch to the specified database.
  ```sql
  USE <database_name>;
  ```
  Example:
  ```sql
  USE mydb;
  ```

- **Drop a Database:**
  Deletes a database and its associated data.
  ```sql
  DROP DATABASE <database_name>;
  ```

---

#### **2. Table Commands**

- **Show Tables:**
  List all the tables in the current database.
  ```sql
  SHOW TABLES;
  ```

- **Create a Table:**
  Define a new table with specific columns.
  ```sql
  CREATE TABLE <table_name> (
      column_name column_type,
      ...
  );
  ```
  Example:
  ```sql
  CREATE TABLE employees (
      id INT,
      name STRING,
      age INT
  );
  ```

- **Describe a Table:**
  Get detailed information about a table, including column names and types.
  ```sql
  DESCRIBE <table_name>;
  ```

- **Drop a Table:**
  Deletes a table and its associated data.
  ```sql
  DROP TABLE <table_name>;
  ```

- **Alter a Table:**
  Modify a table structure, like adding new columns.
  ```sql
  ALTER TABLE <table_name> ADD COLUMNS (<new_column> <type>);
  ```
  Example:
  ```sql
  ALTER TABLE employees ADD COLUMNS (salary DOUBLE);
  ```

---

#### **3. Data Manipulation Commands**

- **Insert Data:**
  Insert data into a table.
  ```sql
  INSERT INTO <table_name> VALUES (<value1>, <value2>, ...);
  ```
  Example:
  ```sql
  INSERT INTO employees VALUES (1, 'John Doe', 30);
  ```

- **Select Data:**
  Retrieve data from a table.
  ```sql
  SELECT * FROM <table_name>;
  ```

- **Update Data:**
  Update existing data in a table.
  ```sql
  UPDATE <table_name> SET <column_name> = <new_value> WHERE <condition>;
  ```

- **Delete Data:**
  Delete rows from a table based on a condition.
  ```sql
  DELETE FROM <table_name> WHERE <condition>;
  ```

---

#### **4. Querying Commands**

- **Select Specific Columns:**
  Retrieve specific columns from a table.
  ```sql
  SELECT <column1>, <column2> FROM <table_name>;
  ```

- **Filtering Data:**
  Filter data based on conditions using the `WHERE` clause.
  ```sql
  SELECT * FROM <table_name> WHERE <column_name> <operator> <value>;
  ```
  Example:
  ```sql
  SELECT * FROM employees WHERE age > 25;
  ```

- **Sorting Data:**
  Sort the result by a column in ascending or descending order.
  ```sql
  SELECT * FROM <table_name> ORDER BY <column_name> ASC|DESC;
  ```
  Example:
  ```sql
  SELECT * FROM employees ORDER BY age DESC;
  ```

- **Group By:**
  Group data by one or more columns and aggregate it using functions like `COUNT`, `AVG`, `SUM`, etc.
  ```sql
  SELECT <column_name>, COUNT(*) FROM <table_name> GROUP BY <column_name>;
  ```
  Example:
  ```sql
  SELECT age, COUNT(*) FROM employees GROUP BY age;
  ```

---

#### **5. File Format Commands**

- **Create External Table:**
  Create a table that references data stored externally (e.g., in HDFS).
  ```sql
  CREATE EXTERNAL TABLE <table_name> (<column_name> <data_type>, ...) 
  ROW FORMAT DELIMITED
  FIELDS TERMINATED BY '<delimiter>'
  LOCATION '<file_path>';
  ```
  Example:
  ```sql
  CREATE EXTERNAL TABLE employees (
      id INT,
      name STRING,
      age INT
  ) ROW FORMAT DELIMITED
  FIELDS TERMINATED BY ','
  LOCATION '/user/hive/warehouse/employees';
  ```

- **Load Data into Table:**
  Load data from a file into an existing Hive table.
  ```sql
  LOAD DATA LOCAL INPATH '<file_path>' INTO TABLE <table_name>;
  ```

---

#### **6. Other Useful Commands**

- **Show Current User:**
  Display the current user running the Hive session.
  ```sql
  !whoami;
  ```

- **Exit Hive:**
  Exit from the Hive shell.
  ```sql
  EXIT;
  ```

- **Set Hive Variables:**
  Set Hive session variables.
  ```sql
  SET <variable_name>=<value>;
  ```

- **Show Hive Variables:**
  Display all the set variables.
  ```sql
  SET;
  ```

- **Show the Status of Hive Jobs:**
  Display the status of running queries.
  ```sql
  SHOW JOBS;
  ```

---

#### **🌟 Tips & Best Practices**

- **Partitioning Tables:**
  When dealing with large datasets, partitioning your tables can help improve query performance.
  ```sql
  CREATE TABLE sales (id INT, amount DOUBLE)
  PARTITIONED BY (year INT, month INT);
  ```

- **Bucketing:**
  Bucketing splits your data into a fixed number of files or "buckets."
  ```sql
  CREATE TABLE sales (id INT, amount DOUBLE)
  CLUSTERED BY (id) INTO 4 BUCKETS;
  ```

- **Optimization:**
  Use columnar formats like `ORC` or `Parquet` for efficient storage and performance.
  ```sql
  CREATE TABLE sales (id INT, amount DOUBLE)
  STORED AS ORC;
  ```

These basic commands will help you interact with Hive and perform common operations like creating tables, querying data, and managing your Hive environment efficiently.
