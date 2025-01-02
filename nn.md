To set up a Hadoop ecosystem on a Docker container with the provided configurations, follow these steps:

### 1. **Run the Docker Container**
Use the Docker command you provided to start a container with the required ports exposed:
```bash
docker run -it \
    -p 50070:50070 -p 9870:9870 -p 8088:8088 \
    -p 50075:50075 -p 8042:8042 -p 10000:10000 \
    -p 9083:9083 -p 41414:41414 -p 35871:35871 \
    -p 8000:8000 -p 4040:4040 -p 7077:7077 \
    -p 18080:18080 \
    --name bigdata-container \
    ubuntu:20.04 /bin/bash
```

### 2. **Download and Extract Hadoop**
Inside the container, download Hadoop:
```bash
wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz
tar -xvzf hadoop-2.10.2.tar.gz
mv hadoop-2.10.2 /opt/hadoop
```

### 3. **Set Environment Variables**
Update the `~/.bashrc` file:
```bash
echo "export HADOOP_HOME=/opt/hadoop" >> ~/.bashrc
echo "export PATH=\$PATH:/opt/hadoop/bin:/opt/hadoop/sbin" >> ~/.bashrc
echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> ~/.bashrc
source ~/.bashrc
```

### 4. **Modify Hadoop Configuration Files**
Edit the following configuration files located in `/opt/hadoop/etc/hadoop/`.

#### **`core-site.xml`**
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

#### **`hdfs-site.xml`**
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

#### **`yarn-site.xml`**
```xml
<configuration>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>localhost</value>
    </property>
    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>0.0.0.0:8088</value>
    </property>
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>8192</value>
    </property>
    <property>
        <name>yarn.nodemanager.resource.cpu-vcores</name>
        <value>4</value>
    </property>
</configuration>
```

#### **`mapred-site.xml`**
```xml
<configuration>
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>localhost:10020</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>localhost:19888</value>
    </property>
</configuration>
```

### 5. **Format the NameNode**
```bash
hdfs namenode -format
```

### 6. **Start Hadoop Services**
Start the Hadoop daemons:
```bash
start-dfs.sh
start-yarn.sh
```

### 7. **Verify Services**
- HDFS NameNode Web UI: `http://<host-ip>:50070`
- YARN ResourceManager Web UI: `http://<host-ip>:8088`
- HDFS DataNode Web UI: `http://<host-ip>:50075`

### 8. **Persist Data (Optional)**
To ensure data persists, mount volumes using Docker's `-v` option when starting the container:
```bash
docker run -it \
    -v /host/path/namenode:/opt/hadoop/hdfs/namenode \
    -v /host/path/datanode:/opt/hadoop/hdfs/datanode \
    -p 50070:50070 -p 9870:9870 \
    ...
    --name bigdata-container ubuntu:20.04 /bin/bash
```

This setup will provide a functional Hadoop cluster on Docker with the specified configurations. Let me know if you need further assistance!
