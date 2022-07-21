# Migrating clusters using Apache Kafka's MirrorMaker<a name="migration"></a>

You can mirror or migrate your cluster using MirrorMaker, which is part of Apache Kafka\. For example, you can use it to migrate your Apache Kafka cluster to Amazon MSK or to migrate from one MSK cluster to another\. For information about how to use MirrorMaker, see [Mirroring data between clusters](https://kafka.apache.org/documentation/#basic_ops_mirror_maker) in the Apache Kafka documentation\. We recommend setting up MirrorMaker in a highly available configuration\.

**An outline of the steps to follow when using MirrorMaker to migrate to an MSK cluster**

1. Create the destination MSK cluster

1. Start MirrorMaker from an Amazon EC2 instance within the same Amazon VPC as the destination cluster\.

1. Inspect the MirrorMaker lag\.

1. After MirrorMaker catches up, redirect producers and consumers to the new cluster using the MSK cluster bootstrap brokers\.

1. Shut down MirrorMaker\.

## Migrating your Apache Kafka cluster to Amazon MSK<a name="migrate-on-prem-to-msk"></a>

Suppose that you have an Apache Kafka cluster named `CLUSTER_ONPREM`\. That cluster is populated with topics and data\. If you want to migrate that cluster to a newly created Amazon MSK cluster named `CLUSTER_AWSMSK`, this procedure provides a high\-level view of the steps that you need to follow\. 

**To migrate your existing Apache Kafka cluster to Amazon MSK**

1. In `CLUSTER_AWSMSK`, create all the topics that you want to migrate\. 

   You can't use MirrorMaker for this step because it doesn't automatically re\-create the topics that you want to migrate with the right replication level\. You can create the topics in Amazon MSK with the same replication factors and numbers of partitions that they had in `CLUSTER_ONPREM`\. You can also create the topics with different replication factors and numbers of partitions\.

1. Start MirrorMaker from an instance that has read access to `CLUSTER_ONPREM` and write access to `CLUSTER_AWSMSK`\.

1. Run the following command to mirror all topics:

   ```
   <path-to-your-kafka-installation>/bin/kafka-mirror-maker.sh --consumer.config config/mirrormaker-consumer.properties --producer.config config/mirrormaker-producer.properties --whitelist '.*'
   ```

   In this command, `config/mirrormaker-consumer.properties` points to a bootstrap broker in `CLUSTER_ONPREM`; for example, `bootstrap.servers=localhost:9092`\. And `config/mirrormaker-producer.properties` points to a bootstrap broker in CLUSTER\_AWSMSK; for example, `bootstrap.servers=10.0.0.237:9092,10.0.2.196:9092,10.0.1.233:9092`\.

1.  Keep MirrorMaker running in the background, and continue to use `CLUSTER_ONPREM`\. MirrorMaker mirrors all new data\. 

1. Check the progress of mirroring by inspecting the lag between the last offset for each topic and the current offset from which MirrorMaker is consuming\. 

   Remember that MirrorMaker is simply using a consumer and a producer\. So, you can check the lag using the `kafka-consumer-groups.sh` tool\. To find the consumer group name, look inside the `mirrormaker-consumer.properties` file for the `group.id`, and use its value\. If there is no such key in the file, you can create it\. For example, set `group.id=mirrormaker-consumer-group`\. 

1. After MirrorMaker finishes mirroring all topics, stop all producers and consumers, and then stop MirrorMaker\. Then redirect the producers and consumers to the `CLUSTER_AWSMSK` cluster by changing their producer and consumer bootstrap brokers values\. Restart all producers and consumers on `CLUSTER_AWSMSK`\. 

## Migrating from one Amazon MSK cluster to another<a name="msk-to-msk"></a>

You can use Apache MirrorMaker to migrate an MSK cluster to another cluster\. For example, you can migrate from one version of Apache Kafka to another\. For an example of how to use AWS CloudFormation to do this, see [AWS::MSK::Cluster Examples](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-msk-cluster.html#aws-resource-msk-cluster--examples) \(search for the example titled `Create Two MSK Clusters To Use With Apache MirrorMaker`\.

## MirrorMaker 1\.0 best practices<a name="mirrormaker-best-practices"></a>

This list of best practices applies to MirrorMaker 1\.0\.
+ Run MirrorMaker on the destination cluster\. This way, if a network problem happens, the messages are still available in the source cluster\. If you run MirrorMaker on the source cluster and events are buffered in the producer and there is a network issue, events might be lost\. 
+ If encryption is required in transit, run it in the source cluster\.
+ For consumers, set auto\.commit\.enabled=false
+ For producers, set 
  + max\.in\.flight\.requests\.per\.connection=1
  + retries=Int\.Max\_Value
  + acks=all
  + max\.block\.ms = Long\.Max\_Value
+ For a high producer throughput:
  + Buffer messages and fill message batches — tune buffer\.memory, batch\.size, linger\.ms
  + Tune socket buffers — receive\.buffer\.bytes, send\.buffer\.bytes
+ To avoid data loss, turn off auto commit at the source, so that MirrorMaker can control the commits, which it typically does after it receives the ack from the destination cluster\. If the producer has acks=all and the destination cluster has min\.insync\.replicas set to more than 1, the messages are persisted on more than one broker at the destination before the MirrorMaker consumer commits the offset at the source\.
+ If order is important, you can set retries to 0\. Alternatively, for a production environment, set max inflight connections to 1 to ensure that the batches sent out are not committed out of order if a batch fails in the middle\. This way, each batch sent is retried until the next batch is sent out\. If max\.block\.ms is not set to the maximum value, and if the producer buffer is full, there can be data loss \(depending on some of the other settings\)\. This can block and back\-pressure the consumer\.
+ For high throughput
  + Increase buffer\.memory\.
  + Increase batch size\.
  + Tune linger\.ms to allow the batches to fill\. This also allows for better compression, less network bandwidth usage, and less storage on the cluster\. This results in increased retention\. 
  + Monitor CPU and memory usage\.
+ For high consumer throughput
  + Increase the number of threads/consumers per MirrorMaker process — num\.streams\.
  + Increase the number of MirrorMaker processes across machines first before increasing threads to allow for high availability\.
  + Increase the number of MirrorMaker processes first on the same machine and then on different machines \(with the same group ID\)\.
  + Isolate topics that have very high throughput and use separate MirrorMaker instances\.
+ For management and configuration
  + Use AWS CloudFormation and configuration management tools like Chef and Ansible\.
  + Use Amazon EFS mounts to keep all configuration files accessible from all Amazon EC2 instances\.
  + Use containers for easy scaling and management of MirrorMaker instances\.
+ Typically, it takes more than one consumer to saturate a producer in MirrorMaker\. So, set up multiple consumers\. First, set them up on different machines to provide high availability\. Then, scale individual machines up to having a consumer for each partition, with consumers equally distributed across machines\.
+ For high throughput ingestion and delivery, tune the receive and send buffers because their defaults might be too low\. For maximum performance, ensure that the total number of streams \(num\.streams\) matches all of the topic partitions that MirrorMaker is trying to copy to the destination cluster\.

## MirrorMaker 2\.\* advantages<a name="mirrormaker-v2"></a>
+ Makes use of the Apache Kafka Connect framework and ecosystem\.
+ Detects new topics and partitions\.
+ Automatically syncs topic configuration between clusters\.
+ Supports "active/active" cluster pairs, as well as any number of active clusters\.
+ Provides new metrics including end\-to\-end replication latency across multiple data centers and clusters\.
+ Emits offsets required to migrate consumers between clusters and provides tooling for offset translation\.
+ Supports a high\-level configuration file for specifying multiple clusters and replication flows in one place, compared to low\-level producer/consumer properties for each MirrorMaker 1\.\* process\.