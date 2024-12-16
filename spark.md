### 1. **You Need to Specify Ports While Creating the Container?**

You need to specify the ports when creating the container, especially if you want to expose certain services to your host machine. For example, Spark has a web UI that runs on port `8080` by default, and the Spark master is accessible via port `7077`. 

Specifying the ports during the container creation ensures that you can access these services from your host machine. Here's why you need to do it:

- **8080** is the Spark UI (used to monitor Spark jobs).
- **7077** is the default port for the Spark master.
- **Additional ports** (like `2181`, `50070`, `50075`, etc.) are related to Hadoop ecosystem services (like HDFS, YARN, etc.), and you should specify them if you're running Hadoop alongside Spark in the container.

If you are working with **Spark-only** in the container (without Hadoop), then you might not need to specify Hadoop-related ports, but the Spark ports are necessary for communication with your Spark instance.

### 2. **You Don't Need to Install Hadoop?**

- **You don't need to install Hadoop if you're only using Spark**. 
  - Apache Spark can run independently of Hadoop and does not require a Hadoop cluster to operate. In fact, the Spark container can run in "standalone mode," where it doesn’t require Hadoop’s distributed storage (HDFS) or YARN resource manager.

- **However**, if you plan to run Spark with Hadoop (for example, using HDFS for distributed file storage or YARN for resource management), you'll need to install and configure Hadoop as well. 

Here’s a breakdown of the two options:
- **Spark in standalone mode (without Hadoop)**: You only need the Spark container, no need for Hadoop.
- **Spark with Hadoop**: You’ll need to run Hadoop services (like HDFS and YARN) alongside Spark, either within the same container or separate containers. In this case, you'll need to set up both Hadoop and Spark services together.

### Running Spark in Standalone Mode (without Hadoop):
If you just want to use Spark without Hadoop, the following steps should work:

1. **Pull Spark Docker image**:
   ```bash
   docker pull bitnami/spark:latest
   ```

2. **Run Spark container**:
   ```bash
   docker run -d --name spark-container -p 8080:8080 -p 7077:7077 bitnami/spark:latest
   ```

   Here, ports `8080` (Spark UI) and `7077` (Spark master port) are exposed.

3. **Access the Spark container**:
   ```bash
   docker exec -it spark-container bash
   ```

4. **Run Spark Shell or PySpark**:
   - Scala: `spark-shell`
   - Python: `pyspark`

### Running Spark with Hadoop (for HDFS and YARN):

If you intend to integrate Spark with Hadoop (for example, to use HDFS for file storage), you can use a pre-built Docker image that includes both Hadoop and Spark, or you can set up your own multi-container environment using Docker Compose. Here’s how you could go about this:

1. **Pull the Hadoop + Spark image** (e.g., `bde2020/hadoop-spark`).
   ```bash
   docker pull bde2020/hadoop-spark:latest
   ```

2. **Run the Hadoop + Spark container**:
   This container will have all the necessary services (Hadoop HDFS, YARN, and Spark).
   ```bash
   docker run -d --name hadoop-spark-container -p 8080:8080 -p 7077:7077 -p 9000:9000 -p 50070:50070 bde2020/hadoop-spark:latest
   ```

3. **Start the services** inside the container (Hadoop NameNode, ResourceManager, Spark, etc.):
   You might need to initialize HDFS or start the YARN ResourceManager manually depending on the image.

### Conclusion:
- **If you're just using Spark**, no need to install Hadoop. You can run Spark in standalone mode with just the Spark container and relevant ports.
- **If you want Spark to work with Hadoop**, then you'll need to set up Hadoop (HDFS, YARN) as well, either in a separate container or within the same container.

Let me know if you need more clarification or further assistance with any of these steps!
