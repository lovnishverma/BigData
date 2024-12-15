Here’s a streamlined guide reflecting the steps for setting up **Sqoop**, **Hadoop**, **MySQL**, and **Java** on Docker:  

---

# **Complete Guide to Install and Configure Sqoop, Hadoop, MySQL, and Java on Docker**

This guide outlines the steps to install and configure **Sqoop**, **Hadoop**, **MySQL**, and **Java** in a Docker container for big data operations.

1. **Check Existing Containers**  
   List all containers (including stopped ones) to confirm the presence of `sqoop-container`:
   ```bash
   docker ps -a
   ```

   Look for the container named `sqoop-container` in the output.

2. **Restart the Existing Container (If You Want to Reuse It)**  
   If the container `sqoop-container` is stopped and you want to use it, restart it:
   ```bash
   docker start sqoop-container
   ```

   Then, attach to it using:
   ```bash
   docker exec -it sqoop-container /bin/bash
   ```

3. **Remove the Existing Container (If You Want to Create a New One)**  
   If you no longer need the existing `sqoop-container`, remove it:
   ```bash
   docker rm sqoop-container
   ```

   After removing it, you can create a new container with the same name:
   ```bash
   docker run -it --name sqoop-container ubuntu:20.04 /bin/bash
   ```

4. **Use a Different Container Name**  
   If you don’t want to delete the existing container, create a new one with a different name:
   ```bash
   docker run -it --name sqoop-container-2 ubuntu:20.04 /bin/bash
   ```

### Additional Notes
- **Check Container Logs**  
   If the container failed previously, check its logs to identify issues:
   ```bash
   docker logs sqoop-container
   ```

- **Inspect Container Details**  
   To investigate the container further:
   ```bash
   docker inspect sqoop-container
   ``` 

## **Step 1: Set up a Fresh Docker Container**

1. **Create and Start a New Ubuntu Container:**
   ```bash
   docker run -it --name sqoop-container ubuntu:20.04 /bin/bash
   ```

2. **Update Package Lists:**
   ```bash
   apt-get update
   ```

---

## **Step 2: Install Java (OpenJDK 8)**

1. **Install OpenJDK 8:**
   ```bash
   apt-get install openjdk-8-jdk -y
   ```

2. **Verify Java Installation:**
   ```bash
   java -version
   ```

3. **Set `JAVA_HOME` Environment Variable:**
   Open `.bashrc`:
   ```bash
   apt-get install nano -y
   nano ~/.bashrc
   ```

   Add these lines:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   export PATH=$JAVA_HOME/bin:$PATH
   ```

4. **Apply the Changes:**
   ```bash
   source ~/.bashrc
   ```

---

## **Step 3: Install MySQL**

1. **Install MySQL Server:**
   ```bash
   apt-get install mysql-server -y
   ```

2. **Start MySQL Service:**
   ```bash
   service mysql start
   ```

3. **Create a Test Database and User:**
   Log into MySQL:
   ```bash
   mysql -u root
   ```
   Run the following SQL commands:
   ```sql
   CREATE DATABASE testdb;
   CREATE USER 'sqoop_user'@'%' IDENTIFIED BY 'password123';
   GRANT ALL PRIVILEGES ON testdb.* TO 'sqoop_user'@'%';
   FLUSH PRIVILEGES;
   EXIT;
   ```

---

## **Step 4: Install Hadoop**

1. **Install `wget` for File Downloads:**
   ```bash
   apt-get install wget -y
   ```

2. **Download and Extract Hadoop:**
   ```bash
   wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz
   tar -xzf hadoop-2.10.2.tar.gz -C /opt
   mv /opt/hadoop-2.10.2 /opt/hadoop
   ```

3. **Set Hadoop Environment Variables:**
   Open `.bashrc`:
   ```bash
   nano ~/.bashrc
   ```
   Add these lines:
   ```bash
   export HADOOP_HOME=/opt/hadoop
   export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
   ```

   Apply changes:
   ```bash
   source ~/.bashrc
   ```

4. **Verify Hadoop Installation:**
   ```bash
   hadoop version
   ```

---

## **Step 5: Configure Hadoop**

1. **Edit `core-site.xml`:**
   ```bash
   nano /opt/hadoop/etc/hadoop/core-site.xml
   ```
   Add the following configuration:
   ```xml
   <configuration>
       <property>
           <name>fs.defaultFS</name>
           <value>hdfs://localhost:9000</value>
       </property>
   </configuration>
   ```

2. **Edit `hdfs-site.xml`:**
   ```bash
   nano /opt/hadoop/etc/hadoop/hdfs-site.xml
   ```
   Add the following:
   ```xml
   <configuration>
       <property>
           <name>dfs.replication</name>
           <value>1</value>
       </property>
       <property>
           <name>dfs.namenode.name.dir</name>
           <value>file:///opt/hadoop/hdfs/namenode</value>
       </property>
       <property>
           <name>dfs.datanode.data.dir</name>
           <value>file:///opt/hadoop/hdfs/datanode</value>
       </property>
   </configuration>
   ```

3. **Set `JAVA_HOME` in Hadoop:**
   Edit `hadoop-env.sh`:
   ```bash
   nano /opt/hadoop/etc/hadoop/hadoop-env.sh
   ```
   Add the line:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   ```

---

## **Step 6: Install Sqoop**

1. **Download and Extract Sqoop:**
   ```bash
   wget https://archive.apache.org/dist/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
   tar -xzf sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz -C /opt
   mv /opt/sqoop-1.4.7.bin__hadoop-2.6.0 /opt/sqoop
   ```

2. **Set Sqoop Environment Variables:**
   Open `.bashrc`:
   ```bash
   nano ~/.bashrc
   ```
   Add these lines:
   ```bash
   export SQOOP_HOME=/opt/sqoop
   export PATH=$SQOOP_HOME/bin:$PATH
   ```

   Apply changes:
   ```bash
   source ~/.bashrc
   ```

3. **Verify Sqoop Installation:**
   ```bash
   sqoop version
   ```
![image](https://github.com/user-attachments/assets/34ca8bd3-048a-4a9a-b76a-fbdb2f400f60)

---



To download the MySQL JDBC driver using `wget`, you can follow these steps:

### **Step 7: Download the MySQL JDBC Driver**
You can download the MySQL JDBC driver `.jar` file from the official MySQL website or from a Maven repository.

#### From MySQL's Official Site:
- Find the MySQL Connector/J download page: [MySQL Connector/J](https://dev.mysql.com/downloads/connector/j/)
- Direct download URL (for version 8.0.29 as an example):
  ```bash
  wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.29.tar.gz
  ```

#### From Maven Repository:
Alternatively, you can download it from Maven Central:
- Visit [MySQL Connector/J at Maven Central](https://mvnrepository.com/artifact/mysql/mysql-connector-java)
- Find the latest version, and use the direct download URL. For example, for version `5.1.49`:
  ```bash
  wget https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.49/mysql-connector-java-5.1.49.jar
  ```

### 2. **Extract the JAR File**
If you downloaded a `.tar.gz` archive (from the MySQL website), you need to extract the `.jar` file:
```bash
tar -xvzf mysql-connector-java-8.0.29.tar.gz
ls
cd mysql-connector-java-8.0.29
```
This will extract the `mysql-connector-java-x.x.x.jar` file from the archive.

### 3. **Move the JAR File to Sqoop's lib Directory**
Once you have the `.jar` file, move it to Sqoop’s `lib` directory:
```bash
cp mysql-connector-java-5.1.49.jar /opt/sqoop/lib/
```

### 4. **Verify the Driver in Sqoop**
Now that the JDBC driver is in place, you can proceed with running your Sqoop command again:
```bash
sqoop list-databases --connect jdbc:mysql://localhost:3306 --username sqoop_user --password password123
```

## **Step 8: Test Sqoop with MySQL**

1. **Use Sqoop to List MySQL Databases:**
   ```bash
   sqoop list-databases \
       --connect jdbc:mysql://localhost:3306 \
       --username sqoop_user --password password123
   ```

. **Expected Output:**
   A list of databases should appear, including `testdb`.
   
![image](https://github.com/user-attachments/assets/08d18ae5-c686-4433-af5a-de14bb433803)

---

This concludes the installation and configuration process for **Sqoop**, **Hadoop**, **MySQL**, and **Java** on Docker Container.
