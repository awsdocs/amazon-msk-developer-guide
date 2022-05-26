# Supported Apache Kafka versions<a name="supported-kafka-versions"></a>

Amazon Managed Streaming for Apache Kafka \(Amazon MSK\) supports the following Apache Kafka and Amazon MSK versions\.

**Topics**
+ [Apache Kafka version 2\.8\.1](#2.8.1)
+ [Apache Kafka version 2\.8\.0](#2.8.0)
+ [Apache Kafka version 2\.7\.2](#2.7.2)
+ [Apache Kafka version 2\.7\.1](#2.7.1)
+ [Apache Kafka version 2\.6\.3](#2.6.3)
+ [Apache Kafka version 2\.6\.2 \[Recommended\]](#2.6.2)
+ [Apache Kafka version 2\.7\.0](#2.7.0)
+ [Apache Kafka version 2\.6\.1](#2.6.1)
+ [Apache Kafka version 2\.6\.0](#2.6.0)
+ [Apache Kafka version 2\.5\.1](#2.5.1)
+ [Amazon MSK bug\-fix version 2\.4\.1\.1](#2.4.1.1)
+ [Apache Kafka version 2\.4\.1 \(use 2\.4\.1\.1 instead\)](#2.4.1)
+ [Apache Kafka version 2\.3\.1](#2.3.1)
+ [Apache Kafka version 2\.2\.1](#2.2.1)
+ [Apache Kafka version 1\.1\.1 \(for existing clusters only\)](#1.1.1)

## Apache Kafka version 2\.8\.1<a name="2.8.1"></a>

For information about Apache Kafka version 2\.8\.1, see its [release notes](https://downloads.apache.org/kafka/2.8.1/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.8\.0<a name="2.8.0"></a>

For information about Apache Kafka version 2\.8\.0, see its [release notes](https://archive.apache.org/dist/kafka/2.8.0/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.7\.2<a name="2.7.2"></a>

For information about Apache Kafka version 2\.7\.2, see its [release notes](https://archive.apache.org/dist/kafka/2.7.2/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.7\.1<a name="2.7.1"></a>

For information about Apache Kafka version 2\.7\.1, see its [release notes](https://archive.apache.org/dist/kafka/2.7.1/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.6\.3<a name="2.6.3"></a>

For information about Apache Kafka version 2\.6\.3, see its [release notes](https://archive.apache.org/dist/kafka/2.6.3/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.6\.2 \[Recommended\]<a name="2.6.2"></a>

For information about Apache Kafka version 2\.6\.2, see its [release notes](https://archive.apache.org/dist/kafka/2.6.2/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.7\.0<a name="2.7.0"></a>

For information about Apache Kafka version 2\.7\.0, see its [release notes](https://archive.apache.org/dist/kafka/2.7.0/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.6\.1<a name="2.6.1"></a>

For information about Apache Kafka version 2\.6\.1, see its [release notes](https://archive.apache.org/dist/kafka/2.6.1/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.6\.0<a name="2.6.0"></a>

For information about Apache Kafka version 2\.6\.0, see its [release notes](https://archive.apache.org/dist/kafka/2.6.0/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.5\.1<a name="2.5.1"></a>

Apache Kafka version 2\.5\.1 includes several bug fixes and new features, including encryption in\-transit for Apache ZooKeeper and administration clients\. Amazon MSK provides TLS ZooKeeper endpoints, which you can query with the [DescribeCluster ](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster) operation\. 

The output of the [ DescribeCluster](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster) operation includes the `ZookeeperConnectStringTls` node, which lists the TLS zookeeper endpoints\.

The following example shows the `ZookeeperConnectStringTls` node of the response for the `DescribeCluster` operation:

```
"ZookeeperConnectStringTls": "z-3.awskafkatutorialc.abcd123.c3.kafka.us-east-1.amazonaws.com:2182,z-2.awskafkatutorialc.abcd123.c3.kafka.us-east-1.amazonaws.com:2182,z-1.awskafkatutorialc.abcd123.c3.kafka.us-east-1.amazonaws.com:2182"
```

For information about using TLS encryption with zookeeper, see [Using TLS security with Apache ZooKeeper](zookeeper-security.md#zookeeper-security-tls)\.

For more information about Apache Kafka version 2\.5\.1, see its [release notes](https://archive.apache.org/dist/kafka/2.5.1/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Amazon MSK bug\-fix version 2\.4\.1\.1<a name="2.4.1.1"></a>

This release is an Amazon MSK\-only bug\-fix version of Apache Kafka version 2\.4\.1\. This bug\-fix release contains a fix for [KAFKA\-9752](https://issues.apache.org/jira/browse/KAFKA-9752), a rare issue that causes consumer groups to continuously rebalance and remain in the `PreparingRebalance` state\. This issue affects clusters running Apache Kafka versions 2\.3\.1 and 2\.4\.1\. This release contains a community\-produced fix that is available in Apache Kafka version 2\.5\.0\. 

**Note**  
Amazon MSK clusters running version 2\.4\.1\.1 are compatible with any Apache Kafka client that is compatible with Apache Kafka version 2\.4\.1\.

We recommend that you use MSK bug\-fix version 2\.4\.1\.1 for new Amazon MSK clusters if you prefer to use Apache Kafka 2\.4\.1\. You can update existing clusters running Apache Kafka version 2\.4\.1 to this version to incorporate this fix\. For information about upgrading an existing cluster, see [Updating the Apache Kafka version](version-upgrades.md)\.

To work around this issue without upgrading the cluster to version 2\.4\.1\.1, see the [Consumer group stuck in `PreparingRebalance` state](troubleshooting.md#consumer-group-rebalance) section of the [](troubleshooting.md) guide\. 

## Apache Kafka version 2\.4\.1 \(use 2\.4\.1\.1 instead\)<a name="2.4.1"></a>

**Note**  
You can no longer create an MSK cluster with Apache Kafka version 2\.4\.1\. Instead, you can use [Amazon MSK bug\-fix version 2\.4\.1\.1](#2.4.1.1) with clients compatible with Apache Kafka version 2\.4\.1\. And if you already have an MSK cluster with Apache Kafka version 2\.4\.1, we recommend you update it to use Apache Kafka version 2\.4\.1\.1 instead\.

KIP\-392 is one of the key Kafka Improvement Proposals that are included in the 2\.4\.1 release of Apache Kafka\. This improvement allows consumers to fetch from the closest replica\. To use this feature, set `client.rack` in the consumer properties to the ID of the consumer's Availability Zone\. An example AZ ID is `use1-az1`\. Amazon MSK sets `broker.rack` to the IDs of the Availability Zones of the brokers\. You must also set the `replica.selector.class` configuration property to `org.apache.kafka.common.replica.RackAwareReplicaSelector`, which is an implementation of rack awareness provided by Apache Kafka\. 

When you use this version of Apache Kafka, the metrics in the `PER_TOPIC_PER_BROKER` monitoring level appear only after their values become nonzero for the first time\. For more information about this, see [`PER_TOPIC_PER_BROKER` Level Monitoring](metrics-details.md#broker-topic-metrics)\. 

For information about how to find Availability Zone IDs, see [AZ IDs for Your Resource](https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids.html) in the AWS Resource Access Manager user guide\. 

For information about setting configuration properties, see [Amazon MSK Configuration](msk-configuration.md)\. 

For more information about KIP\-392, see [Allow Consumers to Fetch from Closest Replica](https://cwiki.apache.org/confluence/display/KAFKA/KIP-392:+Allow+consumers+to+fetch+from+closest+replica) in the Confluence pages\.

For more information about Apache Kafka version 2\.4\.1, see its [release notes](https://archive.apache.org/dist/kafka/2.4.1/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.3\.1<a name="2.3.1"></a>

For information about Apache Kafka version 2\.3\.1, see its [release notes](https://archive.apache.org/dist/kafka/2.3.1/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 2\.2\.1<a name="2.2.1"></a>

For information about Apache Kafka version 2\.2\.1, see its [release notes](https://archive.apache.org/dist/kafka/2.2.1/RELEASE_NOTES.html) on the Apache Kafka downloads site\.

## Apache Kafka version 1\.1\.1 \(for existing clusters only\)<a name="1.1.1"></a>

You can no longer create a new MSK cluster with Apache Kafka version 1\.1\.1\. You can continue to use existing clusters that are configured with Apache Kafka version 1\.1\.1\. For information about Apache Kafka version 1\.1\.1, see its [release notes](https://archive.apache.org/dist/kafka/1.1.1/RELEASE_NOTES.html) on the Apache Kafka downloads site\.