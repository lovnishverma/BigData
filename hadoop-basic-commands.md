Here are some of the essential **Hadoop basic commands** for managing Hadoop Distributed File System (HDFS) and interacting with YARN:

### **1. HDFS Commands**  
These commands are used for interacting with HDFS, which is Hadoop's file system.

- **List files in a directory**  
  ```bash
  hdfs dfs -ls /path/to/directory
  ```
  Lists all files and directories in the specified HDFS directory.

- **Create a directory**  
  ```bash
  hdfs dfs -mkdir /path/to/directory
  ```
  Creates a directory in HDFS.

- **Upload a file to HDFS**  
  ```bash
  hdfs dfs -put localfile /user/hadoop/hdfspath
  ```
  Uploads a file from the local file system to HDFS.

- **Download a file from HDFS**  
  ```bash
  hdfs dfs -get /user/hadoop/hdfspath localfile
  ```
  Downloads a file from HDFS to the local file system.

- **View file contents**  
  ```bash
  hdfs dfs -cat /path/to/file
  ```
  Displays the contents of a file in HDFS.

- **Remove a file or directory from HDFS**  
  ```bash
  hdfs dfs -rm /path/to/file
  hdfs dfs -rm -r /path/to/directory  # To remove a directory and its contents
  ```
  Removes files or directories from HDFS.

- **Check file status**  
  ```bash
  hdfs dfs -stat /path/to/file
  ```
  Displays the status of a file or directory in HDFS.

- **Display the disk usage of a directory**  
  ```bash
  hdfs dfs -du /path/to/directory
  ```
  Shows the disk usage of files and directories in HDFS.

### **2. YARN Commands**  
YARN (Yet Another Resource Negotiator) is used for resource management and job scheduling.

- **List running YARN applications**  
  ```bash
  yarn application -list
  ```
  Lists all the currently running applications on YARN.

- **Check application status**  
  ```bash
  yarn application -status <Application_ID>
  ```
  Displays the status of a specific application.

- **Kill a running YARN application**  
  ```bash
  yarn application -kill <Application_ID>
  ```
  Kills a running YARN application by its ID.

- **Run a YARN application**  
  ```bash
  yarn jar <jar-file> <main-class> [arguments]
  ```
  Runs a YARN job with the specified JAR file and main class.

### **3. Hadoop Daemon Commands**  
These commands are used for managing Hadoop services and daemons like NameNode, DataNode, ResourceManager, etc.

- **Start Hadoop services (HDFS and YARN)**  
  ```bash
  start-dfs.sh    # Starts HDFS daemons: NameNode, DataNode, etc.
  start-yarn.sh   # Starts YARN daemons: ResourceManager, NodeManager, etc.
  ```

- **Stop Hadoop services (HDFS and YARN)**  
  ```bash
  stop-dfs.sh     # Stops HDFS daemons
  stop-yarn.sh    # Stops YARN daemons
  ```

- **Check the status of Hadoop services**  
  ```bash
  jps    # Lists all running Java processes, including Hadoop daemons like NameNode, ResourceManager, etc.
  ```

### **4. General Hadoop Commands**

- **Run a MapReduce job**  
  ```bash
  hadoop jar <your-jar-file> <main-class> <input> <output>
  ```
  Executes a MapReduce job using the specified JAR file, with input and output directories.

- **Show Hadoop version**  
  ```bash
  hadoop version
  ```
  Displays the version of Hadoop installed.

- **Start a Hadoop cluster (for a single-node setup)**  
  ```bash
  start-all.sh
  ```
  Starts all the Hadoop daemons (NameNode, DataNode, ResourceManager, and NodeManager).

### **5. Other Useful Commands**

- **Check the status of HDFS health**  
  ```bash
  hdfs dfsadmin -report
  ```
  Provides a report on the status of the HDFS cluster.

- **Show detailed information about a file**  
  ```bash
  hdfs dfs -stat %b /path/to/file
  ```
  Displays the block size of the file in HDFS.

These are some of the basic commands used to interact with and manage Hadoop clusters, including file operations, YARN job management, and daemon control.
