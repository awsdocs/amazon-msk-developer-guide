# Getting the Apache ZooKeeper connection string for an Amazon MSK Cluster<a name="msk-get-connection-string"></a>

## Getting the Apache ZooKeeper connection string using the AWS Management Console<a name="get-connection-string-console"></a>

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. The table shows all the clusters for the current region under this account\. Choose the name of a cluster to view its description\.

1. On the **Cluster summary** page, choose **View client information**\. This shows you the bootstrap brokers, as well as the Apache ZooKeeper connection string\.

## Getting the Apache ZooKeeper connection string using the AWS CLI<a name="get-connection-string-cli"></a>

1. If you don't know the Amazon Resource Name \(ARN\) of your cluster, you can find it by listing all the clusters in your account\. For more information, see [Listing Amazon MSK clusters](msk-list-clusters.md)\.

1. To get the Apache ZooKeeper connection string, along with other information about your cluster, run the following command, replacing *ClusterArn* with the ARN of your cluster\. 

   ```
   aws kafka describe-cluster --cluster-arn ClusterArn
   ```

   The output of this `describe-cluster` command looks like the following JSON example\.

   ```
   {
       "ClusterInfo": {
           "BrokerNodeGroupInfo": {
               "BrokerAZDistribution": "DEFAULT",
               "ClientSubnets": [
                   "subnet-0123456789abcdef0",
                   "subnet-2468013579abcdef1",
                   "subnet-1357902468abcdef2"
               ],
               "InstanceType": "kafka.m5.large",
               "StorageInfo": {
                   "EbsStorageInfo": {
                       "VolumeSize": 1000
                   }
               }
           },
           "ClusterArn": "arn:aws:kafka:us-east-1:111122223333:cluster/testcluster/12345678-abcd-4567-2345-abcdef123456-2",
           "ClusterName": "testcluster",
           "CreationTime": "2018-12-02T17:38:36.75Z",
           "CurrentBrokerSoftwareInfo": {
               "KafkaVersion": "2.2.1"
           },
           "CurrentVersion": "K13V1IB3VIYZZH",
           "EncryptionInfo": {
               "EncryptionAtRest": {
                   "DataVolumeKMSKeyId": "arn:aws:kms:us-east-1:555555555555:key/12345678-abcd-2345-ef01-abcdef123456"
               }
           },
           "EnhancedMonitoring": "DEFAULT",
           "NumberOfBrokerNodes": 3,
           "State": "ACTIVE",
           "ZookeeperConnectString": "10.0.1.101:2018,10.0.2.101:2018,10.0.3.101:2018"
       }
   }
   ```

   The previous JSON example shows the `ZookeeperConnectString` key in the output of the `describe-cluster` command\. Copy the value corresponding to this key and save it for when you need to create a topic on your cluster\.
**Important**  
Your Amazon MSK cluster must be in the `ACTIVE` state for you to be able to obtain the Apache ZooKeeper connection string\. When a cluster is still in the `CREATING` state, the output of the `describe-cluster` command doesn't include `ZookeeperConnectString`\. If this is the case, wait a few minutes and then run the `describe-cluster` again after your cluster reaches the `ACTIVE` state\.

## Getting the Apache ZooKeeper connection string using the API<a name="get-connection-string-api"></a>

To get the Apache ZooKeeper connection string using the API, see [DescribeCluster](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster)\.