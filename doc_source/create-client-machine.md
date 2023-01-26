# Step 3: Create a client machine<a name="create-client-machine"></a>

In this step of [Getting Started Using Amazon MSK](getting-started.md), you create a client machine\. You use this client machine to create a topic that produces and consumes data\. For simplicity, you'll create this client machine in the VPC that is associated with the MSK cluster so that the client can easily connect to the cluster\.

**To create a client machine**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch instances**\.

1. Enter a **Name** for your client machine, such as **MSKTutorialClient**\.

1. Leave **Amazon Linux 2 AMI \(HVM\) \- Kernel 5\.10, SSD Volume Type** selected for **Amazon Machine Image \(AMI\) type**\.

1. Leave the **t2\.micro** instance type selected\.

1. Under **Key pair \(login\)**, choose **Create a new key pair**\. Enter **MSKKeyPair** for **Key pair name**, and then choose **Download Key Pair**\. Alternatively, you can use an existing key pair\.

1. Expand the **Advanced details** section and choose the IAM role that you created in [Step 2: Create an IAM role](create-client-iam-role.md)\.

1. Choose **Launch instance**\.

1. Choose **View Instances**\. Then, in the **Security Groups** column, choose the security group that is associated with your new instance\. Copy the ID of the security group, and save it for later\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\. Find the security group whose ID you saved in [Step 1: Create an Amazon MSK cluster](create-cluster.md)\.

1. In the **Inbound Rules** tab, choose **Edit inbound rules**\.

1. Choose **Add rule**\.

1. In the new rule, choose **All traffic** in the **Type** column\. In the second field in the **Source** column, select the security group of your client machine\. This is the group whose name you saved after you launched the client machine instance\.

1. Choose **Save rules**\. Now the cluster's security group can accept traffic that comes from the client machine's security group\.

**Next Step**

[Step 4: Create a topic](create-topic.md)