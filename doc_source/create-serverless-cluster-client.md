# Step 3: Create a client machine<a name="create-serverless-cluster-client"></a>

In the step, you perform two tasks\. The first task is to create an Amazon EC2 instance to use as an Apache Kafka client machine\. The second task is to install Java and Apache Kafka tools on the machine\.

**To create a client machine**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch instance**\.

1. Choose the first **Select** button\.

1. Choose **Next: Configure Instance Details**\.

1. For **Network**, choose the serverless cluster's virtual private cloud \(VPC\)\. This is the VPC based on the Amazon VPC service whose ID you saved after you created the cluster\.

1. For **Subnet**, choose the subnet whose ID you saved after you created the cluster\.

1. For **IAM role**, choose `msk-serverless-tutorial`\.

1. Choose **Next: Add Storage**\.

1. Choose **Next: Add Tags**\.

1. Choose **Add Tag**, then under **Key** enter `Name` and under **Value** enter `msk-serverless-tutorial`\.

1. Choose **Next: Configure Security Group**\.

1. Choose the button labeled **Select an existing security group**\.

1. In the list of security groups, select the security group associated with the cluster\. This works if that security group has an inbound rule that allows traffic from the security group to itself\. Such a rule allows members of the same security group to communicate with each other\. For more information, see [Security group rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules) in the Amazon VPC Developer Guide\.

1. Choose **Review and Launch** and then choose **Launch**\.

1. Choose an existing key pair, or create a new key pair\.

1. Choose **Launch Instances**, and then choose **View Instances**\.

1. Choose the check box in the row that represents this newly created Amazon EC2 instance\. From this point forward, we call this instance the *client machine*\.

1. Choose **Connect** and follow the instructions to connect to the client machine\.

**To set up Apache Kafka client tools on the client machine**

1. To install Java, run the following command on the client machine:

   ```
   sudo yum -y install java-11
   ```

1. To get the Apache Kafka tools that we need to create topics and send data, run the following commands:

   ```
   wget https://archive.apache.org/dist/kafka/2.8.1/kafka_2.12-2.8.1.tgz
   ```

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

**Next Step**

[Step 4: Create an Apache Kafka topic](msk-serverless-create-topic.md)