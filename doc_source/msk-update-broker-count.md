# Expanding an Amazon MSK Cluster<a name="msk-update-broker-count"></a>

Use this Amazon MSK operation when you want to increase the number of brokers in your MSK cluster\. To expand a cluster, make sure that it is in the `ACTIVE` state\.

**Important**  
If you want to expand an MSK cluster, make sure to use this Amazon MSK operation \. Don't try to add brokers to a cluster without using this operation\.

For information about how to rebalance partitions after you add brokers to a cluster, see [Reassign partitions](bestpractices.md#bestpractices-balance-cluster)\.

## Expanding a cluster using the AWS Management Console<a name="expand-cluster-console"></a>

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose the MSK cluster whose number of brokers you want to increase\.

1. On the cluster details page, choose the **Edit** button next to the **Cluster\-Level Broker Details** heading\.

1. Enter the number of brokers that you want the cluster to have per Availability Zone and then choose **Save changes**\.

## Expanding a cluster using the AWS CLI<a name="expand-cluster-cli"></a>

1. Run the following command, replacing *ClusterArn* with the Amazon Resource Name \(ARN\) that you obtained when you created your cluster\. If you don't have the ARN for your cluster, you can find it by listing all clusters\. For more information, see [Listing Amazon MSK clusters](msk-list-clusters.md)\.

   Replace *Current\-Cluster\-Version* with the current version of the cluster\. 
**Important**  
Cluster versions aren't simple integers\. To find the current version of the cluster, use the [DescribeCluster](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster) operation or the [describe\-cluster](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kafka/describe-cluster.html) AWS CLI command\. An example version is `KTVPDKIKX0DER`\.

   The *Target\-Number\-of\-Brokers* parameter represents the total number of broker nodes that you want the cluster to have when this operation completes successfully\. The value you specify for *Target\-Number\-of\-Brokers* must be a whole number that is greater than the current number of brokers in the cluster\. It must also be a multiple of the number of Availability Zones\.

   ```
   aws kafka update-broker-count --cluster-arn ClusterArn --current-version Current-Cluster-Version --target-number-of-broker-nodes Target-Number-of-Brokers
   ```

   The output of this `update-broker-count` operation looks like the following JSON\.

   ```
   {
       
       "ClusterArn": "arn:aws:kafka:us-east-1:012345678012:cluster/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2",
       "ClusterOperationArn": "arn:aws:kafka:us-east-1:012345678012:cluster-operation/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2/0123abcd-abcd-4f7f-1234-9876543210ef"
   }
   ```

1. To get the result of the `update-broker-count` operation, run the following command, replacing *ClusterOperationArn* with the ARN that you obtained in the output of the `update-broker-count` command\.

   ```
   aws kafka describe-cluster-operation --cluster-operation-arn ClusterOperationArn
   ```

   The output of this `describe-cluster-operation` command looks like the following JSON example\.

   ```
   {
       "ClusterOperationInfo": {
           "ClientRequestId": "c0b7af47-8591-45b5-9c0c-909a1a2c99ea",
           "ClusterArn": "arn:aws:kafka:us-east-1:012345678012:cluster/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2",
           "CreationTime": "2019-09-25T23:48:04.794Z",
           "OperationArn": "arn:aws:kafka:us-east-1:012345678012:cluster-operation/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2/0123abcd-abcd-4f7f-1234-9876543210ef",
           "OperationState": "UPDATE_COMPLETE",
           "OperationType": "INCREASE_BROKER_COUNT",
           "SourceClusterInfo": {
               "NumberOfBrokerNodes": 9
           },
           "TargetClusterInfo": {
               "NumberOfBrokerNodes": 12
           }
       }
   }
   ```

   In this output, `OperationType` is `INCREASE_BROKER_COUNT`\. If `OperationState` has the value `UPDATE_IN_PROGRESS`, wait a while, then run the `describe-cluster-operation` command again\. 

## Expanding a cluster using the API<a name="expand-cluster-api"></a>

To increase the number of brokers in a cluster using the API, see [UpdateBrokerCount](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn-nodes-count.html#UpdateBrokerCount)\.