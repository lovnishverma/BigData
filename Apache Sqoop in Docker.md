### **Complete Guide to Install and Configure Sqoop, Hadoop, MySQL, and Java on Docker**

This guide will help you set up **Sqoop**, **Hadoop**, **MySQL**, and **Java** in a fresh Docker container. Follow each step to get everything working smoothly.

---

## **Step 1: Set up a Fresh Docker Container**

1. **Create and Start a New Ubuntu Container:**
   ```bash
   docker run -it --name sqoop-container ubuntu:20.04 /bin/bash
   ```

2. **Update Package Lists in the Container:**
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
   You should see Java 8 installed.

3. **Set `JAVA_HOME`:**
   Open `.bashrc` to add environment variables:
   ```bash
   apt-get install nano -y
   nano ~/.bashrc
   ```

   Add these lines to the end of the file:
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
   Access the MySQL shell:
   ```bash
   mysql -u root
   ```
   Run these commands in the MySQL shell:
   ```sql
   CREATE DATABASE testdb;
   CREATE USER 'sqoop_user'@'%' IDENTIFIED BY 'password123';
   GRANT ALL PRIVILEGES ON testdb.* TO 'sqoop_user'@'%';
   FLUSH PRIVILEGES;
   EXIT;
   ```

---

## **Step 4: Install Hadoop**

1. **Install `wget` to Download Hadoop:**
   ```bash
   apt-get install wget -y
   ```

2. **Download Hadoop 2.10.2:**
   ```bash
   wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz
   ```

3. **Extract and Move Hadoop:**
   ```bash
   tar -xzf hadoop-2.10.2.tar.gz -C /opt
   mv /opt/hadoop-2.10.2 /opt/hadoop
   ```

4. **Set Hadoop Environment Variables:**
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

5. **Verify Hadoop Installation:**
   ```bash
   hadoop version
   ```

---

## **Step 5: Configure Hadoop**

1. **Edit `core-site.xml`:**
   Open the file:
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
   Add this configuration:
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

---

### **Step 6: Install SSH Utilities for Hadoop**

Hadoop requires SSH for managing nodes, even for single-node setups.

1. **Install SSH in the Docker container:**
   ```bash
   apt-get update
   apt-get install -y openssh-client openssh-server
   ```

2. **Start the SSH service:**
   ```bash
   service ssh start
   ```

3. **Verify SSH installation:**
   ```bash
   ssh localhost
   ```

   If prompted to generate an RSA key or accept a host fingerprint, follow the instructions.

---

### **Step 7: Fix Hadoop Logs and Ownership**

1. **Create the logs directory:**
   ```bash
   mkdir -p /opt/hadoop/logs
   ```

2. **Set the correct ownership:**
   ```bash
   chown -R $(whoami):$(whoami) /opt/hadoop/logs
   chmod -R 755 /opt/hadoop/logs
   ```

---

### **Step 8: Set `JAVA_HOME` in Hadoop**

1. **Edit `hadoop-env.sh`:**
   Open the Hadoop environment file:
   ```bash
   nano /opt/hadoop/etc/hadoop/hadoop-env.sh
   ```

2. **Set the `JAVA_HOME` variable:**
   Find the line (or add it if it doesn't exist):
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   ```

3. **Apply the changes:**
   ```bash
   source /opt/hadoop/etc/hadoop/hadoop-env.sh
   ```

---

### **Step 9: Start Hadoop**

1. **Format the Namenode (optional for fresh setup):**
   ```bash
   hdfs namenode -format
   ```

2. **Start Hadoop services:**
   ```bash
   start-dfs.sh
   start-yarn.sh
   ```

---

## **Step 10: Install Sqoop**

1. **Download and Extract Sqoop:**
   Download Sqoop:
   [Sqoop 1.99.6](https://archive.apache.org/dist/sqoop/1.99.6/sqoop-1.99.6-bin-hadoop200.tar.gz)

   Transfer it to the container:
   ```bash
   docker cp sqoop-1.99.6-bin-hadoop200.tar.gz sqoop-container:/tmp
   ```

   Inside the container, extract and move Sqoop:
   ```bash
   tar -xvzf /tmp/sqoop-1.99.6-bin-hadoop200.tar.gz -C /opt
   mv /opt/sqoop-1.99.6-bin-hadoop200 /opt/sqoop
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

---

## **Step 11: Test Sqoop with MySQL**

1. **Connect Sqoop to MySQL:**
   Use Sqoop to list MySQL databases:
   ```bash
   sqoop list-databases \
       --connect jdbc:mysql://localhost:3306 \
       --username sqoop_user --password password123
   ```

   Replace `password123` with your MySQL user password.

2. **Expected Output:**
   If everything is configured correctly, you should see a list of MySQL databases, including `testdb`.

---

### **Congratulations!**

You’ve successfully installed and configured **Sqoop**, **Hadoop**, **MySQL**, and **Java** in your Docker container. You can now use **Sqoop** to transfer data between **MySQL** and **Hadoop**.

If you face any issues, feel free to ask!
