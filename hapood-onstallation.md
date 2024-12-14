Here's a step-by-step guide to installing Hadoop on Ubuntu using Docker.

---

# **Hadoop Installation on Ubuntu Using Docker**

### **Introduction to Docker**
Docker is a platform that allows you to run software in lightweight, isolated containers. Think of containers as small, portable virtual environments that contain everything needed to run an application.

---

### **Step 1: Run a Docker Container**

1. Open your terminal and run the following command to start a Docker container using the `ubuntu:20.04` image:
   ```bash
   docker run -it --name hadoop-container ubuntu:20.04 /bin/bash
   ```
   ![Docker Run Command](https://github.com/user-attachments/assets/75ff0e66-d30a-430e-b869-3efe99a6a1a2)

---

### **Step 2: Update the Package List**

1. Inside the container, update the package list to ensure all software is up-to-date:
   ```bash
   apt-get update
   ```
   ![Update Packages](https://github.com/user-attachments/assets/d6e32909-7ce1-446c-a3bc-082b7d0187c0)

---

### **Step 3: Install Java**

1. Hadoop requires Java. Install OpenJDK 8 by running:
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

### **Step 4: Install a Text Editor (Nano)**

1. Install the Nano text editor, which you’ll use to edit configuration files:
   ```bash
   apt-get install nano -y
   ```
   ![Install Nano](https://github.com/user-attachments/assets/78319fa9-9365-4b41-94de-a8959ce4fe12)

---

### **Step 5: Set JAVA_HOME**

1. Open the `.bashrc` file to set environment variables:
   ```bash
   nano ~/.bashrc
   ```
   Add the following lines:
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

### **Step 6: Install wget for File Downloads**

1. Install the `wget` tool, which is used to download files from the internet:
   ```bash
   apt-get install wget -y
   ```
   ![Install Wget](https://github.com/user-attachments/assets/3e076cbc-74d7-461f-be96-54521057aaf9)

---

### **Step 7: Download and Extract Hadoop**

1. Download Hadoop using `wget`:
   ```bash
   wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz
   ```

2. Extract the downloaded file to `/opt`:
   ```bash
   tar -xzf hadoop-2.10.2.tar.gz -C /opt
   ```

3. Rename the extracted directory for simplicity:
   ```bash
   mv /opt/hadoop-2.10.2 /opt/hadoop
   ```
   ![Download and Extract Hadoop](https://github.com/user-attachments/assets/63396bcc-03a6-42dd-8987-eadc566beb15)
   ![Rename Hadoop Directory](https://github.com/user-attachments/assets/4bfe042a-0a03-4064-b44f-6ff60e7e3b62)

---

### **Step 8: Set Hadoop Environment Variables**

1. Open the `.bashrc` file again:
   ```bash
   nano ~/.bashrc
   ```

2. Add these lines:
   ```bash
   export HADOOP_HOME=/opt/hadoop
   export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
   ```
   ![Edit Bashrc for Hadoop](https://github.com/user-attachments/assets/ab6d24f8-5348-4329-9a39-0ba869ee315c)

3. Apply the changes:
   ```bash
   source ~/.bashrc
   ```
   ![Apply Hadoop Changes](https://github.com/user-attachments/assets/08d511c2-487a-4b9e-b3dc-be1ec830e744)

4. Verify the Hadoop installation:
   ```bash
   hadoop version
   ```
   ![Hadoop Version](https://github.com/user-attachments/assets/f34af121-3663-495a-bcb8-680584a6111e)

---

### **Step 9: Configure Hadoop**

#### **1. Configure core-site.xml**
1. Open the `core-site.xml` file:
   ```bash
   nano /opt/hadoop/etc/hadoop/core-site.xml
   ```

2. Add the following configuration:
   ```xml
   <configuration>
       <property>
           <name>fs.defaultFS</name>
           <value>hdfs://localhost:9000</value>
       </property>
   </configuration>
   ```

---

#### **2. Configure hdfs-site.xml**
1. Open the `hdfs-site.xml` file:
   ```bash
   nano /opt/hadoop/etc/hadoop/hdfs-site.xml
   ```

2. Add the following configuration:
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

#### **3. Set JAVA_HOME in Hadoop**
1. Open the `hadoop-env.sh` file:
   ```bash
   nano /opt/hadoop/etc/hadoop/hadoop-env.sh
   ```

2. Add the following line:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   ```

---

### **Next Steps**
- Format the HDFS (Hadoop Distributed File System) and start the Hadoop services.  
- You now have a Docker container ready with Hadoop installed and configured! 🎉
