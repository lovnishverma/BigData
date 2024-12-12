Step 1:
Pull a Modern Hadoop Image: Use a maintained Hadoop Docker image like bde2020/hadoop-base.



Step 2:
docker pull bde2020/hadoop-namenode:latest

Run the Hadoop Container:

docker run -it --name hadoop-sqoop -p 50070:50070 -p 8088:8088 bde2020/hadoop-namenode:latest bash


Step 3:
Install Sqoop Manually in the Container: Follow the steps mentioned earlier to download and configure Apache Sqoop within the running container.
