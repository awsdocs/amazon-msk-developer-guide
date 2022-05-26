# Step 2: Create a Client Machine<a name="create-client-machine"></a>

In this step of [Getting Started Using Amazon MSK](getting-started.md), you create a client machine\. You use this client machine to create a topic that produces and consumes data\. For simplicity, we'll put this client machine in the same VPC as the MSK cluster\. But a client machine doesn't have to be in the same VPC as the cluster\.

**To create a client machine**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch instances**\.

1. Choose **Select** to create an instance of **Amazon Linux 2 AMI \(HVM\) \- Kernel 5\.10, SSD Volume Type**\.

1. Choose the **t2\.xlarge** instance type by selecting the check box next to it\.

1. Choose **Next: Configure Instance Details**\.

1. In the **Network** list, choose the VPC whose ID you saved in [Step 1: Create an Amazon MSK Cluster](create-cluster.md)\.

1. In the **Auto\-assign Public IP** list, choose **Enable**\.

1. In the menu near the top, choose **Add Tags**\.

1. Choose **Add Tag**\.

1. Enter **Name** for the **Key** and **MSKTutorialClient** for the **Value**\.

1. Choose **Review and Launch**, and then choose **Launch**\.

1. Choose **Create a new key pair**, enter **MSKKeyPair** for **Key pair name**, and then choose **Download Key Pair**\. Alternatively, you can use an existing key pair if you prefer\.

1. Read the acknowledgement, select the check box next to it, and choose **Launch Instances**\.

1. Choose **View Instances**\. Then, in the **Security Groups** column, choose the security group that is associated with the **MSKTutorialClient** instance\.

1. Copy the name of the security group, and save it for later\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\. Find the security group whose ID you saved in [Step 1: Create an Amazon MSK Cluster](create-cluster.md)\. Choose this row by selecting the check box in the first column\.

1. In the **Inbound Rules** tab, choose **Edit inbound rules**\.

1. Choose **Add rule**\.

1. In the new rule, choose **All traffic** in the **Type** column\. In the second field in the **Source** column, select the security group of the client machine\. This is the group whose name you saved earlier in this step\.

1. Choose **Save rules**\. Now the cluster's security group can accept traffic that comes from the client machine's security group\.

**Next Step**

[Step 3: Create a Topic](create-topic.md)