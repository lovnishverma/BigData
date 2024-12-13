Here is a complete step-by-step guide to install and configure Sqoop in a Docker container to work with Hadoop, also running in a separate Docker container. This includes setting up the environment, installing Sqoop, connecting to Hadoop, and running Sqoop jobs.

---

### **Step 1: Pull and Run Hadoop Docker Image**

1. **Pull a Hadoop Docker image:**
   ```bash
   docker pull bde2020/hadoop-namenode:latest
   ```

2. **Run the Hadoop container:**
   ```bash
   docker run -itd --name hadoop-container \
     -p 50070:50070 -p 8088:8088 \
     bde2020/hadoop-namenode:latest bash
   ```

3. **Verify Hadoop is running:**
   Inside the container:
   ```bash
   hadoop version
   ```

---

### **Step 2: Pull and Run Sqoop Docker Image**

1. **Pull a base Ubuntu Docker image for Sqoop:**
   ```bash
   docker pull ubuntu:20.04
   ```

2. **Run the Sqoop container:**
   ```bash
   docker run -itd --name sqoop-container \
     --network hadoop-network \
     ubuntu:20.04 bash
   ```

3. **Install required packages in the Sqoop container:**
   ```bash
   docker exec -it sqoop-container bash
   apt update
   apt install -y openjdk-8-jdk wget tar net-tools
   ```

4. **Set JAVA_HOME in the container:**
   ```bash
   echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> ~/.bashrc
   echo "export PATH=$JAVA_HOME/bin:$PATH" >> ~/.bashrc
   source ~/.bashrc
   ```

---

### **Step 3: Download and Install Sqoop**

1. **Download the Sqoop binary distribution:**
   ```bash
   wget https://archive.apache.org/dist/sqoop/1.99.6/sqoop-1.99.6-bin-hadoop200.tar.gz
   ```

2. **Copy Sqoop to the container:**
   On the host machine:
   ```bash
   docker cp sqoop-1.99.6-bin-hadoop200.tar.gz sqoop-container:/tmp/
   ```

3. **Extract and set up Sqoop:**
   Inside the Sqoop container:
   ```bash
   tar -xvzf /tmp/sqoop-1.99.6-bin-hadoop200.tar.gz -C /opt/
   mv /opt/sqoop-1.99.6-bin-hadoop200 /opt/sqoop
   echo "export SQOOP_HOME=/opt/sqoop" >> ~/.bashrc
   echo "export PATH=$SQOOP_HOME/bin:$PATH" >> ~/.bashrc
   source ~/.bashrc
   ```

4. **Verify Sqoop installation:**
   ```bash
   sqoop version
   ```

---

### **Step 4: Configure Sqoop to Connect to Hadoop**

1. **Copy Hadoop configuration files to the Sqoop container:**
   From the Hadoop container:
   ```bash
   docker cp hadoop-container:/path/to/hadoop-config-files sqoop-container:/opt/hadoop/etc/hadoop
   ```

   Ensure `core-site.xml` and `hdfs-site.xml` are included.

2. **Set Hadoop environment variables in Sqoop container:**
   ```bash
   export HADOOP_HOME=/opt/hadoop
   export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
   echo "export HADOOP_HOME=/opt/hadoop" >> ~/.bashrc
   echo "export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop" >> ~/.bashrc
   source ~/.bashrc
   ```

3. **Verify Hadoop connectivity:**
   Test HDFS access:
   ```bash
   hadoop fs -ls /
   ```

---

### **Step 5: Install and Configure a Database**

1. **Run a MySQL container:**
   ```bash
   docker run -itd --name mysql-container \
     -e MYSQL_ROOT_PASSWORD=root \
     -e MYSQL_DATABASE=testdb \
     -p 3306:3306 \
     mysql:5.7
   ```

2. **Connect to MySQL:**
   Inside the MySQL container:
   ```bash
   mysql -u root -p
   ```

   Create a test table:
   ```sql
   CREATE TABLE test_table (id INT PRIMARY KEY, name VARCHAR(50));
   INSERT INTO test_table VALUES (1, 'Sqoop Test');
   ```

3. **Download MySQL JDBC driver:**
   Inside the Sqoop container:
   ```bash
   wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.33.tar.gz
   tar -xvzf mysql-connector-java-8.0.33.tar.gz
   cp mysql-connector-java-8.0.33/mysql-connector-java-8.0.33.jar /opt/sqoop/lib/
   ```

---

### **Step 6: Run a Sqoop Job**

1. **Test database connectivity:**
   ```bash
   sqoop list-databases --connect jdbc:mysql://mysql-container:3306/ \
     --username root --password root
   ```

2. **Import data from MySQL to HDFS:**
   ```bash
   sqoop import \
     --connect jdbc:mysql://mysql-container:3306/testdb \
     --username root --password root \
     --table test_table \
     --target-dir /user/hadoop/test_table
   ```

3. **Export data from HDFS to MySQL:**
   ```bash
   sqoop export \
     --connect jdbc:mysql://mysql-container:3306/testdb \
     --username root --password root \
     --table test_table \
     --export-dir /user/hadoop/test_table
   ```

---

### **Step 7: Troubleshooting**

- **Check Network Connectivity:** Ensure all containers are on the same network.
  ```bash
  docker network inspect hadoop-network
  ```
  
- **Environment Variables:** Ensure all paths (`JAVA_HOME`, `SQOOP_HOME`, `HADOOP_HOME`) are correctly set.
  
- **Log Files:** Review logs for Sqoop and Hadoop for errors.

---
