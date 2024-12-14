# **Installing Hadoop on Ubuntu Using Docker**
![image](https://github.com/user-attachments/assets/a78badcf-4181-4379-aea2-8922855bfc0a) ![image](https://github.com/user-attachments/assets/0180422c-3395-4e93-a5ce-e4291820f9e5) ![image](https://github.com/user-attachments/assets/c37d59ff-7b5f-43e0-aeb8-cc3f5700dfca)
![image](https://github.com/user-attachments/assets/c282459d-ef45-4589-b452-9424561d5ba3) ![image](https://github.com/user-attachments/assets/d2177214-7746-41ba-988d-d1653b37bc87)




## **Introduction**
Docker provides a convenient way to create isolated environments for applications. This guide walks you through the installation and setup of Hadoop on Ubuntu inside a Docker container.

---

## **Preliminary Steps**

### **Check Existing Containers**
1. List all containers (including stopped ones):
   ```bash
   docker ps -a
   ```
   Look for a container named `hadoop-container`.

2. **Restart an Existing Container**  
   If the container is stopped:
   ```bash
   docker start hadoop-container
   docker exec -it hadoop-container /bin/bash
   ```

3. **Remove an Old Container**  
   To remove an existing container:
   ```bash
   docker rm hadoop-container
   ```
   Then create a new one:
   ```bash
   docker run -it -p 9870:9870 -p 8088:8088 --name hadoop-container ubuntu:20.04 /bin/bash
   ```

4. **Create a Container with a Different Name**  
   If you want a new container without deleting the old one:
   ```bash
   docker run -it --name hadoop-container-2 ubuntu:20.04 /bin/bash
   ```

---

## **Step 1: Run a Docker Container with exposed ports**
1. Start a Docker container using the `ubuntu:20.04` image:
   ```bash
   docker run -it -p 9870:9870 -p 8088:8088 --name hadoop-container ubuntu:20.04 /bin/bash
   ```
![Docker Run Command](https://github.com/user-attachments/assets/75ff0e66-d30a-430e-b869-3efe99a6a1a2)
---

## **Step 2: Update the Package List**
Update the package list inside the container:
```bash
apt-get update
```
![Update Packages](https://github.com/user-attachments/assets/d6e32909-7ce1-446c-a3bc-082b7d0187c0)

---

## **Step 3: Install Java**
1. Install OpenJDK 8:
   ```bash
   apt-get install openjdk-8-jdk -y
   ```
   ![Install OpenJDK 8](https://github.com/user-attachments/assets/4ecad1fd-d9db-4ba5-bd75-a438346fc77f)

2. Verify the installation:
   ```bash
   java -version
   ```
   ![Verify Java Version](https://github.com/user-attachments/assets/475e6b03-ae1a-4345-9be2-a62dac0e632a)

---

## **Step 4: Install a Text Editor**
Install the Nano text editor for editing configuration files:
```bash
apt-get install nano -y
```
![Install Nano](https://github.com/user-attachments/assets/78319fa9-9365-4b41-94de-a8959ce4fe12)

---

## **Step 5: Set JAVA_HOME**
1. Open `.bashrc` to add environment variables:
   ```bash
   nano ~/.bashrc
   ```
   Add these lines:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   export PATH=$JAVA_HOME/bin:$PATH
   ```
   ![Edit Bashrc for Java](https://github.com/user-attachments/assets/fc07e050-4484-4a2c-ae4b-d64598aa2a9f)

2. Apply the changes:
   ```bash
   source ~/.bashrc
   ```
   ![Apply Changes](https://github.com/user-attachments/assets/16cc68ab-d5e3-4005-8831-9d2b1f2f270b)

---

## **Step 6: Install wget**
Install `wget` for downloading files:
```bash
apt-get install wget -y
```
![Install Wget](https://github.com/user-attachments/assets/3e076cbc-74d7-461f-be96-54521057aaf9)

---

## **Step 7: Download and Extract Hadoop**
1. Download Hadoop:
   ```bash
   wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz
   ```

2. Extract and move to `/opt`:
   ```bash
   tar -xzf hadoop-2.10.2.tar.gz -C /opt
   mv /opt/hadoop-2.10.2 /opt/hadoop
   ```
   ![Download and Extract Hadoop](https://github.com/user-attachments/assets/63396bcc-03a6-42dd-8987-eadc566beb15)

---

## **Step 8: Configure Hadoop**

### **1. Configure core-site.xml**
1. Edit `core-site.xml`:
   ```bash
   nano /opt/hadoop/etc/hadoop/core-site.xml
   ```
2. Add:
   ```xml
   <configuration>
       <property>
           <name>fs.defaultFS</name>
           <value>hdfs://localhost:9000</value>
       </property>
   </configuration>
   ```
   ![Edit core-site.xml](https://github.com/user-attachments/assets/c8579cd5-708f-4398-bc5b-f683c6df5356)

---

### **2. Configure hdfs-site.xml**
1. Edit `hdfs-site.xml`:
   ```bash
   nano /opt/hadoop/etc/hadoop/hdfs-site.xml
   ```
2. Add:
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
   ![Edit hdfs-site.xml](https://github.com/user-attachments/assets/5cd73d3d-2d59-479e-b7a2-19a20eb52ee8)

### **3. Configure yarn-site.xml**
2. Edit `yarn-site.xml`:
   ```bash
   nano /opt/hadoop/etc/hadoop/yarn-site.xml
   ```
2. Add:
   ```xml
   <configuration>
   <property>
  <name>yarn.resourcemanager.address</name>
  <value>0.0.0.0:8088</value>
  </property>
<property>
<name>yarn.resourcemanager.webapp.address</name>
<value>0.0.0.0:8088</value>
</property>
</configuration>
```

![image](https://github.com/user-attachments/assets/c0b47340-47a6-4dd0-93b8-a8dceab780e9)

---

### **4. Set JAVA_HOME in Hadoop**
1. Edit `hadoop-env.sh`:
   ```bash
   nano /opt/hadoop/etc/hadoop/hadoop-env.sh
   ```
2. Add:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   ```
   ![Edit hadoop-env.sh](https://github.com/user-attachments/assets/6ca75959-ee7f-422d-8dd3-52ad1f363121)

---

## **Step 9: Format HDFS**
1. Format the NameNode:
   ```bash
   hdfs namenode -format
   ```
   Example Output:
   ![Format HDFS](https://github.com/user-attachments/assets/f530cb29-9040-47cd-b8ba-4d7bbc6c5b00)

---

## **Step 11: Install OpenSSH Client**
```bash
apt-get update
apt-get install openssh-client -y
```
![image](https://github.com/user-attachments/assets/45140d6e-844d-43bb-a6c2-9ead988a9b03)

## **Step 12: Start Namenode and Datanode**

Start the NameNode:
```bash
hadoop-daemon.sh start namenode
```

Start the DataNode:
```bash
hadoop-daemon.sh start datanode
```

## **Step 13: Start Hadoop Services**

### **1. Start HDFS**
```bash
start-dfs.sh
```
Check services:
```bash
jps
```

### **2. Start YARN**
```bash
start-yarn.sh
```
Verify with:
```bash
jps
```
![image](https://github.com/user-attachments/assets/1e8afc9b-3ac7-49ef-a342-6be253bea0d1)

---

## **Verification**
1. Access the Hadoop Web UIs:
   - **HDFS**: [http://localhost:9870](http://localhost:9870)
   - **YARN**: [http://localhost:8088](http://localhost:8088)

2. Test HDFS:
   ```bash
   hdfs dfs -mkdir /test
   hdfs dfs -ls /
   ```

---

## **Stopping Hadoop Services**
To stop services:
```bash
stop-dfs.sh
stop-yarn.sh
```
**With these steps completed, you now have Hadoop running in a Docker container on Ubuntu!**
