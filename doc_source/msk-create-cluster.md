# Creating an Amazon MSK cluster<a name="msk-create-cluster"></a>

**Important**  
You can't change the VPC for an Amazon MSK cluster after you create the cluster\.

Before you can create an Amazon MSK cluster you need to have an Amazon Virtual Private Cloud \(VPC\) and set up subnets within that VPC\.

You need two subnets in two different Availability Zones in the US West \(N\. California\) Region\. In all other Regions where Amazon MSK is available, you can specify either two or three subnets\. Your subnets must all be in different Availability Zones\. When you create a cluster, Amazon MSK distributes the broker nodes evenly over the subnets that you specify\.

## Broker types<a name="broker-instance-types"></a>

When you create an Amazon MSK cluster, you specify the type of brokers that you want it to have\. Amazon MSK supports the following broker types:
+ kafka\.t3\.small
+ kafka\.m5\.large, kafka\.m5\.xlarge, kafka\.m5\.2xlarge, kafka\.m5\.4xlarge, kafka\.m5\.8xlarge, kafka\.m5\.12xlarge, kafka\.m5\.16xlarge, kafka\.m5\.24xlarge

M5 brokers have higher baseline throughput performance than T3 brokers and are recommended for production workloads\. M5 brokers can also have more partitions per broker than T3 brokers\. Use M5 brokers if you are running larger production\-grade workloads or require a greater number of partitions\. To learn more about M5 instance types, see [Amazon EC2 M5 Instances](https://aws.amazon.com/ec2/instance-types/m5/)\.

T3 brokers have the ability to use CPU credits to temporarily burst performance\. Use T3 brokers for low\-cost development, if you are testing small to medium streaming workloads, or if you have low\-throughput streaming workloads that experience temporary spikes in throughput\. We recommend that you run a proof\-of\-concept test to determine if T3 brokers are sufficient for production or critical workload\. To learn more about T3 instance types, see [Amazon EC2 T3 Instances](https://aws.amazon.com/ec2/instance-types/t3/)\.

For more information on how to choose broker types, see [Best practices](bestpractices.md)\.

## Creating a cluster using the AWS Management Console<a name="create-cluster-console"></a>

****

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose **Create cluster**\.

1. Specify a name for the cluster\. 

1. In the VPC list, choose the VPC you want to use for the cluster\. You can also specify which version of Apache Kafka you want Amazon MSK to use to create the cluster\.

1. Specify two subnets if you're using one of the following Regions: South America \(São Paulo\), Canada \(Central\), and US West \(N\. California\)\. In other Regions where Amazon MSK is available, you can specify either two or three subnets\. The subnets that you specify must be in different Availability Zones\. 

1. Choose the kind of configuration you want\. For information about MSK configurations, see [Amazon MSK configuration](msk-configuration.md)\.

1. Specify the type and number of brokers you want MSK to create in each Availability Zone\. The minimum is one broker per Availability Zone and the maximum is 30 brokers per cluster\.

1. \(Optional\) Assign tags to your cluster\. Tags are optional\. For more information, see [Tagging an Amazon MSK cluster](msk-tagging.md)\.

1. You can adjust the storage volume per broker\. After you create the cluster, you can increase the storage volume per broker but you can't decrease it\.

1. Choose the settings you want for encrypting data in transit\. By default, MSK encrypts data as it transits between brokers within a cluster\. If you don't want to encrypt data as it transits between brokers, clear the check box labeled **Enable encryption within the cluster**\.

1. Choose one of the three settings for encrypting data as it transits between clients and brokers\. For more information, see [Encryption in transit](msk-encryption.md#msk-encryption-in-transit)\.

1. Choose the kind of KMS key that you want to use for encrypting data at rest\. For more information, see [Encryption at rest](msk-encryption.md#msk-encryption-at-rest)\.

1. If you want to authenticate the identity of clients, choose **Enable TLS client authentication** by selecting the box next to it\. For more information about authentication, see [Mutual TLS authentication](msk-authentication.md)\.

1. Choose the monitoring level you want\. This determines the set of metrics you get\. For more information, see [Monitoring an Amazon MSK cluster](monitoring.md)\.

1. \(Optional\) Choose **Advanced settings**, and then choose **Customize settings**\. You can specify one or more security groups that you want to give access to your cluster \(for example, the security groups of client machines\)\. If you specify security groups that were shared with you, you must ensure that you have permissions to them\. Specifically, you need the `ec2:DescribeSecurityGroups` permission\. For an example, see [Amazon EC2: Allows Managing EC2 Security Groups Associated With a Specific VPC, Programmatically and in the Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_securitygroups-vpc.html)\.

1. Choose **Create cluster**\.

1. Check the cluster **Status** on the **Cluster summary** page\. The status changes from **Creating** to **Active** as Amazon MSK provisions the cluster\. When the status is **Active**, you can connect to the cluster\. For more information about cluster status, see [Cluster states](msk-cluster-states.md)\.

## Creating a cluster using the AWS CLI<a name="create-cluster-cli"></a>

****

1. Copy the following JSON and save it to a file\. Name the file `brokernodegroupinfo.json`\. Replace the subnet IDs in the JSON with the values that correspond to your subnets\. These subnets must be in different Availability Zones\. Replace *"Security\-Group\-ID"* with the ID of one or more security groups of the client VPC\. Clients associated with these security groups get access to the cluster\. If you specify security groups that were shared with you, you must ensure that you have permissions to them\. Specifically, you need the `ec2:DescribeSecurityGroups` permission\. For an example, see [Amazon EC2: Allows Managing EC2 Security Groups Associated With a Specific VPC, Programmatically and in the Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_securitygroups-vpc.html)\. Finally, save the updated JSON file on the computer where you have the AWS CLI installed\.

   ```
   {
     "InstanceType": "kafka.m5.large",
     "ClientSubnets": [
       "Subnet-1-ID",
       "Subnet-2-ID"
     ],
     "SecurityGroups": [
       "Security-Group-ID"
     ]
   }
   ```
**Important**  
Specify exactly two subnets if you are using one of the following Regions: South America \(São Paulo\), Canada \(Central\), and US West \(N\. California\)\. For other Regions where Amazon MSK is available, you can specify either two or three subnets\. The subnets that you specify must be in distinct Availability Zones\. When you create a cluster, Amazon MSK distributes the broker nodes evenly across the subnets that you specify\.

1. Run the following AWS CLI command in the directory where you saved the `brokernodegroupinfo.json` file, replacing *"Your\-Cluster\-Name"* with a name of your choice\. For *"Monitoring\-Level"*, you can specify one of the following three values: `DEFAULT`, `PER_BROKER`, or `PER_TOPIC_PER_BROKER`\. For information about these three different levels of monitoring, see [Monitoring an Amazon MSK cluster](monitoring.md)\. The `enhanced-monitoring` parameter is optional\. If you don't specify it in the `create-cluster` command, you get the `DEFAULT` level of monitoring\.

   ```
   aws kafka create-cluster --cluster-name "Your-Cluster-Name" --broker-node-group-info file://brokernodegroupinfo.json --kafka-version "2.2.1" --number-of-broker-nodes 3 --enhanced-monitoring "Monitoring-Level"
   ```

   The output of the command looks like the following JSON:

   ```
   {
       "ClusterArn": "...",
       "ClusterName": "AWSKafkaTutorialCluster",
       "State": "CREATING"
   }
   ```
**Note**  
The `create-cluster` command might return an error stating that one or more subnets belong to unsupported Availability Zones\. When this happens, the error indicates which Availability Zones are unsupported\. Create subnets that don't use the unsupported Availability Zones and try the `create-cluster` command again\.

1. Save the value of the `ClusterArn` key because you need it to perform other actions on your cluster\.

1. Run the following command to check your cluster `STATE`\. The `STATE` value changes from `CREATING` to `ACTIVE` as Amazon MSK provisions the cluster\. When the state is `ACTIVE`, you can connect to the cluster\. For more information about cluster status, see [Cluster states](msk-cluster-states.md)\.

   ```
   aws kafka describe-cluster --cluster-arn <your-cluster-ARN>
   ```

## Creating a cluster with a custom MSK configuration using the AWS CLI<a name="create-cluster-cli-custom-config"></a>

****

For information about custom MSK configurations and how to create them, see [Amazon MSK configuration](msk-configuration.md)\.

1. Save the following JSON to a file, replacing *configuration\-arn* with the ARN of the configuration that you want to use to create the cluster\.

   ```
   {
       "Arn": configuration-arn,
       "Revision": 1
   }
   ```

1. Run the `create-cluster` command and use the `configuration-info` option to point to the JSON file you saved in the previous step\. The following is an example\.

   ```
   aws kafka create-cluster --cluster-name ExampleClusterName --broker-node-group-info file://brokernodegroupinfo.json --kafka-version "1.1.1" --number-of-broker-nodes 3 --enhanced-monitoring PER_TOPIC_PER_BROKER --configuration-info file://configuration.json
   ```

   The following is an example of a successful response after running this command\.

   ```
   {
       "ClusterArn": "arn:aws:kafka:us-east-1:123456789012:cluster/CustomConfigExampleCluster/abcd1234-abcd-dcba-4321-a1b2abcd9f9f-2",
       "ClusterName": "CustomConfigExampleCluster",
       "State": "CREATING"
   }
   ```

## Creating a cluster using the API<a name="create-cluster-api"></a>

To create a cluster using the API, see [CreateCluster](https://docs.aws.amazon.com//msk/1.0/apireference/clusters.html#CreateCluster)\.