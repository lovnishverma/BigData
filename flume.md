Here’s a step-by-step guide to **start HDFS** and then **install and configure Apache Flume**

---

### **Part 1: Start Hadoop and HDFS**

#### Step 1: Start Hadoop Services
1. Open a terminal and navigate to the Hadoop installation directory:
   ```bash
   cd $HADOOP_HOME
   ```
2. Start HDFS and related services:
   ```bash
   start-dfs.sh
   start-yarn.sh
   ```

3. Verify that the HDFS services are running:
   ```bash
   jps
   ```
   You should see services like `NameNode`, `DataNode`, `SecondaryNameNode`, `ResourceManager`, and `NodeManager` in the list.

#### Step 2: Create a Directory in HDFS for Flume Output
1. Create a directory in HDFS to store Flume’s output:
   ```bash
   hdfs dfs -mkdir -p /user/hadoop/flume-output
   ```

2. Verify the directory:
   ```bash
   hdfs dfs -ls /user/hadoop/
   ```

---

### **Part 2: Install Apache Flume**

#### Step 1: Download Apache Flume
1. Download the latest stable release of Flume from the Apache website:
   ```bash
   wget https://downloads.apache.org/flume/1.11.0/apache-flume-1.11.0-bin.tar.gz
   ```
   Replace `<1.11.0>` with the actual version (e.g., `1.9.0`).

2. Extract the Flume tarball:
   ```bash
   tar -xzf apache-flume-1.11.0-bin.tar.gz
   ```

3. Move the extracted folder to a convenient directory (e.g., `/usr/local/`):
   ```bash
   sudo mv apache-flume-1.11.0-bin /usr/local/flume
   ```

#### Step 2: Configure Flume Environment
1. Open the Flume environment file:
   ```bash
   nano /usr/local/flume/conf/flume-env.sh
   ```

2. Add the following lines to configure Java and Hadoop paths:

# Set Java Home Path
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

# Add Java to PATH
export PATH=$JAVA_HOME/bin:$PATH

# Set Hadoop Home Path (if Hadoop is installed)
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

# Optional: Configure Flume logging (uncomment if needed)
# export FLUME_LOG_DIR=/usr/local/flume/logs
# export FLUME_PID_DIR=/usr/local/flume/pid


   Replace `/path/to/java` and `/path/to/hadoop` with your actual Java and Hadoop installation paths.

3. Save and close the file.

#### Step 3: Verify Flume Installation
Run the following command to verify Flume:
```bash
/usr/local/flume/bin/flume-ng version
```

---

### **Part 3: Configure Flume for Data Ingestion**

#### Step 1: Create a Sample Log File
Create a sample log file to act as the data source:
```bash
echo "Hello World from Flume!" > /tmp/sample_log.txt
```

#### Step 2: Create a Flume Configuration File
1. Navigate to the Flume configuration directory:
   ```bash
   cd /usr/local/flume/conf
   ```

2. Create a configuration file `flume.conf`:
   ```bash
   nano flume.conf
   ```

3. Add the following configuration:
   ```properties
   # Define the agent, source, sink, and channel
   agent1.sources = source1
   agent1.sinks = sink1
   agent1.channels = channel1

   # Source Configuration
   agent1.sources.source1.type = exec
   agent1.sources.source1.command = tail -F /tmp/sample_log.txt

   # Sink Configuration
   agent1.sinks.sink1.type = hdfs
   agent1.sinks.sink1.hdfs.path = hdfs://localhost:9000/user/hadoop/flume-output
   agent1.sinks.sink1.hdfs.fileType = DataStream
   agent1.sinks.sink1.hdfs.writeFormat = Text

   # Channel Configuration
   agent1.channels.channel1.type = memory
   agent1.channels.channel1.capacity = 1000
   agent1.channels.channel1.transactionCapacity = 100

   # Bind source and sink to the channel
   agent1.sources.source1.channels = channel1
   agent1.sinks.sink1.channel = channel1
   ```

4. Save and close the file.

#### Step 3: Run Flume Agent
Run the Flume agent with the created configuration:
```bash
/usr/local/flume/bin/flume-ng agent --conf /usr/local/flume/conf --conf-file /usr/local/flume/conf/flume.conf --name agent1 -Dflume.root.logger=INFO,console
```

---

### **Part 4: Verify the Output in HDFS**

1. Check the files in the HDFS output directory:
   ```bash
   hdfs dfs -ls /user/hadoop/flume-output
   ```

2. View the contents of the output file:
   ```bash
   hdfs dfs -cat /user/hadoop/flume-output/<file_name>
   ```

   You should see the data from the sample log file.

---

This step-by-step approach will help you successfully set up and test Apache Flume for data ingestion into HDFS.
