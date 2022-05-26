# Best Practices<a name="bestpractices"></a>

This topic outlines some best practices to follow when using Amazon MSK\.

## Right\-size your cluster: Number of partitions per broker<a name="partitions-per-broker"></a>

The following table shows the maximum number of partitions \(including leader and follower replicas\) that you can have per broker\. 


****  

| Broker type | Maximum number of partitions \(including leader and follower replicas\) per broker | 
| --- | --- | 
| kafka\.t3\.small | 300 | 
| kafka\.m5\.large or kafka\.m5\.xlarge | 1000 | 
| kafka\.m5\.2xlarge | 2000 | 
| kafka\.m5\.4xlarge, kafka\.m5\.8xlarge, kafka\.m5\.12xlarge, kafka\.m5\.16xlarge, or kafka\.m5\.24xlarge | 4000 | 

If the number of partitions per broker exceeds the maximum value specified in the previous table, you cannot perform any of the following operations on the cluster:
+ Update the cluster configuration
+ Update the Apache Kafka version for the cluster
+ Update the cluster to a smaller broker type
+ Associate an AWS Secrets Manager secret with a cluster that has SASL/SCRAM authentication

For guidance on choosing the number of partitions, see [Apache Kafka Supports 200K Partitions Per Cluster](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)\. We also recommend that you perform your own testing to determine the right type for your brokers\. For more information about the different broker types, see [Broker types](msk-create-cluster.md#broker-instance-types)\. 

## Right\-size your cluster: Number of brokers per cluster<a name="brokers-per-cluster"></a>

To determine the right number of brokers for your MSK cluster and understand costs, see the [MSK Sizing and Pricing](https://dy7oqpxkwhskb.cloudfront.net/MSK_Sizing_Pricing.xlsx) spreadsheet\. This spreadsheet provides an estimate for sizing an MSK cluster and the associated costs of Amazon MSK compared to a similar, self\-managed, EC2\-based Apache Kafka cluster\. For more information about the input parameters in the spreadsheet, hover over the parameter descriptions\. Estimates provided by this sheet are conservative and provide a starting point for a new cluster\. Cluster performance, size, and costs are dependent on your use case and we recommend that you verify them with actual testing\.

To understand how the underlying infrastructure affects Apache Kafka performance, see [Best practices for right\-sizing your Apache Kafka clusters to optimize performance and cost](http://aws.amazon.com/blogs/big-data/best-practices-for-right-sizing-your-apache-kafka-clusters-to-optimize-performance-and-cost/) in the AWS Big Data Blog\. The blog post provides information about how to size your clusters to meet your throughput, availability, and latency requirements\. It also provides answers to questions such as when you should scale *up* versus scale *out*, and guidance on how to continuously verify the size of your production clusters\.

## Build highly available clusters<a name="ensure-high-availability"></a>

Use the following recommendations so that your MSK cluster can be highly available during an update \(such as when you're updating the broker type or Apache Kafka version, for example\) or when Amazon MSK is replacing a broker\.
+ Ensure that the replication factor \(RF\) is at least 2 for two\-AZ clusters and at least 3 for three\-AZ clusters\. An RF of 1 can lead to offline partitions during a rolling update\.
+ Set minimum in\-sync replicas \(minISR\) to at most RF \- 1\. A minISR that is equal to the RF can prevent producing to the cluster during a rolling update\. A minISR of 2 allows three\-way replicated topics to be available when one replica is offline\.
+ Ensure client connection strings include multiple brokers\. Having multiple brokers in a client’s connection string allows for failover when a specific broker is offline for an update\. For information about how to get a connection string with multiple brokers, see [Getting the bootstrap brokers for an Amazon MSK Cluster](msk-get-bootstrap-brokers.md)\.

## Monitor CPU usage<a name="bestpractices-monitor-cpu"></a>

Amazon MSK strongly recommends that you maintain the total CPU utilization for your brokers \(defined as `CPU User + CPU System`\) under 60%\. When you have at least 40% of your cluster’s total CPU available, Apache Kafka can redistribute CPU load across brokers in the cluster when necessary\. One example of when this is necessary is when Amazon MSK detects and recovers from a broker fault; in this case, Amazon MSK performs automatic maintenance, like patching\. Another example is when a user requests a broker\-type change or version upgrade; in these two cases, Amazon MSK deploys rolling workflows that take one broker offline at a time\. When brokers with lead partitions go offline, Apache Kafka reassigns partition leadership to redistribute work to other brokers in the cluster\. By following this best practice you can ensure you have enough CPU headroom in your cluster to tolerate operational events like these\.

You can use [Amazon CloudWatch metric math](https://docs.aws.amazon.com//AmazonCloudWatch/latest/monitoring/using-metric-math.html) to create a composite metric that is `CPU User + CPU System`\. Set an alarm that gets triggered when the composite metric reaches an average CPU utilization of 60%\. When this alarm is triggered, scale the cluster using one of the following options:
+ Option 1 \(recommended\): [Update your broker type](https://docs.aws.amazon.com//msk/latest/developerguide/msk-update-broker-type.html) to the next larger type\. For example, if the current type is `kafka.m5.large`, update the cluster to use `kafka.m5.xlarge`\. Keep in mind that when you update the broker type in the cluster, Amazon MSK takes brokers offline in a rolling fashion and temporarily reassigns partition leadership to other brokers\. A size update typically takes 10\-15 minutes per broker\.
+ Option 2: If there are topics with all messages ingested from producers that use round\-robin writes \(in other words, messages aren't keyed and ordering isn’t important to consumers\), [expand your cluster](https://docs.aws.amazon.com//msk/latest/developerguide/msk-update-broker-count.html) by adding brokers\. Also add partitions to existing topics with the highest throughput\. Next, use `kafka-topics.sh --describe` to ensure that newly added partitions are assigned to the new brokers\. The main benefit of this option compared to the previous one is that you can manage resources and costs more granularly\. Additionally, you can use this option if CPU load significantly exceeds 60% because this form of scaling doesn't typically result in increased load on existing brokers\.
+ Option 3: Expand your cluster by adding brokers, then reassign existing partitions by using the partition reassignment tool named `kafka-reassign-partitions.sh`\. However, if you use this option, the cluster will need to spend resources to replicate data from broker to broker after partitions are reassigned\. Compared to the two previous options, this can significantly increase the load on the cluster at first\. As a result, Amazon MSK doesn't recommend using this option when CPU utilization is above 70% because replication causes additional CPU load and network traffic\. Amazon MSK only recommends using this option if the two previous options aren't feasible\.

Other recommendations: 
+ Monitor total CPU utilization per broker as a proxy for load distribution\. If brokers have consistently uneven CPU utilization it might be a sign that load isn't evenly distributed within the cluster\. Amazon MSK recommends using [Cruise Control](https://docs.aws.amazon.com//msk/latest/developerguide/cruise-control.html) to continuously manage load distribution via partition assignment\.
+ Monitor produce and consume latency\. Produce and consume latency can increase linearly with CPU utilization\.

## Monitor disk space<a name="bestpractices-monitor-disk-space"></a>

To avoid running out of disk space for messages, create a CloudWatch alarm that watches the `KafkaDataLogsDiskUsed` metric\. When the value of this metric reaches or exceeds 85%, perform one or more of the following actions:
+ Use [Automatic scaling](msk-autoexpand.md)\. You can also manually increase broker storage as described in [Manual scaling](manually-expand-storage.md)\.
+ Reduce the message retention period or log size\. For information on how to do that, see [Adjust data retention parameters](#bestpractices-retention-period)\.
+ Delete unused topics\.

For information on how to set up and use alarms, see [Using Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\. For a full list of Amazon MSK metrics, see [Monitoring an Amazon MSK Cluster](monitoring.md)\.

## Adjust data retention parameters<a name="bestpractices-retention-period"></a>

Consuming messages doesn't remove them from the log\. To free up disk space regularly, you can explicitly specify a retention time period, which is how long messages stay in the log\. You can also specify a retention log size\. When either the retention time period or the retention log size are reached, Apache Kafka starts removing inactive segments from the log\.

To specify a retention policy at the cluster level, set one or more of the following parameters: `log.retention.hours`, `log.retention.minutes`, `log.retention.ms`, or `log.retention.bytes`\. For more information, see [Custom MSK Configurations](msk-configuration-properties.md)\.

You can also specify retention parameters at the topic level:
+ To specify a retention time period per topic, use the following command\.

  ```
  kafka-configs.sh --zookeeper ZooKeeperConnectionString --alter --entity-type topics --entity-name TopicName --add-config retention.ms=DesiredRetentionTimePeriod
  ```
+ To specify a retention log size per topic, use the following command\.

  ```
  kafka-configs.sh --zookeeper ZooKeeperConnectionString --alter --entity-type topics --entity-name TopicName --add-config retention.bytes=DesiredRetentionLogSize
  ```

The retention parameters that you specify at the topic level take precedence over cluster\-level parameters\.

## Don't add non\-MSK brokers<a name="bestpractices-non-msk-brokders"></a>

If you use Apache ZooKeeper commands to add brokers, these brokers don't get added to your MSK cluster, and your Apache ZooKeeper will contain incorrect information about the cluster\. This might result in data loss\. For supported cluster operations, see [Amazon MSK: How It Works](operations.md)\.

## Enable in\-transit encryption<a name="bestpractices-enable-in-transit-encryption"></a>

For information about encryption in transit and how to enable it, see [Encryption in Transit](msk-encryption.md#msk-encryption-in-transit)\.

## Reassign partitions<a name="bestpractices-balance-cluster"></a>

To move partitions to different brokers on the same cluster, you can use the partition reassignment tool named `kafka-reassign-partitions.sh`\. For example, after you add new brokers to expand a cluster, you can rebalance that cluster by reassigning partitions to the new brokers\. For information about how to add brokers to a cluster, see [Expanding an Amazon MSK Cluster](msk-update-broker-count.md)\. For information about the partition reassignment tool, see [Expanding your cluster](https://kafka.apache.org/documentation/#basic_ops_cluster_expansion) in the Apache Kafka documentation\.