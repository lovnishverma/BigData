Steps for setting up **Hadoop** and **Java** on Docker:  


# **Complete Guide to Install and Configure Sqoop, Hadoop, MySQL, and Java on Docker**

This guide outlines the steps to install and configure **Hadoop** and **Java** in a Docker container for big data operations.

1. **Check Existing Containers**  
   List all containers (including stopped ones) to confirm the presence of `nielit-container`:
   ```bash
   docker ps -a
   ```

   Look for the container named `nielit-container` in the output.

2. **Restart the Existing Container (If You Want to Reuse It)**  
   If the container `nielit-container` is stopped and you want to use it, restart it:
   ```bash
   docker start nielit-container
   ```

   Then, attach to it using:
   ```bash
   docker exec -it nielit-container /bin/bash
   ```

3. **Remove the Existing Container (If You Want to Create a New One)**  
   If you no longer need the existing `nielit-container`, remove it:
   ```bash
   docker rm nielit-container
   ```

   After removing it, you can create a new container with the same name:
   ```bash
   docker run -it -p 8088:8088 -p 9000:9000 -p 8042:8042 -p 8080:8080 --name nielit-container ubuntu:20.04 bash
   ```

4. **Use a Different Container Name**  
   If you don’t want to delete the existing container, create a new one with a different name:
   ```bash
   docker run -it -p 50070:50070 -p 9870:9870 -p 8088:8088 -p 50075:50075 -p 8042:8042 -p 10000:10000 -p 9083:9083 -p 41414:41414 -p 35871:35871 -p 8000:8000 -p 4040:4040 -p 7077:7077 -p 18080:18080 --name bigdata-container ubuntu:20.04 /bin/bash

   ```

### Additional Notes
docker run -it \
    -p 50070:50070 \      # HDFS NameNode Web UI
    -p 9870:9870 \        # HDFS ResourceManager Web UI
    -p 8088:8088 \        # YARN ResourceManager Web UI
    -p 50075:50075 \      # HDFS DataNode Web UI
    -p 8042:8042 \        # YARN NodeManager Web UI
    -p 10000:10000 \      # HiveServer2
    -p 9083:9083 \        # Hive Metastore
    -p 41414:41414 \      # Flume Avro Client
    -p 35871:35871 \      # Flume Thrift Client
    -p 8000:8000 \        # Flume Admin Web UI
    -p 4040:4040 \        # Spark Web UI
    -p 7077:7077 \        # Spark Master
    -p 18080:18080 \      # Spark History Server
    --name bigdata-container \
    ubuntu:20.04 /bin/bash


- **Check Container Logs**  
   If the container failed previously, check its logs to identify issues:
   ```bash
   docker logs nielit-container
   ```

- **Inspect Container Details**  
   To investigate the container further:
   ```bash
   docker inspect nielit-container
   ``` 

## **Step 1: Set up a Fresh Docker Container**

1. **Create and Start a New Ubuntu Container:**
   ```bash
   docker run -it -p 50070:50070 -p 9870:9870 -p 8088:8088 -p 50075:50075 -p 8042:8042 -p 10000:10000 -p 9083:9083 -p 41414:41414 -p 35871:35871 -p 8000:8000 -p 4040:4040 -p 7077:7077 -p 18080:18080 --name bigdata-container ubuntu:20.04 /bin/bash
   ```

2. **Update Package Lists:**
   ```bash
   apt-get update
   ```
![image](https://github.com/user-attachments/assets/711d5e9c-fc73-41d8-bf3f-e65d55e348c8)

---

## **Step 2: Install Java (OpenJDK 8) This Step will Take some time**

1. **Install OpenJDK 8:**
   ```bash
   apt-get install openjdk-8-jdk -y
   ```
![image](https://github.com/user-attachments/assets/6c7edc75-61fb-4f5e-a3fb-2e23d823cf84)


2. **Verify Java Installation:**
   ```bash
   java -version
   ```
![image](https://github.com/user-attachments/assets/a838c0ec-7f92-454c-af88-6731101cb514)


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

## **Step 3: Install Hadoop**

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
![image](https://github.com/user-attachments/assets/cf113dae-d86b-4d01-a6c3-4ccfa3e6d371)


---

## **Step 4: Configure Hadoop**

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
 Save and exit the editor by pressing `CTRL + O`, `Enter`, and then `CTRL + X`.
---
2. **Edit `hdfs-site.xml`:**
   ```bash
   nano /opt/hadoop/etc/hadoop/hdfs-site.xml
   ```
   Add the following:
   ```xml
   <configuration>
       <property>
        <name>dfs.namenode.http-address</name>
        <value>localhost:50070</value>
    </property>
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

   
3. Edit `yarn-site.xml`
 Open the `yarn-site.xml` file for editing:
   ```bash
   nano /opt/hadoop/etc/hadoop/yarn-site.xml
   ```

 Add the following configuration to expose the NodeManager web UI:
   ```xml
  <configuration>

   <!-- ResourceManager hostname -->
   <property>
       <name>yarn.resourcemanager.hostname</name>
       <value>localhost</value>
   </property>

   <!-- ResourceManager web UI address -->
   <property>
       <name>yarn.resourcemanager.webapp.address</name>
       <value>0.0.0.0:8088</value>
   </property>

   <!-- YARN ResourceManager address -->
   <property>
       <name>yarn.resourcemanager.address</name>
       <value>0.0.0.0:8032</value>
   </property>

   <!-- ResourceManager Scheduler address -->
   <property>
       <name>yarn.resourcemanager.scheduler.address</name>
       <value>0.0.0.0:8030</value>
   </property>

   <!-- NodeManager address -->
   <property>
       <name>yarn.nodemanager.address</name>
       <value>0.0.0.0:8042</value>
   </property>

   <!-- ResourceManager State Store -->
   <property>
       <name>yarn.resourcemanager.store.class</name>
       <value>org.apache.hadoop.yarn.server.resourcemanager.store.impl.InMemoryRMStateStore</value>
   </property>

   <!-- Scheduling Policy -->
   <property>
       <name>yarn.resourcemanager.scheduler.class</name>
       <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler</value>
   </property>

   <!-- Log Aggregation -->
   <property>
       <name>yarn.log-aggregation-enable</name>
       <value>true</value>
   </property>

   <!-- Maximum Memory for NodeManagers -->
   <property>
       <name>yarn.nodemanager.resource.memory-mb</name>
       <value>8192</value>
   </property>

   <!-- Maximum vCores for NodeManagers -->
   <property>
       <name>yarn.nodemanager.resource.cpu-vcores</name>
       <value>4</value>
   </property>

   <!-- NodeManager Logs Directory -->
   <property>
       <name>yarn.nodemanager.log-dirs</name>
       <value>/var/log/hadoop-yarn/nodemanager</value>
   </property>

</configuration>
   ```


--- 

4. **Set `JAVA_HOME` in Hadoop:**
   Edit `hadoop-env.sh`:
   ```bash
   nano /opt/hadoop/etc/hadoop/hadoop-env.sh
   ```
   Add the line:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   ```
5. **Format NameNode:**
   ```bash
   hdfs namenode -format
   ```
![image](https://github.com/user-attachments/assets/7eb773ae-4e8c-484f-9be8-b018504190f9)


### 1. **Install SSH**

Hadoop requires SSH to be installed on all nodes in the cluster (even if you're running everything on a single node). Install SSH using the following commands:

```bash
apt update
apt install openssh-client openssh-server
```

After installing SSH, make sure that the `ssh` command is available:

```bash
ssh -v localhost
```

If this works, SSH is properly set up if not then start SSH Daemon.

### 2. **Start SSH Daemon**

Make sure the SSH daemon is running. You can start it using:

```bash
service ssh start
```

Then, ensure SSH is properly configured for passwordless login. You can do this by creating an SSH key and copying it to the `~/.ssh/authorized_keys` file on the same machine (or between nodes if you have a multi-node setup).

To generate the SSH key pair (Do not enter yes and don't enter anything keep blank):

```bash
ssh-keygen -t rsa
```

Then, copy the public key to the authorized keys:

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

### 3. **Verify Hadoop Configuration**

Ensure that your Hadoop `slaves` configuration is correct. By default, Hadoop expects a list of slave nodes (if you are running a multi-node setup), but since you’re on a single node, make sure the `slaves` file located at `$HADOOP_HOME/etc/hadoop/slaves` contains `localhost` or the hostname of your machine.

Edit the `slaves` file:

```bash
nano $HADOOP_HOME/etc/hadoop/slaves
```

Ensure it contains:

```text
localhost
```

It will ask you Are you sure you want to continue connecting (yes/no/[fingerprint])? yes ...Enter yes

```bash
start-all.sh
```
then verify

```bash
jps
```

You should see processes like `NameNode`, `DataNode`, and `SecondaryNameNode` if everything is working correctly.

Start ResourceManager
If the ResourceManager is not running, try starting it manually:

```bash
start-yarn.sh
```
Check the status of the HDFS services:

```bash
jps
```
![image](https://github.com/user-attachments/assets/ff73c6bb-b9d3-4f50-8653-3ff173a1f45f)


You should see processes like `NameNode`, `ResourceManager`, `DataNode`, and `SecondaryNameNode` if everything is working correctly.

![image](https://github.com/user-attachments/assets/250242f9-d48f-4ec2-b0da-4869cbb84927)


### 5. **Check HDFS Web UI**

Once the services are running, check the **ResourceManager and NodeManager Web UI**:

- **ResourceManager Web UI**: `http://localhost:8088`
- **NodeManager Web UI**: `http://localhost:8042`

![image](https://github.com/user-attachments/assets/76c37ea9-f385-41f2-94b3-15364250594c)


### 6. **Verify HDFS Access**

After ensuring that HDFS is running, check the HDFS directory:

```bash
hdfs dfs -ls /
```

It should now list the directories in the root of HDFS (e.g., `/user`, `/tmp`).


From your output, it looks like the `DataNode` has started successfully, as it appears in the `jps` command output with the process ID `12265`. This means your HDFS is now fully operational.

---

### Next Steps:

1. **Verify the HDFS Health:**
   Open the HDFS web UI in your browser:
   - URL: `http://<hostname or IP>:9870` (default is `http://localhost:9870` if running locally)
   - Check the "Live Nodes" section under the "Datanodes" tab to ensure the `DataNode` is listed as active.

2. **Test HDFS Functionality:**
   - **Create a Directory in HDFS:**
     ```bash
     hdfs dfs -mkdir /test
     ```
   - **List the Directory:**
     ```bash
     hdfs dfs -ls /
     ```
   - **Upload a File to HDFS:**
     ```bash
     hdfs dfs -put /path/to/local/file /test
     ```
   - **Read the File from HDFS:**
     ```bash
     hdfs dfs -cat /test/file
     ```

3. **Verify `YARN` Functionality:**
   - Check the YARN web UI:
     - URL: `http://<hostname or IP>:8088`
   - Submit a simple YARN job (e.g., word count) to test the cluster's execution environment.

4. **Monitor Logs for Errors:**
   If any issues arise, inspect the logs for each service. For the `DataNode`, the log is located at:
   ```
   /opt/hadoop/logs/hadoop--datanode-31e552ba4f34.out
   ```

5. **Integrate Additional Tools:**
   With HDFS and YARN working, you can now configure and start other tools like Hive, Sqoop, Flume, and Spark as part of your Big Data ecosystem.

---
