Here’s a well-organized and formatted Markdown document to guide you through installing Hadoop on Ubuntu using Docker, including detailed steps and images.

---

# **Hadoop Installation on Ubuntu Using Docker**

## **Introduction**
Docker is a powerful platform for containerization, enabling lightweight, portable, and isolated environments to run applications. This guide demonstrates how to set up Hadoop on an Ubuntu-based Docker container.

---

## **Step 1: Start a Docker Container**

1. Launch a Docker container with the Ubuntu 20.04 image:
   ```bash
   docker run -it --name hadoop-container ubuntu:20.04 /bin/bash
   ```
   > This creates and starts a container named `hadoop-container`.

---

## **Step 2: Update the Package List**

1. Inside the container, update the package list:
   ```bash
   apt-get update
   ```

---

## **Step 3: Install Java**

1. Install OpenJDK 8, which Hadoop requires:
   ```bash
   apt-get install openjdk-8-jdk -y
   ```

2. Verify the Java installation:
   ```bash
   java -version
   ```
   > Expected output:
   ```
   openjdk version "1.8.0_xxx"
   OpenJDK Runtime Environment (build 1.8.0_xxx)
   OpenJDK 64-Bit Server VM (build xx, mixed mode)
   ```

---

## **Step 4: Install Nano Text Editor**

1. Install the Nano text editor for configuration file editing:
   ```bash
   apt-get install nano -y
   ```

---

## **Step 5: Set JAVA_HOME**

1. Open the `.bashrc` file to add environment variables:
   ```bash
   nano ~/.bashrc
   ```

2. Append the following lines to set `JAVA_HOME`:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   export PATH=$JAVA_HOME/bin:$PATH
   ```

3. Apply the changes:
   ```bash
   source ~/.bashrc
   ```

4. Confirm the `JAVA_HOME` path:
   ```bash
   echo $JAVA_HOME
   ```

---

## **Step 6: Install wget for File Downloads**

1. Install `wget` to download files:
   ```bash
   apt-get install wget -y
   ```

---

## **Step 7: Download and Extract Hadoop**

1. Download Hadoop:
   ```bash
   wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz
   ```

2. Extract the archive to `/opt`:
   ```bash
   tar -xzf hadoop-2.10.2.tar.gz -C /opt
   ```

3. Rename the extracted directory for simplicity:
   ```bash
   mv /opt/hadoop-2.10.2 /opt/hadoop
   ```

---

## **Step 8: Set Hadoop Environment Variables**

1. Edit `.bashrc` to add Hadoop variables:
   ```bash
   nano ~/.bashrc
   ```

2. Add the following lines:
   ```bash
   export HADOOP_HOME=/opt/hadoop
   export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
   ```

3. Apply the changes:
   ```bash
   source ~/.bashrc
   ```

4. Verify the Hadoop version:
   ```bash
   hadoop version
   ```

---

## **Step 9: Configure Hadoop**

### **1. Configure `core-site.xml`**
1. Open `core-site.xml`:
   ```bash
   nano /opt/hadoop/etc/hadoop/core-site.xml
   ```

2. Add the following:
   ```xml
   <configuration>
       <property>
           <name>fs.defaultFS</name>
           <value>hdfs://localhost:9000</value>
       </property>
   </configuration>
   ```

---

### **2. Configure `hdfs-site.xml`**
1. Open `hdfs-site.xml`:
   ```bash
   nano /opt/hadoop/etc/hadoop/hdfs-site.xml
   ```

2. Add the following:
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

### **3. Set JAVA_HOME in `hadoop-env.sh`**
1. Open `hadoop-env.sh`:
   ```bash
   nano /opt/hadoop/etc/hadoop/hadoop-env.sh
   ```

2. Add the following line:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   ```

---

## **Step 10: Format HDFS**

1. Format the NameNode:
   ```bash
   hdfs namenode -format
   ```

---

## **Step 11: Start Hadoop Services**

### **1. Start HDFS**
1. Start NameNode and DataNode:
   ```bash
   start-dfs.sh
   ```

2. Verify services:
   ```bash
   jps
   ```
   > Expected output:
   ```
   NameNode
   DataNode
   SecondaryNameNode
   ```

---

### **2. Start YARN**
1. Start ResourceManager and NodeManager:
   ```bash
   start-yarn.sh
   ```

2. Verify services:
   ```bash
   jps
   ```
   > Expected output:
   ```
   ResourceManager
   NodeManager
   ```

---

## **Step 12: Verify Setup**

### **1. Access Hadoop Web UI**
- **NameNode UI:**  
  [http://localhost:9870](http://localhost:9870)  
  Displays the HDFS overview.
  
- **ResourceManager UI:**  
  [http://localhost:8088](http://localhost:8088)  
  Displays YARN resource management.

---

### **2. Test HDFS**
1. Create a directory:
   ```bash
   hdfs dfs -mkdir /test
   ```

2. List HDFS root directory:
   ```bash
   hdfs dfs -ls /
   ```

---

## **Step 13: Stop Hadoop Services**

1. Stop HDFS:
   ```bash
   stop-dfs.sh
   ```

2. Stop YARN:
   ```bash
   stop-yarn.sh
   ```

---

With these steps completed, you now have Hadoop running in a Docker container on Ubuntu! 🎉
