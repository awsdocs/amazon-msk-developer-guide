# Step 3: Create client machine and Apache Kafka topic<a name="mkc-create-topic"></a>

In this step you create an Amazon EC2 instance to use as an Apache Kafka client instance\. You then use this instance to create a topic on the cluster\.

**To create a client machine**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch instances**\.

1. Enter a **Name** for your client machine, such as **mkc\-tutorial\-client**\.

1. Leave **Amazon Linux 2 AMI \(HVM\) \- Kernel 5\.10, SSD Volume Type** selected for **Amazon Machine Image \(AMI\) type**\.

1. Choose the **t2\.xlarge** instance type\.

1. Under **Key pair \(login\)**, choose **Create a new key pair**\. Enter **mkc\-tutorial\-key\-pair** for **Key pair name**, and then choose **Download Key Pair**\. Alternatively, you can use an existing key pair\.

1. Choose **Launch instance**\.

1. Choose **View Instances**\. Then, in the **Security Groups** column, choose the security group that is associated with your new instance\. Copy the ID of the security group, and save it for later\.

**To allow the newly created client to send data to the cluster**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left pane, under **SECURITY**, choose **Security Groups**\. In the **Security group ID** column, find the security group of the cluster\. You saved the ID of this security group when you created the cluster in [Step 1: Set up required resources](mkc-tutorial-setup.md)\. Choose this security group by selecting the box to the left of its row\. Make sure no other security groups are simultaneously selected\.

1. In the bottom half of the screen, choose the **Inbound rules** tab\.

1. Choose **Edit inbound rules**\.

1. In the bottom left of the screen, choose **Add rule**\.

1. In the new rule, choose **All traffic** in the **Type** column\. In the field to the right of the **Source** column, enter the ID of the security group of the client machine\. This is the security group ID that you saved after you created the client machine\.

1. Choose **Save rules**\. Your MSK cluster will now accept all traffic from the client you created in the previous procedure\.

**To create a topic**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the table of instances choose `mkc-tutorial-client`\.

1. Near the top of the screen, choose **Connect**, then follow the instructions to connect to the instance\.

1. Install Java on the client instance by running the following command:

   ```
   sudo yum install java-1.8.0
   ```

1. Run the following command to download Apache Kafka\. 

   ```
   wget https://archive.apache.org/dist/kafka/2.2.1/kafka_2.12-2.2.1.tgz
   ```
**Note**  
If you want to use a mirror site other than the one used in this command, you can choose a different one on the [Apache](https://www.apache.org/dyn/closer.cgi?path=/kafka/1.1.1/kafka_2.11-1.1.1.tgz) website\.

1. Run the following command in the directory where you downloaded the TAR file in the previous step\.

   ```
   tar -xzf kafka_2.12-2.2.1.tgz
   ```

1. Go to the **kafka\_2\.12\-2\.2\.1** directory\.

1. Open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home?region=us\-east\-1\#/home/](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/)\.

1. In the left pane choose **Clusters**, then choose the name `mkc-tutorial-cluster`\.

1. Choose **View client information**\.

1. Copy the **Plaintext** connection string\.

1. Choose **Done**\.

1. Run the following command on the client instance \(`mkc-tutorial-client`\), replacing *bootstrapServerString* with the value that you saved when you viewed the cluster's client information\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-topics.sh --create --bootstrap-server bootstrapServerString --replication-factor 2 --partitions 1 --topic mkc-tutorial-topic
   ```

   If the command succeeds, you see the following message: Created topic mkc\-tutorial\-topic\.

**Next Step**

[Step 4: Create connector](mkc-create-connector.md)