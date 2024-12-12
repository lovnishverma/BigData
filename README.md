# BigData
# Hadoop-on-Docker
 This repo will help you how to install hadoop on docker container

# Pre-requisite:
                                                                          
-> Git        Link:  https://git-scm.com/downloads/win                                                                        
-> Docker     Link:  https://docs.docker.com/desktop/setup/install/windows-install/

# Steps to follow:
By Following these steps you will able to setup the hadoop setup on docker container                                    
                                                                                                                      
Step 1: Clone the "BigData" repository from GitHub using the following command:                                   
git clone https://github.com/lovnishverma/BigData.git                                          

Step 2: cd BigData

Step 3: 
docker-compose up -d

Step 4: 
docker container ls

Step 5:
docker exec -it namenode /bin/bash


![image](https://github.com/user-attachments/assets/7b010ecc-f37e-4006-857d-839315304c93)



# Running Hadoop Code:

Step 1: Copy the code folder on docker conatiner by running this command on the terminal (opened in the folder where you have cloned the repo):

cd BigData

docker cp code namenode:/    
![image](https://github.com/user-attachments/assets/9b29c002-63e2-4269-a85d-e8be401fb6f6)

                                                                                                                        
Step 2: Then go into Hadoop_Code directory and further into input directory from where you have to copy the data.txt file
                                                                                                                             
Step 3: Create some directories in hadoop file system by following command:                                                 
      -> hdfs dfs -mkdir /user                                                                       
      -> hdfs dfs -mkdir /user/root                                                                                                 
      -> hdfs dfs -mkdir /user/root/input   

![image](https://github.com/user-attachments/assets/ec2d4bbf-29a6-404d-af4b-304668b4fde3)

                                                                                                                                                      
Step 4: Copy the data.txt to the input directory (user/root/input) created in hadoop file system by following command:                   
      -> hdfs dfs -put data.txt /user/root/input   
![image](https://github.com/user-attachments/assets/8f8fd7f3-937a-42c9-b3f0-41279a993520)

                                                                                                                                          
Step 5: Return back to directory where wordCount.jar file is located:                                                    
      -> cd ../
      
 ![image](https://github.com/user-attachments/assets/9ce4fff2-1419-441e-8914-61d066884154)
                                                                                                                                
Step 6: Then execute the jar file by following command:                                                                        
      -> hadoop jar wordCount.jar org.apache.hadoop.examples.WordCount input output    
  ![image](https://github.com/user-attachments/assets/3674da9c-fe7a-4d4d-948e-7b28bf3a0f50)

                                                                                                                                                                   
Step 7: Display the output usind this command:                                                                              
      -> hdfs dfs -cat /user/root/output/*
      
