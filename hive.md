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
2. This will download the Hive Docker image to your system.  
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
INSERT INTO employees VALUES (1, 'John Doe', 30);
INSERT INTO employees VALUES (2, 'Jane Smith', 25);
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
