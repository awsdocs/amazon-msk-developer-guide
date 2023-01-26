# Step 4: Create a topic<a name="create-topic"></a>

In this step of [Getting Started Using Amazon MSK](getting-started.md), you install Apache Kafka client libraries and tools on the client machine, and then you create a topic\.

**To create a topic on the client machine**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. Then select the check box beside the name of the client machine that you created in [Step 3: Create a client machine](create-client-machine.md)\.

1. Choose **Actions**, and then choose **Connect**\. Follow the instructions in the console to connect to your client machine\.

1. Install Java on the client machine by running the following command:

   ```
   sudo yum -y install java-11
   ```

1. Run the following command to download Apache Kafka\. 

   ```
   wget https://archive.apache.org/dist/kafka/2.8.1/kafka_2.12-2.8.1.tgz
   ```
**Note**  
If you want to use a mirror site other than the one used in this command, you can choose a different one on the [Apache](https://www.apache.org/dyn/closer.cgi?path=/kafka/1.1.1/kafka_2.11-1.1.1.tgz) website\.

1. Run the following command in the directory where you downloaded the TAR file in the previous step\.

   ```
   tar -xzf kafka_2.12-2.8.1.tgz
   ```

1. Go to the `kafka_2.12-2.8.1/libs` directory, then run the following command to download the Amazon MSK IAM JAR file\. The Amazon MSK IAM JAR makes it possible for the client machine to access the cluster\.

   ```
   wget https://github.com/aws/aws-msk-iam-auth/releases/download/v1.1.1/aws-msk-iam-auth-1.1.1-all.jar
   ```

1. Go to the `kafka_2.12-2.8.1/bin` directory\. Copy the following property settings and paste them into a new file\. Name the file `client.properties` and save it\.

   ```
   security.protocol=SASL_SSL
   sasl.mechanism=AWS_MSK_IAM
   sasl.jaas.config=software.amazon.msk.auth.iam.IAMLoginModule required;
   sasl.client.callback.handler.class=software.amazon.msk.auth.iam.IAMClientCallbackHandler
   ```

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Wait for the status of your cluster to become **Active**\. This might take several minutes\. After the status becomes **Active**, choose the cluster name\. This takes you to a page containing the cluster summary\.

1. Choose **View client information**\.

1. Copy the connection string for the Private endpoint\.

1. You will get 3 endpoints for each of the brokers. You will only need one for the following step.

1. Run the following command, replacing *BootstrapServerString* with one the broker endpoints that you obtained in the previous step\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-topics.sh --create --bootstrap-server BootstrapServerString --command-config client.properties --replication-factor 3 --partitions 1 --topic MSKTutorialTopic
   ```

   If the command succeeds, you see the following message: Created topic MSKTutorialTopic\.

**Next Step**

[Step 5: Produce and consume data](produce-consume.md)