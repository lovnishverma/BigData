### **Complete Guide to Install and Use Sqoop with MySQL in Docker**

This guide walks you through the complete steps to install and set up **Sqoop** and **MySQL** in a Docker container, including a demo project to test the integration.

---

### **Prerequisites:**
1. **Docker** installed and running on your machine.
2. **Java 8 or higher** installed in your Docker container.
3. **Sqoop Tarball** (`sqoop-1.99.6-bin-hadoop200.tar.gz`) available locally. Download it from: [Sqoop Tarball](https://archive.apache.org/dist/sqoop/1.99.6/sqoop-1.99.6-bin-hadoop200.tar.gz)
4. **MySQL** installed on the Docker container (or a separate MySQL instance running).

---

### **Step 1: Set Up Docker Environment**

#### 1.1. **Create a new Docker container:**

Create a new Docker container using Ubuntu as the base image.

```bash
docker run -it --name hadoop-sqoop ubuntu:20.04 /bin/bash
```

#### 1.2. **Update and Install Java:**

Inside the Docker container, install **Java 8** and required dependencies.

```bash
apt-get update
apt-get install openjdk-8-jdk -y
```

---

### **Step 2: Install MySQL in Docker (If Not Installed)**

If MySQL is not already installed, you can follow these steps to install and configure MySQL in your Docker container.

#### 2.1. **Install MySQL:**

Inside the Docker container, run the following commands to install MySQL:

```bash
apt-get update
apt-get install mysql-server -y
```

#### 2.2. **Start MySQL Server:**

Start the MySQL service inside the container:

```bash
service mysql start
```

You may encounter warnings related to user configurations (e.g., `cannot change directory to /nonexistent`). These can be ignored as they do not affect MySQL functionality.

#### 2.3. **Check MySQL Status:**

Verify if MySQL is running properly:

```bash
service mysql status
```

#### 2.4. **Configure MySQL:**

To set up MySQL and create a database and user, run the following commands:

```bash
mysql -u root -p
CREATE DATABASE testdb;
CREATE USER 'sqoop_user'@'%' IDENTIFIED BY 'password123';
GRANT ALL PRIVILEGES ON testdb.* TO 'sqoop_user'@'%';
FLUSH PRIVILEGES;
```

Replace `testdb`, `sqoop_user`, and `password123` with your preferred database name, username, and password.

---

### **Step 3: Upload and Install Sqoop**

#### 3.1. **Copy Sqoop Tarball to Docker Container:**

Use the following command to copy the Sqoop tarball (`sqoop-1.99.6-bin-hadoop200.tar.gz`) into the Docker container.

```bash
docker cp /path/to/sqoop-1.99.6-bin-hadoop200.tar.gz hadoop-sqoop:/tmp/
```

#### 3.2. **Extract Sqoop Tarball:**

Inside the Docker container, navigate to the `/tmp` directory and extract the Sqoop tarball:

```bash
cd /tmp
tar -xvzf sqoop-1.99.6-bin-hadoop200.tar.gz
```

#### 3.3. **Move Sqoop Directory:**

Move the extracted Sqoop directory to a more appropriate location (e.g., `/opt/sqoop`):

```bash
mv sqoop-1.99.6-bin-hadoop200 /opt/sqoop
```

#### 3.4. **Verify Sqoop Directory:**

Check if the `sqoop` directory has been moved successfully:

```bash
ls /opt/sqoop
```

---

### **Step 4: Set Environment Variables**

#### 4.1. **Edit `.bashrc` File:**

Edit the `.bashrc` file to set the environment variables for Java and Sqoop:

```bash
nano ~/.bashrc
```

Add the following lines at the end of the file:

```bash
export SQOOP_HOME=/opt/sqoop
export PATH=$SQOOP_HOME/bin:$PATH
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
```

#### 4.2. **Apply Changes:**

Run the following command to apply the changes made in `.bashrc`:

```bash
source ~/.bashrc
```

---

### **Step 5: Install Java (If Not Already Installed)**

If Java is not installed yet, use the following command:

```bash
apt-get install openjdk-8-jdk
```

#### 5.1. **Verify Java Installation:**

Check the Java version to confirm it is installed properly:

```bash
java -version
```

#### 5.2. **Set JAVA_HOME:**

If `JAVA_HOME` is not set, add the following to your `.bashrc`:

```bash
echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> ~/.bashrc
echo "export PATH=\$JAVA_HOME/bin:\$PATH" >> ~/.bashrc
```

Then apply the changes:

```bash
source ~/.bashrc
```

---

### **Step 6: Verify and Test the Sqoop Installation**

#### 6.1. **Verify Sqoop Version:**

To verify that Sqoop has been installed successfully, run:

```bash
sqoop version
```

This should output the version of Sqoop, confirming that it is correctly installed.

---

### **Step 7: Run Sqoop Commands**

#### 7.1. **List Databases in MySQL:**

Use Sqoop to connect to MySQL and list the databases:

```bash
sqoop list-databases --connect jdbc:mysql://localhost:3306 --username sqoop_user --password password123
```

Replace `sqoop_user` and `password123` with your configured MySQL username and password.

---

### **Step 8: Demo Project – Import Data from MySQL to HDFS**

Now that Sqoop is successfully set up, you can use it to import data from MySQL into Hadoop HDFS.

#### 8.1. **Import Data from MySQL Table:**

Suppose you have a table `employees` in the `testdb` database. To import the data into HDFS, run:

```bash
sqoop import --connect jdbc:mysql://localhost:3306/testdb --username sqoop_user --password password123 --table employees --target-dir /user/hadoop/employees
```

This will import the `employees` table into HDFS at `/user/hadoop/employees`.

---

### **Step 9: Troubleshooting**

#### 9.1. **Missing Executable Permission:**

If the `sqoop.sh` script is not running, ensure that it has the necessary executable permissions:

```bash
chmod +x /opt/sqoop/bin/sqoop.sh
```

#### 9.2. **MySQL Connection Issues:**

If Sqoop fails to connect to MySQL, ensure that:

- MySQL is running and accessible.
- The MySQL user has the correct permissions.
- The correct JDBC URL is used for the connection.

---

### **Step 10: Exit the Sqoop Interactive Shell**

To exit the Sqoop interactive shell, type:

```bash
exit
```

---

### **Final Notes**

This guide provides a complete step-by-step walkthrough for setting up Sqoop, MySQL, and Java inside a Docker container. After setting up the environment and verifying the installation, you should be able to use Sqoop to interact with databases and transfer data between various systems.

Feel free to modify the steps based on your environment and requirements. If you encounter any issues, refer to the troubleshooting section or ask for further help.
