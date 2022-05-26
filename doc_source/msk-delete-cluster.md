# Deleting an Amazon MSK Cluster<a name="msk-delete-cluster"></a>

## Deleting a cluster using the AWS Management Console<a name="delete-cluster-console"></a>

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose the MSK cluster that you want to delete by selecting the check box next to it\.

1. Choose **Delete**, and then confirm deletion\.

## Deleting a cluster using the AWS CLI<a name="delete-cluster-cli"></a>

Run the following command, replacing *ClusterArn* with the Amazon Resource Name \(ARN\) that you obtained when you created your cluster\. If you don't have the ARN for your cluster, you can find it by listing all clusters\. For more information, see [Listing Amazon MSK clusters](msk-list-clusters.md)\.

```
aws kafka delete-cluster --cluster-arn ClusterArn
```

## Deleting a cluster using the API<a name="delete-cluster-api"></a>

To delete a cluster using the API, see [DeleteCluster](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn.html#DeleteCluster)\.