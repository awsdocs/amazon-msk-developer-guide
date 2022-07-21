# Step 1: Create an MSK Serverless cluster<a name="create-serverless-cluster"></a>

In this step, you perform two tasks\. First, you create an MSK Serverless cluster with default settings\. Second, you gather information about the cluster\. This is information that you need in later steps when you create a client that can send data to the cluster\.

**To create a serverless cluster**

1. Sign in to the AWS Management Console, and open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home](https://console.aws.amazon.com/msk/home)\.

1. Choose **Create cluster**\.

1. For **Creation method**, leave the **Quick create** option selected\. The **Quick create** option lets you create a serverless cluster with default settings\.

1. For **Cluster name**, enter a descriptive name, such as **msk\-serverless\-tutorial\-cluster**\.

1. For **General cluster properties**, choose **Serverless** as the **Cluster type**\. Use the default values for the remaining **General cluster** properties\.

1. Note the table under **All cluster settings**\. This table lists the default values for important settings such as networking and availability, and indicates whether you can change each setting after you create the cluster\. To change a setting before you create the cluster, you should choose the **Custom create** option under **Creation method**\.
**Note**  
You can connect clients from up to five different VPCs with MSK Serverless clusters\. To help client applications switch over to another Availability Zone in the event of an outage, you must specify at least two subnets in each VPC\.

1. Choose **Create cluster**\.

**To gather information about the cluster**

1. In the **Cluster summary** section, choose **View client information**\. This button remains grayed out until Amazon MSK finishes creating the cluster\. You might need to wait a few minutes until the button becomes active so you can use it\.

1. Copy the string under the label **Endpoint**\. This is your bootstrap server string\.

1. Choose the **Properties** tab\.

1. Under the **Networking settings** section, copy the IDs of the subnets and the security group and save them because you need this information later to create a client machine\.

1. Choose any of the subnets\. This opens the Amazon VPC Console\. Find the ID of the Amazon VPC that is associated with the subnet\. Save this Amazon VPC ID for later use\.

**Next Step**

[Step 2: Create an IAM role](create-iam-role.md)