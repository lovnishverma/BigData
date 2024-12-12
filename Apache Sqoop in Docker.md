**Apache Scoop**
Step 1:
Pull a Modern Hadoop Image: Use a maintained Hadoop Docker image like bde2020/hadoop-base.

Step 2:
**docker pull bde2020/hadoop-namenode:latest**

Run the Hadoop Container:

**docker run -it --name hadoop-sqoop -p 50070:50070 -p 8088:8088 bde2020/hadoop-namenode:latest bash**


Step 3:
Install Sqoop Manually in the Container: Follow the steps mentioned earlier to download and configure Apache Sqoop within the running container.

Download Method (Manual Download)
If the above steps don't work, consider downloading the file on your local machine and then transferring it to the container using docker cp as follows:

Step 1: Download the file locally.
Download the Sqoop binary file to your local machine.

Step 2: Copy the file to your Docker container.
Once downloaded, use the docker cp command to copy the file into your container:


**docker cp /path/to/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz hadoop-sqoop:/tmp/**
Make sure to replace /path/to/ with the actual path to the file on your local machine.

Step 4: Extract the File.
Then, log back into your container and attempt to extract the file:

**tar -xvzf /tmp/sqoop-1.4.7.bin__hadoop-2.6.0.tar.**gz
