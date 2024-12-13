Let’s start fresh and go through the steps step by step to properly install and configure Sqoop on your Docker container. This guide will also include MySQL installation and proper environment variable setup to ensure that everything works smoothly.

---

### **Fresh Start Guide: Install Sqoop, MySQL, and Java in Docker**

#### **Step 1: Create a Fresh Docker Container**

1. **Create a fresh container with Ubuntu:**

   Run the following command to create and start a new Ubuntu container:

   ```bash
   docker run -it --name sqoop-container ubuntu:20.04 /bin/bash
   ```

2. **Update package lists in the container:**

   Once inside the container, update the package lists:

   ```bash
   apt-get update
   ```

---

#### **Step 2: Install Java (OpenJDK 8)**

1. **Install OpenJDK 8:**

   Run the following command to install Java:

   ```bash
   apt-get install openjdk-8-jdk -y
   ```

2. **Verify the Java installation:**

   After installation, verify the Java version:

   ```bash
   java -version
   ```

   You should see output confirming the installation of Java 8.

3. **Set `JAVA_HOME`:**

   Set the `JAVA_HOME` environment variable. Open `.bashrc`:

   ```bash
   nano ~/.bashrc
   ```

   Add the following line to the end of the file:

   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   export PATH=$JAVA_HOME/bin:$PATH
   ```

   Save and exit by pressing `CTRL + X`, then `Y`, and then `Enter`.

4. **Apply the changes:**

   Run the following command to apply the changes:

   ```bash
   source ~/.bashrc
   ```

---

#### **Step 3: Install MySQL**

1. **Install MySQL server:**

   Run the following commands to install MySQL:

   ```bash
   apt-get install mysql-server -y
   ```

2. **Start MySQL service:**

   After installation, start the MySQL server:

   ```bash
   service mysql start
   ```

3. **Verify MySQL status:**

   You can check if MySQL is running using:

   ```bash
   service mysql status
   ```

   It should show that the MySQL server is active and running.

4. **Access MySQL:**

   To access the MySQL shell, run:

   ```bash
   mysql -u root -p
   ```

   Enter your MySQL root password. If you haven’t set one yet, you can leave the password blank.

5. **Create a test database and user:**

   In the MySQL shell, run the following commands to create a database and a user for Sqoop:

   ```sql
   CREATE DATABASE testdb;
   CREATE USER 'sqoop_user'@'%' IDENTIFIED BY 'password123';
   GRANT ALL PRIVILEGES ON testdb.* TO 'sqoop_user'@'%';
   FLUSH PRIVILEGES;
   ```

---

#### **Step 4: Install Sqoop**

1. **Download the Sqoop tarball:**

   If you haven’t already, download the Sqoop tarball (`sqoop-1.99.6-bin-hadoop200.tar.gz`) from the official Apache website:

   [Download Sqoop Tarball](https://archive.apache.org/dist/sqoop/1.99.6/sqoop-1.99.6-bin-hadoop200.tar.gz)

2. **Copy the Sqoop tarball to the Docker container:**

   Use `docker cp` to copy the Sqoop tarball to the `/tmp/` directory in the container:

   ```bash
   docker cp /path/to/sqoop-1.99.6-bin-hadoop200.tar.gz sqoop-container:/tmp/
   ```

   Replace `/path/to/sqoop-1.99.6-bin-hadoop200.tar.gz` with the actual path to the tarball on your local machine.

3. **Extract the tarball:**

   Inside the container, navigate to the `/tmp` directory and extract the tarball:

   ```bash
   cd /tmp
   tar -xvzf sqoop-1.99.6-bin-hadoop200.tar.gz
   ```

4. **Move Sqoop to a proper directory:**

   After extraction, move the Sqoop directory to `/opt/`:

   ```bash
   mv /tmp/sqoop-1.99.6-bin-hadoop200 /opt/sqoop
   ```

---

#### **Step 5: Set Environment Variables for Sqoop**

1. **Open `.bashrc` to set environment variables:**

   ```bash
   nano ~/.bashrc
   ```

2. **Add the following lines to set environment variables for Sqoop and Java:**

   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   export SQOOP_HOME=/opt/sqoop
   export PATH=$JAVA_HOME/bin:$SQOOP_HOME/bin:$PATH
   ```

3. **Save and exit:**

   Press `CTRL + X`, then `Y`, and press `Enter`.

4. **Apply the changes:**

   To apply the changes, run:

   ```bash
   source ~/.bashrc
   ```

---

#### **Step 6: Verify the Installation**

1. **Verify Java:**

   Check if Java is correctly installed:

   ```bash
   java -version
   ```

   It should show Java 8 information.

2. **Verify Sqoop:**

   Check if Sqoop is correctly installed by running:

   ```bash
   sqoop version
   ```

   This should show the Sqoop version information, confirming that Sqoop is installed correctly.

---

#### **Step 7: Test Sqoop with MySQL**

1. **List Databases using Sqoop:**

   To test the connection to MySQL, use the following Sqoop command to list databases:

   ```bash
   sqoop list-databases --connect jdbc:mysql://localhost:3306 --username root --password password123
   ```

   Replace `password123` with the actual password you set for MySQL root user.

   This should list the available databases in MySQL, including the `testdb` we created earlier.

---

### **Congratulations!**

If you followed all these steps correctly, Sqoop, Java, and MySQL should be installed and configured correctly on your Docker container. You should now be able to use Sqoop to interact with your MySQL database. Let me know if you encounter any further issues!
