# Practical: Running a WordCount Job on YARN ![image](https://github.com/user-attachments/assets/04d38509-38b8-4cef-b544-4a8c566fd863)

In this practical, you will run a simple WordCount job using Hadoop YARN. This exercise walks you through preparing a basic Hadoop job and running it on a YARN cluster.
 ![image](https://github.com/user-attachments/assets/4e70e157-5bc5-4cd3-9f0f-de6ce97437f2)

![image](https://github.com/user-attachments/assets/2d961b6d-f046-42cc-b18b-08d0a313137e)

### Prerequisites:
1. Docker Desktop must be up and running.
2. **YARN ResourceManager** and **NodeManager** must be up and running.
3. Hadoop should be set up correctly, with access to the HDFS file system.
4. A sample WordCount program (JAR) is ready to be executed.

---

### Step-by-Step Guide
docker exec -it namenode /bin/bash
---

### Step-by-Step Guide

#### Step 1: Upload Data to HDFS

Before running a YARN job, we need some input data in HDFS. We will create a simple text file locally and upload it to HDFS.

1. **Create a sample text file locally inside docker terminal**:
   Use the following commands to create a file called `sample.txt` on your local machine with some sample text data.

   ```bash
   echo "Hadoop YARN Resource Manager" > sample.txt
   echo "YARN is responsible for managing resources" >> sample.txt
   echo "It is a resource manager and job scheduler" >> sample.txt
   ```

   ![image](https://github.com/user-attachments/assets/92edc8cc-86de-40b7-9bba-d298a6579cf4)


2. **Upload the text file to HDFS**:
   Use the `hadoop fs -put` command to upload the file to HDFS.

   ```bash
   hadoop fs -mkdir -p /user/root/input  # Create the input directory in HDFS
   hadoop fs -put sample.txt /user/root/input/
   ```

   You can confirm the file is uploaded by running:

   ```bash
   hadoop fs -ls /user/root/input/
   ```

---

#### Step 2: Submit the WordCount Job to YARN

Now, we can run the WordCount job using YARN. This job will count the occurrences of each word in the input file.

1. **Change your working directory to where the `wordCount.jar` is located**:

   ```bash
   cd /code/Hadoop_Code  # Change to the directory where wordCount.jar is stored
   ```

2. **Submit the WordCount job to YARN**:
   Run the following command to submit the job:

   ```bash
   hadoop jar wordCount.jar org.apache.hadoop.examples.WordCount /user/root/input /user/root/outputfolder
   ```

   - `wordCount.jar`: The MapReduce program (JAR file).
   - `/user/root/input`: The input directory in HDFS containing the `sample.txt` file.
   - `/user/root/outputfolder`: The output directory in HDFS where the result will be stored.

3. **Check the YARN UI**:
   After submitting the job, you can monitor the job through the YARN ResourceManager UI.

   - Visit the YARN ResourceManager UI at `http://localhost:8088`.
   - Look for your job under the "Applications" section. You should see your job with its status (e.g., Running, Completed, etc.).

   ![YARN UI](https://github.com/user-attachments/assets/5d771515-9f49-4fcf-85d4-1d726c4c3e4b)

   - Click on your job to see more details, such as job progress, logs, and running containers.

   ![YARN UI Logs](https://github.com/user-attachments/assets/f674fb08-1b1a-44b5-942c-43af7701a3ca)

---

#### Step 3: Check the Output of the WordCount Job

Once the job finishes, you can view the results in HDFS.

1. **Check the output on HDFS**:
   To verify that the output was successfully created, run the following command:

   ```bash
   hadoop fs -ls /user/root/outputfolder
   ```

   You should see output files like `part-r-00000`.

2. **View the contents of the output file**:
   To view the WordCount results, use the following command:

   ```bash
   hadoop fs -cat /user/root/outputfolder/part-r-00000
   ```

   The output will show the words and their respective counts, like this:

   ```
   Hadoop 1
   YARN 2
   Resource 1
   Manager 1
   is 1
   responsible 1
   for 1
   managing 1
   resources 1
   and 1
   job 1
   scheduler 1
   ```

---

#### Step 4: Clean Up

Once you’ve completed the practical, it's good practice to clean up by deleting the output files and any unnecessary files.

1. **Remove the output directory from HDFS**:

   ```bash
   hadoop fs -rm -r /user/root/outputfolder
   ```

2. **Optional**: Remove the input file from HDFS if you no longer need it.

   ```bash
   hadoop fs -rm /user/root/input/sample.txt
   ```

---

Yarn (Yet Another Resource Negotiator) in the Hadoop ecosystem is a resource management layer, and its commands are different from the JavaScript package manager **Yarn**. Below is a list of essential **Yarn commands for Hadoop**:

---

### **General Yarn Commands**
1. **Check Yarn Version**  
   ```bash
   yarn version
   ```
   Displays the version of Yarn installed in your Hadoop environment.

2. **Check Cluster Nodes**  
   ```bash
   yarn node -list
   ```
   Lists all the active, decommissioned, and unhealthy nodes in the cluster.

3. **Resource Manager Web UI**  
   ```bash
   yarn rmadmin -getServiceState rm1
   ```
   Checks the state of a specific Resource Manager.

---

### **Application Management**
4. **Submit an Application**  
   ```bash
   yarn jar <path-to-jar> <main-class> [options]
   ```
   Submits a new application to the Yarn cluster.

5. **List Applications**  
   ```bash
   yarn application -list
   ```
   Lists all running applications on the Yarn cluster.

6. **View Application Status**  
   ```bash
   yarn application -status <application_id>
   ```
   Shows the status of a specific application.
Example Output:-
![image](https://github.com/user-attachments/assets/44ab74ab-f662-4d87-834c-43e812117be0)



8. **Kill an Application**  
   ```bash
   yarn application -kill <application_id>
   ```
   Terminates a specific application.

---

### **Logs and Diagnostics**
8. **View Logs of an Application**  
   ```bash
   yarn logs -applicationId <application_id>
   ```
   Displays logs for a specific application.

9. **Fetch Application Logs to Local System**  
   ```bash
   yarn logs -applicationId <application_id> > logs.txt
   ```
   Saves application logs to a local file.

---

### **Queue Management**
10. **List Queues**  
    ```bash
    yarn queue -list
    ```
    Lists all queues available in the Yarn cluster.

11. **Move Application to Another Queue**  
    ```bash
    yarn application -moveToQueue <queue_name> -appId <application_id>
    ```
    Moves a running application to a different queue.

---

### **Resource Manager Administration**
12. **Refresh Queue Configuration**  
    ```bash
    yarn rmadmin -refreshQueues
    ```
    Reloads the queue configuration without restarting the Resource Manager.

13. **Refresh Node Information**  
    ```bash
    yarn rmadmin -refreshNodes
    ```
    Updates the Resource Manager with the latest node information.

14. **Get Cluster Metrics**  
    ```bash
    yarn cluster -metrics
    ```
    Shows resource usage metrics of the Yarn cluster.

15. **Decommission a Node**  
    ```bash
    yarn rmadmin -decommission <node-hostname>
    ```
    Marks a specific node as decommissioned.

16. **Check Cluster Status**  
    ```bash
    yarn cluster -status
    ```
    Displays overall status and health of the cluster.

---

### **Node Manager Commands**
17. **Start Node Manager**  
    ```bash
    yarn nodemanager
    ```
    Starts the Node Manager daemon.

18. **Stop Node Manager**  
    ```bash
    yarn nodemanager -stop
    ```
    Stops the Node Manager daemon.

19. **List Containers on a Node**  
    ```bash
    yarn nodemanager -list
    ```
    Lists all running containers on the Node Manager.

---

### **Debugging and Troubleshooting**
20. **View Container Logs**  
    ```bash
    yarn logs -containerId <container_id> -nodeAddress <node_hostname>
    ```
    Retrieves logs for a specific container.

21. **Check Application Environment Variables**  
    ```bash
    yarn application -envs <application_id>
    ```
    Displays environment variables for a specific application.

---

These commands allow you to manage applications, queues, resources, and logs effectively on a Hadoop Yarn cluster.

### Additional Tips

- **Custom Jobs**: You can write your own MapReduce programs in Java and package them into a JAR file, then submit them to YARN in a similar way.
- **Resource Allocation**: If you want to control how much memory or CPU your YARN job uses, you can specify resources in the command, or modify the YARN configuration files.

---

### Troubleshooting

- **Job Not Starting**: If the job does not start or fails, check the logs for errors. You can view the logs from the YARN ResourceManager UI or use the following command to retrieve logs:

   ```bash
   yarn logs -applicationId <application_id>
   ```

- **Out of Memory Errors**: If your job runs into memory issues, consider adjusting the memory allocation in the `yarn-site.xml` configuration file for your NodeManagers and ResourceManager.

---

**Conclusion**
This practical exercise provided a hands-on experience in running a simple MapReduce job (WordCount) on YARN. You can now submit jobs, monitor them, and view results in HDFS using the YARN ResourceManager. By following the steps outlined, you should be able to run more complex jobs and work with Hadoop in a YARN-managed environment.

---

### Instructions for Use:
- Ensure your Hadoop environment (including YARN and HDFS) is properly set up before running the job.
- Submit your jobs using the `hadoop jar` command and monitor their progress through the YARN UI.
- Clean up your HDFS after completing the practical exercise to maintain a clutter-free envir
