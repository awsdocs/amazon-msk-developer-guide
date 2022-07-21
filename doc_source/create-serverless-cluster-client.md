# Step 3: Create a client machine<a name="create-serverless-cluster-client"></a>

In the step, you perform two tasks\. The first task is to create an Amazon EC2 instance to use as an Apache Kafka client machine\. The second task is to install Java and Apache Kafka tools on the machine\.

**To create a client machine**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch instance**\.

1. Enter a descriptive **Name** for your client machine, such as **msk\-serverless\-tutorial\-client**\.

1. Leave the **Amazon Linux 2 AMI \(HVM\) \- Kernel 5\.10, SSD Volume Type** selected for **Amazon Machine Image \(AMI\) type**\.

1. Leave the **t2\.micro** instance type selected\.

1. Under **Key pair \(login\)**, choose **Create a new key pair**\. Enter **MSKServerlessKeyPair** for **Key pair name**\. Then choose **Download Key Pair**\. Alternatively, you can use an existing key pair\.

1. For **Network settings**, choose **Edit**\.

1. Under **VPC**, enter the ID of the virtual private cloud \(VPC\) for your serverless cluster \. This is the VPC based on the Amazon VPC service whose ID you saved after you created the cluster\.

1. For **Subnet**, choose the subnet whose ID you saved after you created the cluster\.

1. For **Firewall \(security groups\)**, select the security group associated with the cluster\. This value works if that security group has an inbound rule that allows traffic from the security group to itself\. With such a rule, members of the same security group can communicate with each other\. For more information, see [Security group rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules) in the Amazon VPC Developer Guide\.

1. Expand the **Advanced details** section and choose the IAM role that you created in [Step 2: Create an IAM role](create-iam-role.md)\.

1. Choose **Launch**\.

1. In the left navigation pane, choose **Instances**\. Then choose the check box in the row that represents your newly created Amazon EC2 instance\. From this point forward, we call this instance the *client machine*\.

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