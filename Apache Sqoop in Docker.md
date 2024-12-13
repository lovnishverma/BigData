Great! Now that you've installed Java and Sqoop is working, here’s a step-by-step guide to set up Sqoop in Docker and a small basic project using Sqoop for data transfer from a MySQL database to Hadoop.

### Sqoop Setup in Docker

```markdown
# Sqoop Setup in Docker

This guide helps you to set up Sqoop inside a Docker container and use it to transfer data between a MySQL database and Hadoop.

## Prerequisites

1. **Docker** should be installed and running on your system.
2. **Java 8** should be installed inside the Docker container.
3. **Sqoop 1.99.6** should be downloaded and placed inside the Docker container.
4. A **Hadoop** setup (local or remote) should be accessible.
5. A **MySQL** instance should be running and accessible.

## Steps

### Step 1: Create a Dockerfile
Create a Dockerfile to set up the environment with Java, Sqoop, and Hadoop.

```Dockerfile
FROM ubuntu:20.04

# Install dependencies
RUN apt-get update && apt-get install -y \
    openjdk-8-jdk \
    wget \
    mysql-client \
    curl \
    && apt-get clean

# Set JAVA_HOME
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64
ENV PATH $JAVA_HOME/bin:$PATH

# Install Sqoop
RUN wget https://archive.apache.org/dist/sqoop/1.99.6/sqoop-1.99.6-bin-hadoop200.tar.gz -P /tmp \
    && tar -xzf /tmp/sqoop-1.99.6-bin-hadoop200.tar.gz -C /opt \
    && rm /tmp/sqoop-1.99.6-bin-hadoop200.tar.gz

# Set SQOOP_HOME and PATH
ENV SQOOP_HOME /opt/sqoop-1.99.6-bin-hadoop200
ENV PATH $SQOOP_HOME/bin:$PATH

# Install Hadoop binaries (if needed)
# RUN wget <HADOOP_URL> -P /opt && tar -xzf /opt/hadoop.tar.gz -C /opt
# ENV HADOOP_HOME /opt/hadoop
# ENV PATH $HADOOP_HOME/bin:$PATH

CMD ["bash"]
```

### Step 2: Build and Run Docker Container

1. Build the Docker image:

    ```bash
    docker build -t sqoop-docker .
    ```

2. Run the Docker container:

    ```bash
    docker run -it sqoop-docker
    ```

This will start a container with Java, Sqoop, and other dependencies installed.

### Step 3: Configure Sqoop for MySQL and Hadoop

1. **MySQL Configuration:**

   - Make sure you have MySQL running.
   - Create a database and table in MySQL.
   
   Example:

   ```sql
   CREATE DATABASE sqoop_db;
   USE sqoop_db;

   CREATE TABLE employee (
       id INT PRIMARY KEY,
       name VARCHAR(100),
       age INT,
       salary INT
   );

   INSERT INTO employee (id, name, age, salary) VALUES
   (1, 'Alice', 30, 50000),
   (2, 'Bob', 25, 40000),
   (3, 'Charlie', 35, 60000);
   ```

2. **Hadoop Configuration:**
   - Ensure that Hadoop is properly set up and running. You can use the `HDFS` and `YARN` components for distributed storage and execution.

3. **Sqoop Configuration:**

   Sqoop should now be ready for data transfer. Set up the connection to your MySQL and Hadoop environment.

### Step 4: Basic Sqoop Project - Data Transfer

In this example, we will transfer data from MySQL to Hadoop.

#### Step 4.1: Export Data from MySQL to HDFS

Run the following command to export data from MySQL into a file in HDFS:

```bash
sqoop export \
    --connect jdbc:mysql://<MYSQL_HOST>:<MYSQL_PORT>/sqoop_db \
    --username <MYSQL_USER> \
    --password <MYSQL_PASSWORD> \
    --table employee \
    --export-dir /user/hadoop/sqoop_data \
    --input-fields-terminated-by ',' \
    --hadoop-home /opt/hadoop
```

This will export the `employee` table into a file on HDFS.

#### Step 4.2: Import Data from MySQL to HDFS

To import data from MySQL to HDFS:

```bash
sqoop import \
    --connect jdbc:mysql://<MYSQL_HOST>:<MYSQL_PORT>/sqoop_db \
    --username <MYSQL_USER> \
    --password <MYSQL_PASSWORD> \
    --table employee \
    --target-dir /user/hadoop/sqoop_data \
    --fields-terminated-by ',' \
    --hadoop-home /opt/hadoop
```

This will import the `employee` table into HDFS, storing the data in the `/user/hadoop/sqoop_data` directory.

### Step 5: Verify the Data in HDFS

To verify the imported data, run the following command:

```bash
hdfs dfs -cat /user/hadoop/sqoop_data/*
```

### Step 6: Clean Up

To clean up the resources:

```bash
hdfs dfs -rm -r /user/hadoop/sqoop_data
```

## Conclusion

You have successfully set up Sqoop in Docker and transferred data between MySQL and Hadoop. This setup can be extended for more complex data integration workflows using Sqoop.
```

### Key Steps Recap:
1. Install Java and dependencies inside Docker.
2. Download and set up Sqoop inside Docker.
3. Set up a MySQL database and Hadoop environment.
4. Use Sqoop commands to import/export data between MySQL and HDFS.

This should help you get started with Sqoop in Docker! Let me know if you need any further details or have more questions.
