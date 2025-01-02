To test HDFS commands and perform a WordCount operation using the `wordCount.jar` file located on your local machine (in your Windows `Downloads` folder), you will need to interact with the Hadoop HDFS running in the Docker container. Here’s a step-by-step guide on how to perform this:

Download: 
hadoop-ecosystemNIELIT.zip
extract it and then compose


![image](https://github.com/user-attachments/assets/d49ec331-240d-4e18-b62e-7b9312661e97)



### Steps:

#### 1. **Ensure the Hadoop Cluster is Running**
First, make sure the Hadoop ecosystem is running via Docker:

```bash
docker-compose up --build
```

Verify that the NameNode and other services are running by checking the logs:

```bash
docker-compose logs -f namenode
```
#### 2. **Copy the `input.txt` File to the Docker Container**

![image](https://github.com/user-attachments/assets/e19c3559-a175-42bc-9c2d-1a5388111736)


#### 2. **Copy the `wordCount.jar` File to the Docker Container**
Since the `wordCount.jar` is on your local Windows machine, you need to copy it into the Hadoop Docker container so that HDFS can access it.

You can copy the JAR file from your Windows machine to the Hadoop Docker container using `docker cp`:

```bash
docker cp C:\Users\princ\Downloads\wordCount.jar namenode:/root/wordCount.jar
```

This copies the `wordCount.jar` from your local `Downloads` folder to the `/root/` directory in the `namenode` container.

#### 3. **Use the Hadoop CLI to Interact with HDFS**
Now, you need to interact with HDFS inside the Docker container. Follow these steps:

- Open a terminal to the running `namenode` container:

```bash
docker exec -it namenode bash
```

- Once inside the container, you can use the Hadoop CLI commands. To confirm that HDFS is working, list the files in the root of HDFS:

```bash
hadoop fs -ls /
```

This should show the directories/files present in HDFS.

#### 4. **Upload the Input Files to HDFS**
To run the WordCount example, you need some input text. Let's assume you have an input file (`input.txt`) on your local machine that you want to upload to HDFS.

First, copy the wordCount.jar file to the Docker container:

![image](https://github.com/user-attachments/assets/49d5e85d-fc03-4ea3-8d92-5b18321bc8e1)

#### 5. **Run the WordCount Program**
You are now ready to run the WordCount program using the `wordCount.jar`. You can do this by executing the following command:

```bash
hadoop jar /root/wordCount.jar WordCount /user/hadoop/input.txt /user/hadoop/output
```

This command:
- Runs the `WordCount.jar` program.
- Takes `/user/hadoop/input.txt` as input.
- Stores the output in `/user/hadoop/output` directory on HDFS.

#### 6. **Check the Output**
Once the job is complete, check the output directory in HDFS:

```bash
hadoop fs -ls /user/hadoop/output
```

This should show the output files (like `part-r-00000`, `part-r-00001`, etc.). To view the contents of one of these files:

```bash
hadoop fs -cat /user/hadoop/output/part-r-00000
```

This will display the results of the WordCount job.

#### 7. **Exit the Docker Container**
Once you're done, you can exit the `namenode` container:

```bash
exit
```

### Conclusion:
These steps help you interact with Hadoop HDFS running in Docker, upload input data, run the WordCount example using a local `wordCount.jar`, and retrieve the results. You can perform similar steps for other HDFS operations or Hadoop-related tasks.
