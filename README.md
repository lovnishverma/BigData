
![image](https://github.com/user-attachments/assets/343cfd7e-73b7-4eb2-a9a4-76c31f5703c8)

---

# **Hadoop-on-Docker Setup Guide**

This guide walks you through installing and running Hadoop on Docker, step-by-step.
---

## **Prerequisites**

Before starting, ensure the following tools are installed on your computer:

1. **Git**: [Download Git](https://git-scm.com/downloads/win)
   - Git is used to download the required files from a repository.

2. **Docker**: [Install Docker](https://docs.docker.com/desktop/setup/install/windows-install/)
   - Docker is used to create and manage virtualized containers.

---

## **Steps to Set Up Hadoop on Docker**

### Step 1: Clone the repository
Download the required files by running the following command in your terminal:
```bash
git clone https://github.com/lovnishverma/BigData.git
```

### Step 2: Navigate into the repository
Move to the folder where the downloaded files are located:
```bash
cd BigData
```

### Step 3: Start the Docker containers
Set up and start Hadoop containers using Docker:
```bash
docker-compose up -d
```

 the purpose of -d (runs the container in detached mode).

### Step 4: Verify running containers
Check if the containers are running:
```bash
docker container ls
```

### Step 5: Access the NameNode container
Enter the NameNode container to interact with Hadoop:
```bash
docker exec -it namenode /bin/bash
```
** -it refers to (interactive terminal)**
---

## **Running Hadoop Code** 

To View NameNode UI Visit:  http://localhost:9870/

### Step 1: Copy the `code` folder into the container
Use the following command in your windows cmd to copy the `code` folder to the container:
```bash
docker cp code namenode:/
```

![image](https://github.com/user-attachments/assets/7acdebdc-2b20-41bf-b92d-8555091d570c)


### Step 2: Locate the `data.txt` file
Inside the container, navigate to the `Hadoop_Code/input` directory where the `data.txt` file is located.

### Step 3: Create directories in the Hadoop file system
Run the following commands to set up directories in Hadoop's file system:
```bash
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/root
hdfs dfs -mkdir /user/root/input
```

### Step 4: Upload the `data.txt` file
Copy `data.txt` into the Hadoop file system:
```bash
hdfs dfs -put /code/Hadoop_Code/input/data.txt /user/root/input
```

### Step 5: Navigate to the directory containing the `wordCount.jar` file
Return to the directory where the `wordCount.jar` file is located:
```bash
cd /code/Hadoop_Code/
```

### Step 6: Execute the WordCount program 

To View NameNode UI Visit:  [http://localhost:8080/](http://localhost:8088/)
To View Resource Manager UI Visithttp://localhost:9870

Run the WordCount program to process the input data:
```bash
hadoop jar wordCount.jar org.apache.hadoop.examples.WordCount input output
```

### Step 7: Display the output
View the results of the WordCount program:
```bash
hdfs dfs -cat /user/root/output/*
```

---

## **Visual Assistance**

### **Step 1: Cloning Repository**
![Step 1](https://github.com/user-attachments/assets/7b010ecc-f37e-4006-857d-839315304c93)

### **Step 3: Creating Directories**
![Step 3](https://github.com/user-attachments/assets/ec2d4bbf-29a6-404d-af4b-304668b4fde3)

### **Step 4: Uploading Input File**
![Step 4](https://github.com/user-attachments/assets/8f8fd7f3-937a-42c9-b3f0-41279a993520)

### **Step 6: Running the Program**
![Step 6](https://github.com/user-attachments/assets/3674da9c-fe7a-4d4d-948e-7b28bf3a0f50)

### **Step 7: Viewing Output**
![Step 7](https://github.com/user-attachments/assets/6743b63e-d518-4c4c-a5a7-65118ee8d582)

---

## **Summary**

This guide simplifies setting up and running Hadoop on Docker. Each step ensures a smooth experience, even for beginners without a technical background. Follow the instructions carefully, and you’ll have a working Hadoop setup in no time!

Certainly! Here’s the explanation of your **MapReduce process** using the input example `DOG CAT RAT`, `CAR CAR RAT`, and `DOG CAR CAT`.
---

## 🐾 **Input Data**

The `data.txt` file contains the following lines:

```
DOG CAT RAT
CAR CAR RAT
DOG CAR CAT
```

This text file is processed by the **MapReduce WordCount program** to count the occurrences of each word.

---

## 💡 **What is MapReduce?**

- **MapReduce** is a two-step process:
  1. **Map Phase** 🗺️: Splits the input into key-value pairs.
  2. **Reduce Phase** ➕: Combines the key-value pairs to produce the final result.

It's like dividing a big task (word counting) into smaller tasks and then combining the results. 🧩

---

## 🔄 **How MapReduce Works in Your Example**

### **1. Map Phase** 🗺️

The mapper processes each line of the input file, splits it into words, and assigns each word a count of `1`.

For example:
```
DOG CAT RAT  -> (DOG, 1), (CAT, 1), (RAT, 1)
CAR CAR RAT  -> (CAR, 1), (CAR, 1), (RAT, 1)
DOG CAR CAT  -> (DOG, 1), (CAR, 1), (CAT, 1)
```

**Mapper Output**:
```
(DOG, 1), (CAT, 1), (RAT, 1)
(CAR, 1), (CAR, 1), (RAT, 1)
(DOG, 1), (CAR, 1), (CAT, 1)
```

---

### **2. Shuffle and Sort Phase** 🔄

This step groups all values for the same key (word) together and sorts them.

For example:
```
(CAR, [1, 1, 1])
(CAT, [1, 1])
(DOG, [1, 1])
(RAT, [1, 1])
```

---

### **3. Reduce Phase** ➕

The reducer sums up the counts for each word to get the total number of occurrences.

**Reducer Output**:
```
CAR 3  🏎️
CAT 2  🐱
DOG 2  🐶
RAT 2  🐭
```

---

### **Final Output** 📋

The final word count is saved in the HDFS output directory. You can view it using:
```bash
hdfs dfs -cat /user/root/output/*
```

**Result**:
```
CAR 3
CAT 2
DOG 2
RAT 2
```

---

## 🗂️ **HDFS Commands You Used**

Here are the basic HDFS commands you used and their purpose:

1. **Upload a file to HDFS** 📤:
   ```bash
   hdfs dfs -put data.txt /user/root/input
   ```
   - **What it does**: Uploads `data.txt` to the HDFS directory `/user/root/input`.
   - **Output**: No output, but the file is now in HDFS.

2. **List files in a directory** 📁:
   ```bash
   hdfs dfs -ls /user/root/input
   ```
   - **What it does**: Lists all files in the `/user/root/input` directory.
   - **Output**: Something like this:
     ```
     Found 1 items
     -rw-r--r--   1 root supergroup        50  2024-12-12  /user/root/input/data.txt
     ```

3. **View the contents of a file** 📄:
   ```bash
   hdfs dfs -cat /user/root/input/data.txt
   ```
   - **What it does**: Displays the contents of the `data.txt` file in HDFS.
   - **Output**:
     ```
     DOG CAT RAT
     CAR CAR RAT
     DOG CAR CAT
     ```

4. **Run the MapReduce Job** 🚀:
   ```bash
   hadoop jar wordCount.jar org.apache.hadoop.examples.WordCount input output
   ```
   - **What it does**: Runs the WordCount program on the input directory and saves the result in the output directory.

5. **View the final output** 📊:
   ```bash
   hdfs dfs -cat /user/root/output/*
   ```
   - **What it does**: Displays the word count results.
   - **Output**:
     ```
     CAR 3
     CAT 2
     DOG 2
     RAT 2
     ```

---

## 🛠️ **How You Utilized MapReduce**

1. **Input**:  
   You uploaded a small text file (`data.txt`) to HDFS.

2. **Process**:  
   The `WordCount` program processed the file using MapReduce:
   - The **mapper** broke the file into words and counted each occurrence.
   - The **reducer** aggregated the counts for each word.

3. **Output**:  
   The results were saved in HDFS and displayed using the `cat` command.

---

## 🧩 **Visualization of the Entire Process**

### **Input** (HDFS file):
```
DOG CAT RAT
CAR CAR RAT
DOG CAR CAT
```

### **Map Phase Output** 🗺️:
```
(DOG, 1), (CAT, 1), (RAT, 1)
(CAR, 1), (CAR, 1), (RAT, 1)
(DOG, 1), (CAR, 1), (CAT, 1)
```

### **Shuffle & Sort** 🔄:
```
(CAR, [1, 1, 1])
(CAT, [1, 1])
(DOG, [1, 1])
(RAT, [1, 1])
```

### **Reduce Phase Output** ➕:
```
CAR 3
CAT 2
DOG 2
RAT 2
```

---

![image](https://github.com/user-attachments/assets/a037fc47-7639-48b8-b3f7-5d9f2d5c51ac)

### 🔑 **Key Takeaways**
- **MapReduce** splits the task into small, manageable pieces and processes them in parallel.
- It’s ideal for large datasets but works the same for smaller ones (like your example).
- Hadoop is designed for distributed systems, making it powerful for big data processing.






### . **Stopping the Containers**  
To stop the Docker containers when done:
```bash
docker-compose down
```
This will stop and remove the containers and networks created by `docker-compose up`.

### 4. **Permissions Issue with Copying Files**  
If you face permission issues while copying files to containers ensure the correct directory permissions in Docker by using:
```bash
docker exec -it namenode bash
chmod -R 777 /your-directory
```

### 5. **Additional Debugging Tips**  
Sometimes, containers might not start or might throw errors related to Hadoop configuration. A small troubleshooting section or references to common issues (e.g., insufficient memory for Hadoop) would be helpful.

### 6. **Final Output File Path**  
The output of the WordCount job will be written to `/user/root/output/` in HDFS. This is clearly explained, but you could also include a note that the output directory might need to be created beforehand to avoid errors.

---

### **Example Additions:**

1. **Network Issues:**
   ```
   If you can't access the NameNode UI, ensure that your Docker container's ports are correctly exposed. For example, if you're running a local machine, the UI should be accessible via http://localhost:9870.
   ```
   
2. **Stopping Containers:**
   ```bash
   docker-compose down  # Stop and remove the containers
   ```

3. **Permissions Fix:**
   ```bash
   docker exec -it namenode bash
   chmod -R 777 /your-directory  # If you face any permission errors
   ```

4. **Handling HDFS Directory Creation:**
   If `hdfs dfs -mkdir` gives an error, it may be because the directory already exists. Consider adding:
   ```bash
   hdfs dfs -rm -r /user/root/input  # If the directory exists, remove it first
   hdfs dfs -mkdir /user/root/input
   ```

---

😊 References

https://data-flair.training/blogs/top-hadoop-hdfs-commands-tutorial/

https://hadoop.apache.org/docs/stable/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html

https://medium.com/@traininghub.io/hadoop-mapreduce-architecture-7e167e264595

