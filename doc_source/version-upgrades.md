# Updating the Apache Kafka version<a name="version-upgrades"></a>

You can update an existing MSK cluster to a newer version of Apache Kafka\. You can't update it to an older version\. When you update the Apache Kafka version of an MSK cluster, also check your client\-side software to make sure its version enables you to use the features of the cluster's new Apache Kafka version\. Amazon MSK only updates the server software\. It doesn't update your clients\. 

For information about how to make a cluster highly available during an update, see [Build highly available clusters](bestpractices.md#ensure-high-availability)\.

**Important**  
You can't update the Apache Kafka version for an MSK cluster that exceeds the limits described in [ Right\-size your cluster: Number of partitions per broker](bestpractices.md#partitions-per-broker)\.

**Updating the Apache Kafka version using the AWS Management Console**

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose the MSK cluster on which you want to update the Apache Kafka version\.

1. On the **Properties** tab choose **Upgrade** in the **Apache Kafka version** section\.

**Updating the Apache Kafka version using the AWS CLI**

1. Run the following command, replacing *ClusterArn* with the Amazon Resource Name \(ARN\) that you obtained when you created your cluster\. If you don't have the ARN for your cluster, you can find it by listing all clusters\. For more information, see [Listing Amazon MSK clusters](msk-list-clusters.md)\.

   ```
   aws kafka get-compatible-kafka-versions --cluster-arn ClusterArn
   ```

   The output of this command includes a list of the Apache Kafka versions to which you can update the cluster\. It looks like the following example\.

   ```
   {
       "CompatibleKafkaVersions": [
           {
               "SourceVersion": "2.2.1",
               "TargetVersions": [
                   "2.3.1",
                   "2.4.1",
                   "2.4.1.1",
                   "2.5.1"
               ]
           }
       ]
   }
   ```

1. Run the following command, replacing *ClusterArn* with the Amazon Resource Name \(ARN\) that you obtained when you created your cluster\. If you don't have the ARN for your cluster, you can find it by listing all clusters\. For more information, see [Listing Amazon MSK clusters](msk-list-clusters.md)\.

   Replace *Current\-Cluster\-Version* with the current version of the cluster\. For *TargetVersion* you can specify any of the target versions from the output of the previous command\.
**Important**  
Cluster versions aren't simple integers\. To find the current version of the cluster, use the [DescribeCluster](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster) operation or the [describe\-cluster](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kafka/describe-cluster.html) AWS CLI command\. An example version is `KTVPDKIKX0DER`\.

   ```
   aws kafka update-cluster-kafka-version --cluster-arn ClusterArn --current-version Current-Cluster-Version --target-kafka-version TargetVersion
   ```

   The output of the previous command looks like the following JSON\.

   ```
   {
       
       "ClusterArn": "arn:aws:kafka:us-east-1:012345678012:cluster/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2",
       "ClusterOperationArn": "arn:aws:kafka:us-east-1:012345678012:cluster-operation/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2/0123abcd-abcd-4f7f-1234-9876543210ef"
   }
   ```

1. To get the result of the `update-cluster-kafka-version` operation, run the following command, replacing *ClusterOperationArn* with the ARN that you obtained in the output of the `update-cluster-kafka-version` command\.

   ```
   aws kafka describe-cluster-operation --cluster-operation-arn ClusterOperationArn
   ```

   The output of this `describe-cluster-operation` command looks like the following JSON example\.

   ```
   {
       "ClusterOperationInfo": {
           "ClientRequestId": "62cd41d2-1206-4ebf-85a8-dbb2ba0fe259",
           "ClusterArn": "arn:aws:kafka:us-east-1:012345678012:cluster/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2",
           "CreationTime": "2021-03-11T20:34:59.648000+00:00",
           "OperationArn": "arn:aws:kafka:us-east-1:012345678012:cluster-operation/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2/0123abcd-abcd-4f7f-1234-9876543210ef",
           "OperationState": "UPDATE_IN_PROGRESS",
           "OperationSteps": [
               {
                   "StepInfo": {
                       "StepStatus": "IN_PROGRESS"
                   },
                   "StepName": "INITIALIZE_UPDATE"
               },
               {
                   "StepInfo": {
                       "StepStatus": "PENDING"
                   },
                   "StepName": "UPDATE_APACHE_KAFKA_BINARIES"
               },
               {
                   "StepInfo": {
                       "StepStatus": "PENDING"
                   },
                   "StepName": "FINALIZE_UPDATE"
               }
           ],
           "OperationType": "UPDATE_CLUSTER_KAFKA_VERSION",
           "SourceClusterInfo": {
               "KafkaVersion": "2.4.1"
           },
           "TargetClusterInfo": {
               "KafkaVersion": "2.6.1"
           }
       }
   }
   ```

   If `OperationState` has the value `UPDATE_IN_PROGRESS`, wait a while, then run the `describe-cluster-operation` command again\. When the operation is complete, the value of `OperationState` becomes `UPDATE_COMPLETE`\. Because the time required for Amazon MSK to complete the operation varies, you might need to check repeatedly until the operation is complete\. 

**Updating the Apache Kafka version using the API**

1. Invoke the [GetCompatibleKafkaVersions](https://docs.aws.amazon.com//msk/1.0/apireference/compatible-kafka-versions.html#GetCompatibleKafkaVersions) operation to get a list of the Apache Kafka versions to which you can update the cluster\.

1. Invoke the [UpdateClusterKafkaVersion](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn-version.html#UpdateClusterKafkaVersion) operation to update the cluster to one of the compatible Apache Kafka versions\.