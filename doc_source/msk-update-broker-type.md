# Updating the broker type<a name="msk-update-broker-type"></a>

You can scale your MSK cluster on demand by changing the type \(the size or family\) of your brokers without reassigning Apache Kafka partitions\. Changing the type of your brokers gives you the flexibility to adjust your MSK cluster’s compute capacity based on changes in your workloads, without interrupting your cluster I/O\. Amazon MSK uses the same broker type for all the brokers in a given cluster\. This section describes how to update the broker type for your MSK cluster\. The broker\-type update happens in a rolling fashion while the cluster is up and running\. This means that Amazon MSK takes down one broker at a time to perform the broker\-type update\. For information about how to make a cluster highly available during a broker\-type update, see [Build highly available clusters](bestpractices.md#ensure-high-availability)\. To further reduce any potential impact on productivity, you can perform the broker\-type update during a period of low traffic\.

During a broker\-type update, you can continue to produce and consume data\. However, you must wait until the update is done before you can reboot brokers or invoke any of the update operations listed under [Amazon MSK operations](https://docs.aws.amazon.com/msk/1.0/apireference/operations.html)\.

If you want to update your cluster to a smaller broker type, we recommend that you try the update on a test cluster first to see how it affects your scenario\. 

**Important**  
You can't update a cluster to a smaller broker type if the number of partitions per broker exceeds the maximum number specified in [ Right\-size your cluster: Number of partitions per broker](bestpractices.md#partitions-per-broker)\.

## Updating the broker type using the AWS Management Console<a name="update-broker-type-console"></a>

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose the MSK cluster for which you want to update the broker type\.

1. On the details page for the cluster, find the **Brokers summary** section, and choose **Edit broker type**\.

1. Choose the broker type you want from the list\.

1. Save changes\.

## Updating the broker type using the AWS CLI<a name="update-broker-type-cli"></a>

1. Run the following command, replacing *ClusterArn* with the Amazon Resource Name \(ARN\) that you obtained when you created your cluster\. If you don't have the ARN for your cluster, you can find it by listing all clusters\. For more information, see [Listing Amazon MSK clusters](msk-list-clusters.md)\. 

   Replace *Current\-Cluster\-Version* with the current version of the cluster and *TargetType* with the new type that you want the brokers to be\. To learn more about broker types, see [Broker types](msk-create-cluster.md#broker-instance-types)\.

   ```
   aws kafka update-broker-type --cluster-arn ClusterArn --current-version Current-Cluster-Version --target-instance-type TargetType
   ```

   The following is an example of how to use this command:

   ```
   aws kafka update-broker-type --cluster-arn "arn:aws:kafka:us-east-1:0123456789012:cluster/exampleName/abcd1234-0123-abcd-5678-1234abcd-1" --current-version "K1X5R6FKA87" --target-instance-type kafka.m5.large 
   ```

   The output of this command looks like the following JSON example\.

   ```
   {
       "ClusterArn": "arn:aws:kafka:us-east-1:0123456789012:cluster/exampleName/abcd1234-0123-abcd-5678-1234abcd-1",
       "ClusterOperationArn": "arn:aws:kafka:us-east-1:012345678012:cluster-operation/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2/0123abcd-abcd-4f7f-1234-9876543210ef"
   }
   ```

1. To get the result of the `update-broker-type` operation, run the following command, replacing *ClusterOperationArn* with the ARN that you obtained in the output of the `update-broker-type` command\.

   ```
   aws kafka describe-cluster-operation --cluster-operation-arn ClusterOperationArn
   ```

   The output of this `describe-cluster-operation` command looks like the following JSON example\.

   ```
   {
     "ClusterOperationInfo": {
       "ClientRequestId": "982168a3-939f-11e9-8a62-538df00285db",
       "ClusterArn": "arn:aws:kafka:us-east-1:0123456789012:cluster/exampleName/abcd1234-0123-abcd-5678-1234abcd-1",
       "CreationTime": "2021-01-09T02:24:22.198000+00:00",
       "OperationArn": "arn:aws:kafka:us-east-1:012345678012:cluster-operation/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2/0123abcd-abcd-4f7f-1234-9876543210ef",
       "OperationState": "UPDATE_COMPLETE",
       "OperationType": "UPDATE_BROKER_TYPE",
       "SourceClusterInfo": {
         "InstanceType": "t3.small"
       },
       "TargetClusterInfo": {
         "InstanceType": "m5.large"
       }
     }
   }
   ```

   If `OperationState` has the value `UPDATE_IN_PROGRESS`, wait a while, then run the `describe-cluster-operation` command again\. 

## Updating the broker type using the API<a name="update-broker-type-api"></a>

To update the broker type using the API, see [UpdateBrokerType](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn-nodes-type.html#UpdateBrokerType)\.