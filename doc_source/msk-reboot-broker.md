# Rebooting a broker for an Amazon MSK cluster<a name="msk-reboot-broker"></a>

Use this Amazon MSK operation when you want to reboot a broker for your MSK cluster\. To reboot a broker for a cluster, make sure that the cluster in the `ACTIVE` state\.

The Amazon MSK service may reboot the brokers for your MSK cluster during system maintenance, such as patching or version upgrades\. Rebooting a broker manually lets you test resilience of your Kafka clients to determine how they respond to system maintenance\. 

## Rebooting a broker using the AWS Management Console<a name="msk-reboot-broker-console"></a>

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose the MSK cluster whose broker you want to reboot\.

1. Scroll down to the **Broker details** section, and choose the broker you want to reboot\.

1. Choose the **Reboot broker** button\.

## Rebooting a broker using the AWS CLI<a name="msk-reboot-broker-cli"></a>

1. Run the following command, replacing *ClusterArn* with the Amazon Resource Name \(ARN\) that you obtained when you created your cluster, and the *BrokerId* with the ID of the broker that you want to reboot\.
**Note**  
The `reboot-broker` operation only supports rebooting one broker at a time\.

   If you don't have the ARN for your cluster, you can find it by listing all clusters\. For more information, see [Listing Amazon MSK clusters](msk-list-clusters.md)\.

   If you don't have the broker IDs for your cluster, you can find them by listing the broker nodes\. For more information, see [list\-nodes](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kafka/list-nodes.html)\.

   ```
   aws kafka reboot-broker --cluster-arn ClusterArn --broker-ids BrokerId
   ```

   The output of this `reboot-broker` operation looks like the following JSON\.

   ```
   {
       
       "ClusterArn": "arn:aws:kafka:us-east-1:012345678012:cluster/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2",
       "ClusterOperationArn": "arn:aws:kafka:us-east-1:012345678012:cluster-operation/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2/0123abcd-abcd-4f7f-1234-9876543210ef"
   }
   ```

1. To get the result of the `reboot-broker` operation, run the following command, replacing *ClusterOperationArn* with the ARN that you obtained in the output of the `reboot-broker` command\.

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
           "OperationState": "REBOOT_IN_PROGRESS",
           "OperationType": "REBOOT_NODE",
           "SourceClusterInfo": {},
           "TargetClusterInfo": {}
       }
   }
   ```

When the reboot operation is complete, the `OperationState` is `REBOOT_COMPLETE`\.

## Rebooting a broker using the API<a name="msk-reboot-broker"></a>

To reboot a broker in a cluster using the API, see [RebootBroker](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn-reboot-broker.html#RebootBroker)\.