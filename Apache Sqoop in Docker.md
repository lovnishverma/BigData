Setting up MySQL on your Docker container. This guide assumes that you're using Docker with Sqoop, and it covers the installation of Sqoop, setting up MySQL, and using Sqoop for importing/exporting data.

---

# **Complete Guide to Install and Use Sqoop on Docker**

### **Prerequisites:**
1. **Docker** installed and running on your machine.
2. **Java 8 or higher** installed in your Docker container.
3. **Sqoop Tarball** (`sqoop-1.99.6-bin-hadoop200.tar.gz`) available locally.
4. **MySQL** installed on the Docker container (or a separate MySQL instance running).

---

### **Step 1: Prepare the Docker Environment**

Manually Set Up Hadoop and Sqoop in a New Container
Create a new container from an official Ubuntu image:

```bash
docker run -it --name hadoop-sqoop ubuntu:20.04 /bin/bash
```

Install Java and Hadoop:

Inside the container, install Java and Hadoop dependencies:

```bash
apt-get update
apt-get install openjdk-8-jdk -y #no need if you've updated already
```

### **Step 2: Install MySQL in Docker (If Not Installed)**

If MySQL is not already installed on your Docker container, follow these steps to install it.

1. **Install MySQL**:
   Access the container shell and run the following commands to install MySQL:

   ```bash
   docker exec -it hadoop-sqoop bash  #no need if you're already in the container
   apt-get update                     #no need if you've updated already
   apt-get install mysql-server
   ```

2. **Start MySQL Server**:
   After installation, start the MySQL server:

   ```bash
   service mysql start
   ```
The message su: warning: cannot change directory to /nonexistent: No such file or directory is a warning related to the default user configuration for MySQL. However, this warning doesn't affect the functionality of MySQL, and MySQL should be running.

To ensure that MySQL is working correctly, you can check status:
   
   ```bash
   service mysql status
   ```

3. **Set Up MySQL User and Database**:
   Access the MySQL shell and set up a user and database:

   ```bash
   mysql -u root -p
   CREATE DATABASE testdb;
   CREATE USER 'sqoop_user'@'%' IDENTIFIED BY 'password123';
   GRANT ALL PRIVILEGES ON testdb.* TO 'sqoop_user'@'%';
   FLUSH PRIVILEGES;
   ```

   - Replace `testdb`, `sqoop_user`, and `password123` with your preferred database name, username, and password.

---

### **Step 3: Upload the Sqoop Tarball to Docker Container**

1. **Copy the Sqoop Tarball from Your Local Machine to the Docker Container**:

   Use the `docker cp` command to copy the Sqoop tarball to the `/tmp/` directory of the Docker container:

   ```bash
   docker cp C:\Users\Dell\Downloads\sqoop-1.99.6-bin-hadoop200.tar.gz hadoop-sqoop:/tmp/
   ```
![image](https://github.com/user-attachments/assets/0b3f1f5a-7fea-474e-8d2e-a17687d605f3)

---

### **Step 4: Access the Docker Container**

1. **Enter the Docker Container**:

   Use the following command to access the Docker container's shell:

   ```bash
   docker exec -it hadoop-sqoop bash
   ```

---

### **Step 5: Extract the Sqoop Tarball**

1. **Navigate to the `/tmp` Directory**:
   Change to the `/tmp` directory where the tarball was copied:

   ```bash
   cd /tmp/
   ```

2. **Extract the Tarball**:

   Use the `tar` command to extract the Sqoop tarball:

   ```bash
   tar -xvzf sqoop-1.99.6-bin-hadoop200.tar.gz
   ```

3. **Verify the Extraction**:
   After extraction, you should see the `sqoop-1.99.6-bin-hadoop200` folder in the `/tmp/` directory.

---

### **Step 6: Move the Sqoop Directory**

1. **Move the Extracted Sqoop Directory to `/opt`**:

   Move the extracted `sqoop-1.99.6-bin-hadoop200` folder to `/opt` for better organization:

   ```bash
   mv /tmp/sqoop-1.99.6-bin-hadoop200 /opt/sqoop
   ```

2. **Verify the Move**:

   Check if the folder is correctly moved:

   ```bash
   ls /opt/sqoop
   ```
![image](https://github.com/user-attachments/assets/fd288081-38c3-4378-8c1d-53b91e23b1c9)

---

### **Step 7: Set Environment Variables**

1. **Edit `.bashrc` to Add Sqoop Environment Variables**:

If you'd prefer to use nano, you can install it using the following steps:

Install nano:
```bash
apt-get update
apt-get install nano
```
   Open the `.bashrc` file to add the necessary environment variables for Sqoop:

   ```bash
   nano ~/.bashrc
   ```

2. **Add the Following Lines** at the end of the file:

   ```bash
   export SQOOP_HOME=/opt/sqoop
   export PATH=$SQOOP_HOME/bin:$PATH
   ```

3. **Save and Exit**:
   - Press `Ctrl+O` to save.
   - Press `Enter` to confirm.
   - Press `Ctrl+X` to exit the editor.

4. **Apply the Changes**:

   Reload the `.bashrc` file to apply the environment variable changes:

   ```bash
   source ~/.bashrc
   ```
![image](https://github.com/user-attachments/assets/e7cfb266-ddad-4f04-a23a-47d82b355aa2)

---

### **Step 8: Install Java (if not already installed)**

If Java 8 is not already installed, install it using the following steps:

1. **Install OpenJDK 8**:

   ```bash
   apt-get install openjdk-8-jdk
   ```

2. **Verify Java Installation**:

   Check the Java version to ensure it’s installed:

   ```bash
   java -version
   ```

---

### **Step 9: Verify the Sqoop Installation**

Verify that Sqoop is installed correctly:

1. **Check the Sqoop Version**:

   Run the following command to check if Sqoop is working:

   ```bash
   sqoop version
   ```

   If everything is installed correctly, you should see Sqoop's version information.

---

### **Step 10: Use Basic Sqoop Commands**

You can now use Sqoop to import and export data between MySQL and HDFS. Below are some basic commands for importing and exporting data.

#### **1. Import Data from MySQL to HDFS**

To import data from a MySQL table into HDFS:

```bash
sqoop import --connect jdbc:mysql://localhost:3306/testdb \
--username sqoop_user --password password123 \
--table employee --target-dir /user/hadoop/employee_data
```

This command imports the `employee` table from the `testdb` database into the HDFS directory `/user/hadoop/employee_data`.

#### **2. Export Data from HDFS to MySQL**

To export data from HDFS back into a MySQL database:

```bash
sqoop export --connect jdbc:mysql://localhost:3306/testdb \
--username sqoop_user --password password123 \
--table employee --export-dir /user/hadoop/employee_data
```

This command exports the data stored in HDFS (`/user/hadoop/employee_data`) to the `employee` table in the `testdb` MySQL database.

#### **3. List Databases in MySQL**

To list all the databases in your MySQL server:

```bash
sqoop list-databases --connect jdbc:mysql://localhost:3306 \
--username sqoop_user --password password123
```

#### **4. List Tables in a Database**

To list all the tables in the `testdb` database:

```bash
sqoop list-tables --connect jdbc:mysql://localhost:3306/testdb \
--username sqoop_user --password password123
```

---

### **Step 11: Troubleshooting**

1. **Check Sqoop Logs**:

   If you encounter any issues, you can check the Sqoop logs for error messages:

   ```bash
   cat /opt/sqoop/sqoop-1.99.6/logs/sqoop*.log
   ```

2. **Ensure MySQL and Hadoop Services are Running**:

   Make sure both MySQL and the Hadoop services (like HDFS and YARN) are running.

3. **Verify Connection Details**:

   Double-check that the JDBC URL and credentials for MySQL are correct.

---

### **Summary of Commands**

- **Upload Sqoop Tarball to Docker Container**:

  ```bash
  docker cp C:\Users\Dell\Downloads\sqoop-1.99.6-bin-hadoop200.tar.gz hadoop-sqoop:/tmp/
  ```

- **Extract the Tarball**:

  ```bash
  tar -xvzf /tmp/sqoop-1.99.6-bin-hadoop200.tar.gz
  ```

- **Move Sqoop to `/opt` Directory**:

  ```bash
  mv /tmp/sqoop-1.99.6 /opt/sqoop
  ```

- **Set Environment Variables**:

  ```bash
  nano ~/.bashrc
  export SQOOP_HOME=/opt/sqoop
  export PATH=$SQOOP_HOME/bin:$PATH
  source ~/.bashrc
  ```

- **Install Java**:

  ```bash
  apt-get install openjdk-8-jdk
  ```

- **Sqoop Commands**:
  - **Import Data**:

    ```bash
    sqoop import --connect jdbc:mysql://localhost:3306/testdb --username sqoop_user --password password123 --table employee --target-dir /user/hadoop/employee_data
    ```

  - **Export Data**:

    ```bash
    sqoop export --connect jdbc:mysql://localhost:3306/testdb --username sqoop_user --password password123 --table employee --export-dir /user/hadoop/employee_data
    ```

This guide should help you set up and use Sqoop efficiently with a MySQL database and Hadoop in a Docker container.
