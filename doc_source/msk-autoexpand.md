# Automatic scaling<a name="msk-autoexpand"></a>

You can configure Amazon Managed Streaming for Apache Kafka to automatically expand your cluster's storage in response to increased usage using Application Auto\-Scaling policies\. Your auto\-scaling policy sets the target disk utilization and the maximum scaling capacity\.

Before you use automatic scaling for Amazon MSK, you should consider the following:
+ 
**Important**  
A storage scaling action can occur only once every six hours\. 

  We recommend that you start with a right\-sized storage volume for your storage demands\. For guidance on right\-sizing your cluster, see [ Right\-size your cluster: Number of brokers per cluster](bestpractices.md#brokers-per-cluster)\.
+ Amazon MSK does not reduce cluster storage in response to reduced usage\. Amazon MSK does not support decreasing the size of storage volumes\. If you need to reduce the size of your cluster storage, you must migrate your existing cluster to a cluster with smaller storage\. For information about migrating a cluster, see [Migration](migration.md)\.
+ Amazon MSK does not support auto scaling in the Asia Pacific \(Osaka\) and Africa \(Cape Town\) Regions\.

## MSK Storage auto\-expansion details<a name="msk-autoexpand-details"></a>

Your auto scaling policy defines the following parameters for your cluster:
+ **Storage Utilization Target**: The storage utilization threshold that Amazon MSK uses to trigger an auto\-scaling operation\. You can set the utilization target between 10% and 80% of the current storage capacity\. We recommend that you set the Storage Utilization Target between 50% and 60%\.
+ **Maximum Storage Capacity**: This setting is the maximum scaling limit that Amazon MSK can set your broker storage\. You can set the maximum storage capacity up to 16 TiB per broker\. For more information, see [Amazon MSK Quota](limits.md)\.

When the service detects that your `Maximum Disk Utilization` metric is equal to or greater than the `Storage Utilization Target` setting, it increases your storage capacity by an amount equal to the larger of two numbers: 10 GiB and 10% of current storage\. For example, if you have 1000 GiB, that amount is 100 GiB\. The service checks your storage utilization every minute\. Further scaling operations continue to increase storage by an amount equal to the larger of two numbers: 10 GiB and 10% of current storage\.

You can check to determine if auto scaling operations occurred using the [ ListClusterOperations](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn-operations.html#ListClusterOperations) operation\.

## Setting up auto\-expansion for your Amazon MSK cluster<a name="msk-autoexpand-setup"></a>

You can use the Amazon MSK console, the Amazon MSK API, or AWS CloudFormation to implement auto\-expanding storage\. CloudFormation support is available through [Application Auto Scaling](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-applicationautoscaling-scalabletarget.html)\.

**Note**  
You can't implement auto\-expansion when you create a cluster\. You must first create the cluster, and then create and enable an auto\-expansion policy for it\. However, you can create the policy while the Amazon MSK service creates your cluster\.

### Setting up auto\-expansion using the AWS Management Console<a name="msk-autoexpand-setup-console"></a>

1. Sign in to the AWS Management Console, and open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home?region=us\-east\-1\#/home/](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/)\.

1. In the list of clusters, choose your cluster\. This takes you to the cluster's **Details** page\.

1. In the **Auto scaling for storage** section, choose **Configure**\.

1. Create and name an auto\-scaling policy\. Specify the storage utilization target, the maximum storage capacity, and the target metric\.

1. Choose `Save changes`\.

When you save and enable the new policy, the policy becomes active for the cluster\. Amazon MSK then expands the cluster's storage when the storage utilization target is reached\.

### Setting up auto\-expansion using the CLI<a name="msk-autoexpand-setup-cli"></a>

1. Use the [ RegisterScalableTarget](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/#available-commands) command to register a storage utilization target\.

1. Use the [ PutScalingPolicy](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/#available-commands) command to create an auto\-expansion policy\.

### Setting up auto\-expansion using the API<a name="msk-autoexpand-setup-api"></a>

1. Use the [ RegisterScalableTarget](https://docs.aws.amazon.com/autoscaling/application/APIReference/API_RegisterScalableTarget.html) API to register a storage utilization target\.

1. Use the [ PutScalingPolicy](https://docs.aws.amazon.com/autoscaling/application/APIReference/API_PutScalingPolicy.html) API to create an auto\-expansion policy\.