# Manual scaling<a name="manually-expand-storage"></a>

To increase storage, wait for the cluster to be in the `ACTIVE` state\. Storage scaling has a cool\-down period of at least six hours between events\. Even though the operation makes additional storage available right away, the service performs optimizations on your cluster that can take up to 24 hours or more\. The duration of these optimizations is proportional to your storage size\.

## Scaling up broker storage using the AWS Management Console<a name="update-storage-console"></a>

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose the MSK cluster for which you want to update broker storage\.

1. In the **Storage** section, choose **Edit**\.

1. Specify the storage volume you want\. You can only increase the amount of storage, you can't decrease it\.

1. Choose **Save changes**\.

## Scaling up broker storage using the AWS CLI<a name="update-storage-cli"></a>

Run the following command, replacing *ClusterArn* with the Amazon Resource Name \(ARN\) that you obtained when you created your cluster\. If you don't have the ARN for your cluster, you can find it by listing all clusters\. For more information, see [Listing Amazon MSK clusters](msk-list-clusters.md)\. 

Replace *Current\-Cluster\-Version* with the current version of the cluster\. 

**Important**  
Cluster versions aren't simple integers\. To find the current version of the cluster, use the [DescribeCluster](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster) operation or the [describe\-cluster](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kafka/describe-cluster.html) AWS CLI command\. An example version is `KTVPDKIKX0DER`\.

The *Target\-Volume\-in\-GiB* parameter represents the amount of storage that you want each broker to have\. It is only possible to update the storage for all the brokers\. You can't specify individual brokers for which to update storage\. The value you specify for *Target\-Volume\-in\-GiB* must be a whole number that is greater than 100 GiB\. The storage per broker after the update operation can't exceed 16384 GiB\.

```
aws kafka update-broker-storage --cluster-arn ClusterArn --current-version Current-Cluster-Version --target-broker-ebs-volume-info '{"KafkaBrokerNodeId": "All", "VolumeSizeGB": Target-Volume-in-GiB}' 
```

## Scaling up broker storage using the API<a name="update-storage-api"></a>

To update a broker storage using the API, see [UpdateBrokerStorage](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn-nodes-storage.html#UpdateBrokerStorage)\.