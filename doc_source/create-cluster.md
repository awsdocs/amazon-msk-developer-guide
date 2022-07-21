# Step 1: Create an Amazon MSK cluster<a name="create-cluster"></a>

In this step of [Getting Started Using Amazon MSK](getting-started.md), you create an Amazon MSK cluster\.

**To create an Amazon MSK cluster using the AWS Management Console**

1. Sign in to the AWS Management Console, and open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home?region=us\-east\-1\#/home/](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/)\.

1. Choose **Create cluster**\.

1. For **Creation method**, leave the **Quick create** option selected\. The **Quick create** option lets you create a cluster with default settings\.

1. For **Cluster name**, enter a descriptive name for your cluster\. For example, **MSKTutorialCluster**\.

1. For **General cluster properties**, choose **Provisioned** as the **Cluster type**\.

1. From the table under **All cluster settings**, copy the values of the following settings and save them because you need them later in this tutorial:
   + VPC
   + Subnets
   + Security groups associated with VPC

1. Choose **Create cluster**\.

1. Check the cluster **Status** on the **Cluster summary** page\. The status changes from **Creating** to **Active** as Amazon MSK provisions the cluster\. When the status is **Active**, you can connect to the cluster\. For more information about cluster status, see [Cluster states](msk-cluster-states.md)\.

**Next Step**

[Step 2: Create a client machine](create-client-machine.md)