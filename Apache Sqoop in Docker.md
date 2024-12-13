Here is the complete, properly formatted guide to install and set up Sqoop and MySQL in your Docker container:

---

# **Complete Guide to Install and Use Sqoop on Docker**

### **Prerequisites:**
1. **Docker** installed and running on your machine.
2. **Java 8 or higher** installed in your Docker container.
3. **Sqoop Tarball** (`sqoop-1.99.6-bin-hadoop200.tar.gz`) available locally. Download Link: https://archive.apache.org/dist/sqoop/1.99.6/sqoop-1.99.6-bin-hadoop200.tar.gz
4. **MySQL** installed on the Docker container (or a separate MySQL instance running).

---

### **Step 1: Prepare the Docker Environment**

#### 1.1. **Create a new Docker container**:

Create a new container from the official Ubuntu image:

```bash
docker run -it --name hadoop-sqoop ubuntu:20.04 /bin/bash
```

#### 1.2. **Install Java and Hadoop dependencies**:

Inside the container, run the following commands:

```bash
apt-get update
apt-get install openjdk-8-jdk -y
```

---

### **Step 2: Install MySQL in Docker (If Not Installed)**

If MySQL is not already installed on your Docker container, follow these steps to install it.

#### 2.1. **Install MySQL**:

Access the container shell and run the following commands to install MySQL:

```bash
docker exec -it hadoop-sqoop bash
apt-get update
apt-get install mysql-server
```

#### 2.2. **Start MySQL Server**:

After installation, start the MySQL server:

```bash
service mysql start
```

You may encounter a warning message related to the user configuration (`su: warning: cannot change directory to /nonexistent: No such file or directory`). This does not affect MySQL functionality.

#### 2.3. **Verify MySQL Status**:

Check MySQL server status:

```bash
service mysql status
```

#### 2.4. **Set Up MySQL User and Database**:

Access the MySQL shell and create a user and database:

```bash
mysql -u root -p
CREATE DATABASE testdb;
CREATE USER 'sqoop_user'@'%' IDENTIFIED BY 'password123';
GRANT ALL PRIVILEGES ON testdb.* TO 'sqoop_user'@'%';
FLUSH PRIVILEGES;
```

Replace `testdb`, `sqoop_user`, and `password123` with your preferred database name, username, and password.

---

### **Step 3: Upload the Sqoop Tarball to Docker Container**

#### 3.1. **Copy the Sqoop Tarball**:

Use the `docker cp` command to copy the Sqoop tarball to the `/tmp/` directory of the Docker container:

```bash
docker cp C:\Users\Dell\Downloads\sqoop-1.99.6-bin-hadoop200.tar.gz hadoop-sqoop:/tmp/
```

---

### **Step 4: Access the Docker Container**

#### 4.1. **Enter the Docker Container**:

Use the following command to access the Docker container’s shell:

```bash
docker exec -it hadoop-sqoop bash
```

---

### **Step 5: Extract the Sqoop Tarball**

#### 5.1. **Navigate to the `/tmp` Directory**:

Change to the `/tmp` directory where the tarball was copied:

```bash
cd /tmp/
```

#### 5.2. **Extract the Tarball**:

Use the `tar` command to extract the Sqoop tarball:

```bash
tar -xvzf sqoop-1.99.6-bin-hadoop200.tar.gz
```

#### 5.3. **Verify the Extraction**:

After extraction, you should see the `sqoop-1.99.6-bin-hadoop200` folder in the `/tmp/` directory.

---

### **Step 6: Move the Sqoop Directory**

#### 6.1. **Move the Extracted Sqoop Directory**:

Move the extracted `sqoop-1.99.6-bin-hadoop200` folder to `/opt` for better organization:

```bash
mv /tmp/sqoop-1.99.6-bin-hadoop200 /opt/sqoop
```

#### 6.2. **Verify the Move**:

Check if the folder is correctly moved:

```bash
ls /opt/sqoop
```

---

### **Step 7: Set Environment Variables**

#### 7.1. **Edit `.bashrc` to Add Sqoop Environment Variables**:

```bash
nano ~/.bashrc
```

Add the following lines to set the environment variables:

```bash
export SQOOP_HOME=/opt/sqoop
export PATH=$SQOOP_HOME/bin:$PATH
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
```

#### 7.2. **Source `.bashrc` to Apply Changes**:

```bash
source ~/.bashrc
```

---

### **Step 8: Install Java (If Not Already Installed)**

#### 8.1. **Install OpenJDK 8**:

If Java is not already installed, install OpenJDK 8:

```bash
apt-get install openjdk-8-jdk
```

#### 8.2. **Verify Java Installation**:

Check the Java version:

```bash
java -version
```

#### 8.3. **Set JAVA_HOME**:

If `JAVA_HOME` is not already set, add the following lines to `.bashrc`:

```bash
echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> ~/.bashrc
echo "export PATH=\$JAVA_HOME/bin:\$PATH" >> ~/.bashrc
```

Then, apply the changes:

```bash
source ~/.bashrc
```

---

### **Step 9: Test the Setup**

#### 9.1. **Verify Sqoop**:

After setting the environment variables, run:

```bash
sqoop version
```

You should see the Sqoop version information, confirming the installation.

---

### **Step 10: Running Sqoop Commands**

#### 10.1. **List Databases**:

To verify the connection to MySQL and list databases:

```bash
sqoop list-databases --connect jdbc:mysql://localhost:3306 --username root --password password123
```

---

### **Step 11: Troubleshooting**

#### 11.1. **Missing Executable Permission**:

If you encounter issues running the `sqoop.sh` script, ensure it's executable:

```bash
chmod +x /opt/sqoop/bin/sqoop.sh
```

---

### **Step 12: Exit the Sqoop Interactive Shell**

To exit the Sqoop interactive shell, type:

```bash
exit
```

This will return you to the regular terminal prompt.

---

This guide should help you successfully set up Sqoop, MySQL, and Java on your Docker container. If you encounter issues with specific steps, feel free to ask for further clarification.
