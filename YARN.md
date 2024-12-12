Here’s a step-by-step guide for running a WordCount job on YARN, written clearly and in a simplified manner for easy understanding:
![image](https://github.com/user-attachments/assets/04d38509-38b8-4cef-b544-4a8c566fd863) ![image](https://github.com/user-attachments/assets/4e70e157-5bc5-4cd3-9f0f-de6ce97437f2)

![image](https://github.com/user-attachments/assets/2d961b6d-f046-42cc-b18b-08d0a313137e)


```markdown
# Practical: Running a WordCount Job on YARN

In this practical, you will run a simple WordCount job using Hadoop YARN. This exercise walks you through preparing a basic Hadoop job and running it on a YARN cluster.

### Prerequisites:
1. Docker Destop must be up and running.
2. **YARN ResourceManager** and **NodeManager** must be up and running.
3. Hadoop should be set up correctly, with access to the HDFS file system.
4. A sample WordCount program (JAR) is ready to be executed.

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
