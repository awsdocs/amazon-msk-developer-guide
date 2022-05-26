# Step 3: Create client machine and Apache Kafka topic<a name="mkc-create-topic"></a>

In this step you create an Amazon EC2 instance to use as an Apache Kafka client instance\. You then use this instance to create a topic on the cluster\.

**To create a client instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch instance**\.

1. Choose **Select** to create an instance of **Amazon Linux 2 AMI \(HVM\), SSD Volume Type**\.

1. Choose the **t2\.xlarge** instance type by selecting the check box next to it\.

1. Choose **Next: Configure Instance Details**\.

1. In the **Network** list, choose the same VPC whose name you saved when you created the cluster in [Step 1: Set up required resources](mkc-tutorial-setup.md)\.

1. In the **Auto\-assign Public IP** list, choose **Enable**\.

1. In the menu near the top, choose **5\. Add Tags**\.

1. Choose **Add Tag**\.

1. Enter **Name** for the **Key** and **mkc\-tutorial\-client** for the **Value**\.

1. Choose **Review and Launch**, and then choose **Launch**\.

1. In the first list, choose the option to **Create a new key pair**, enter **mkc\-tutorial\-key\-pair** for **Key pair name**, and then choose **Download Key Pair**\. Alternatively, you can use an existing key pair if you prefer\.

1. Choose **Launch Instances**\.

1. In the bottom right part of the screen, choose **View Instances**\. 

1. In the list of instances, find `mkc-tutorial-client`\. Choose it by selecting the box to its left\. Make sure no other instances are also selected\.

1. In the bottom half of the screen, choose the **Security** tab\.

1. Under **Security groups** copy the ID of the security group\. We use it in the following procedure\.

**To allow the newly created client to send data to the cluster**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left pane, under **SECURITY**, choose **Security Groups**\. In the **Security group ID** column, find the security group of the cluster\. You saved the ID of this security group when you created the cluster in [Step 1: Set up required resources](mkc-tutorial-setup.md)\. Choose this security group by selecting the box to the left of its row\. Make sure no other security groups are simultaneously selected\.

1. In the bottom half of the screen, choose the **Inbound rules** tab\.

1. Choose **Edit inbound rules**\.

1. In the bottom left of the screen, choose **Add rule**\.

1. In the new rule, choose **All traffic** in the **Type** column\. In the field to the right of the **Source** column, enter the ID of the security group of the client instance\. This is the security group ID that you saved after you created the client in the previous procedure\.

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

1. Copy the Apache ZooKeeper connection string that's under the label **Plaintext**\. Also copy the bootstrap servers string\. You need both of these strings in the following steps\.

1. Choose **Done**\.

1. Run the following command on the client instance \(`mkc-tutorial-client`\), replacing *ZookeeperConnectString* with the value that you saved when you viewed the cluster's client information\.

   ```
   bin/kafka-topics.sh --create --zookeeper ZookeeperConnectString --replication-factor 2 --partitions 1 --topic mkc-tutorial-topic
   ```

   If the command succeeds, you see the following message: Created topic mkc\-tutorial\-topic\.

**Next Step**

[Step 4: Create connector](mkc-create-connector.md)