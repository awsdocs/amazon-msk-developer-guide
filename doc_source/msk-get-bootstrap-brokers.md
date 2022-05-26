# Getting the bootstrap brokers for an Amazon MSK Cluster<a name="msk-get-bootstrap-brokers"></a>

## Getting the bootstrap brokers using the AWS Management Console<a name="get-bootstrap-console"></a>

The term* bootstrap brokers* refers to a list of brokers that an Apache Kafka client can use as a starting point to connect to the cluster\. This list doesn't necessarily include all of the brokers in a cluster\.

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. The table shows all the clusters for the current region under this account\. Choose the name of a cluster to view its description\.

1. On the **Cluster summary** page, choose **View client information**\. This shows you the bootstrap brokers, as well as the Apache ZooKeeper connection string\.

## Getting the bootstrap brokers using the AWS CLI<a name="get-bootstrap-cli"></a>

Run the following command, replacing *ClusterArn* with the Amazon Resource Name \(ARN\) that you obtained when you created your cluster\. If you don't have the ARN for your cluster, you can find it by listing all clusters\. For more information, see [Listing Amazon MSK clusters](msk-list-clusters.md)\.

```
aws kafka get-bootstrap-brokers --cluster-arn ClusterArn
```

For an MSK cluster that uses [IAM access control](iam-access-control.md), the output of this command looks like the following JSON example\.

```
{
    "BootstrapBrokerStringSaslIam": "b-1.myTestCluster.123z8u.c2.kafka.us-west-1.amazonaws.com:9098,b-2.myTestCluster.123z8u.c2.kafka.us-west-1.amazonaws.com:9098"
}
```

The following example shows the bootstrap brokers for a cluster that has public access turned on\. Use the `BootstrapBrokerStringPublicSaslIam` for public access, and the `BootstrapBrokerStringSaslIam` string for access from within AWS\.

```
{
    "BootstrapBrokerStringPublicSaslIam": "b-2-public.myTestCluster.v4ni96.c2.kafka-beta.us-east-1.amazonaws.com:9198,b-1-public.myTestCluster.v4ni96.c2.kafka-beta.us-east-1.amazonaws.com:9198,b-3-public.myTestCluster.v4ni96.c2.kafka-beta.us-east-1.amazonaws.com:9198",
    "BootstrapBrokerStringSaslIam": "b-2.myTestCluster.v4ni96.c2.kafka-beta.us-east-1.amazonaws.com:9098,b-1.myTestCluster.v4ni96.c2.kafka-beta.us-east-1.amazonaws.com:9098,b-3.myTestCluster.v4ni96.c2.kafka-beta.us-east-1.amazonaws.com:9098"
}
```

The bootstrap brokers string should contain three brokers from across the Availability Zones in which your MSK cluster is deployed \(unless only two brokers are available\)\. 

## Getting the bootstrap brokers using the API<a name="get-bootstrap-api"></a>

To get the bootstrap brokers using the API, see [GetBootstrapBrokers](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn-bootstrap-brokers.html#GetBootstrapBrokers)\.