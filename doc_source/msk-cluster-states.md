# Cluster states<a name="msk-cluster-states"></a>

The following table shows the possible states of a cluster and describes what they mean\. It also describes what actions you can and cannot perform when a cluster is in one of these states\. To find out the state of a cluster, you can visit the AWS Management Console\. You can also use the [describe\-cluster\-v2](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kafka/describe-cluster-v2.html) command or the [DescribeClusterV2](https://docs.aws.amazon.com/MSK/2.0/APIReference/v2-clusters-clusterarn.html#DescribeClusterV2) operation to describe the cluster\. The description of a cluster includes its state\.


****  

| Cluster state | Meaning and possible actions | 
| --- | --- | 
| ACTIVE |  You can produce and consume data\. You can also perform Amazon MSK API and AWS CLI operations on the cluster\.  | 
| CREATING |  Amazon MSK is setting up the cluster\. You must wait for the cluster to reach the ACTIVE state before you can use it to produce or consume data or to perform Amazon MSK API or AWS CLI operations on it\.  | 
| DELETING | The cluster is being deleted\. You cannot use it to produce or consume data\. You also cannot perform Amazon MSK API or AWS CLI operations on it\. | 
| FAILED | The cluster creation or deletion process failed\. You cannot use the cluster to produce or consume data\. You can delete the cluster but cannot perform Amazon MSK API or AWS CLI update operations on it\. | 
| HEALING |  Amazon MSK is running an internal operation, like replacing an unhealthy broker\. For example, the broker might be unresponsive\. You can still use the cluster to produce and consume data\. However, you cannot perform Amazon MSK API or AWS CLI update operations on the cluster until it returns to the ACTIVE state\.  | 
| MAINTENANCE |  Amazon MSK is performing routine maintenance operations on the cluster\. Such maintenance operations include security patching\. You can still use the cluster to produce and consume data\. However, you cannot perform Amazon MSK API or AWS CLI update operations on the cluster until it returns to the ACTIVE state\.  | 
| REBOOTING\_BROKER | Amazon MSK is rebooting a broker\. You can still use the cluster to produce and consume data\. However, you cannot perform Amazon MSK API or AWS CLI update operations on the cluster until it returns to the ACTIVE state\. | 
| UPDATING | A user\-initiated Amazon MSK API or AWS CLI operation is updating the cluster\. You can still use the cluster to produce and consume data\. However, you cannot perform any additional Amazon MSK API or AWS CLI update operations on the cluster until it returns to the ACTIVE state\. | 