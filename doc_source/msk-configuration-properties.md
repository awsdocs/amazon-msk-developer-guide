# Custom MSK configurations<a name="msk-configuration-properties"></a>

Amazon MSK enables you to create a custom MSK configuration where you set the following properties\. Properties that you don't set explicitly get the values they have in [The default Amazon MSK configuration](msk-default-configuration.md)\. For more information about configuration properties, see [Apache Kafka Configuration](https://kafka.apache.org/documentation/#configuration)\.


**Apache Kafka configuration properties that you can set**  

| Name | Description | 
| --- | --- | 
| allow\.everyone\.if\.no\.acl\.found | If you want to set this property to false, first make sure you define Apache Kafka ACLs for your cluster\. If you set this property to false without first defining Apache Kafka ACLs, you will lose access to the cluster\. If that happens, you can update the configuration again and set this property to true in order to regain access to the cluster\. | 
| auto\.create\.topics\.enable | Enables topic autocreation on the server\. | 
| compression\.type | The final compression type for a given topic\. You can set this property to the standard compression codecs \(gzip, snappy, lz4, and zstd\)\. It additionally accepts uncompressed, which is equivalent to no compression; and producer, which means retain the original compression codec set by the producer\. | 
|  connections\.max\.idle\.ms  | Idle connections timeout in milliseconds\. The server socket processor threads close the connections that are idle for more than the value that you set for this property\. | 
| default\.replication\.factor | The default replication factor for automatically created topics\. | 
| delete\.topic\.enable | Enables the delete topic operation\. If this config is turned off, you can't delete a topic through the admin tool\. | 
| group\.initial\.rebalance\.delay\.ms | Amount of time the group coordinator waits for more consumers to join a new group before performing the first rebalance\. A longer delay means potentially fewer rebalances, but increases the time until processing begins\. | 
| group\.max\.session\.timeout\.ms | Maximum session timeout for registered consumers\. Longer timeouts give consumers more time to process messages in between heartbeats at the cost of a longer time to detect failures\. | 
| group\.min\.session\.timeout\.ms | Minimum session timeout for registered consumers\. Shorter timeouts result in quicker failure detection at the cost of more frequent consumer heartbeating, which can overwhelm broker resources\. | 
| leader\.imbalance\.per\.broker\.percentage | The ratio of leader imbalance allowed per broker\. The controller triggers a leader balance if it goes above this value per broker\. This value is specified in percentage\. | 
| log\.cleaner\.delete\.retention\.ms | Amount of time that you want Apache Kafka to retain deleted records\. The minimum value is 0\. | 
| log\.cleaner\.min\.cleanable\.ratio |  This configuration property can have values between 0 and 1\. It determines how frequently the log compactor attempts to clean the log \(assuming log compaction is enabled\)\. By default, Apache Kafka avoids cleaning a log where more than 50% of the log has been compacted\. This ratio bounds the maximum space wasted in the log by duplicates \(at 50%, which means at most 50% of the log could be duplicates\)\. A higher ratio means fewer, more efficient cleanings but more wasted space in the log\.  | 
| log\.cleanup\.policy | The default cleanup policy for segments beyond the retention window\. A comma\-separated list of valid policies\. Valid policies are delete and compact\. | 
| log\.flush\.interval\.messages | Number of messages accumulated on a log partition before messages are flushed to disk\. | 
| log\.flush\.interval\.ms | Maximum time in ms that a message in any topic is kept in memory before flushed to disk\. If not set, the value in log\.flush\.scheduler\.interval\.ms is used\. The minimum value is 0\. | 
| log\.message\.timestamp\.difference\.max\.ms | The maximum difference allowed between the timestamp when a broker receives a message and the timestamp specified in the message\. If log\.message\.timestamp\.type=CreateTime, a message is rejected if the difference in timestamp exceeds this threshold\. This configuration is ignored if log\.message\.timestamp\.type=LogAppendTime\. | 
| log\.message\.timestamp\.type | Specifies whether the timestamp in the message is the message creation time or the log append time\. The allowed values are CreateTime and LogAppendTime\. | 
| log\.retention\.bytes | Maximum size of the log before deleting it\. | 
| log\.retention\.hours | Number of hours to keep a log file before deleting it, tertiary to the log\.retention\.ms property\. | 
| log\.retention\.minutes | Number of minutes to keep a log file before deleting it, secondary to log\.retention\.ms property\. If not set, the value in log\.retention\.hours is used\. | 
| log\.retention\.ms | Number of milliseconds to keep a log file before deleting it \(in milliseconds\), If not set, the value in log\.retention\.minutes is used\. | 
| log\.roll\.ms | Maximum time before a new log segment is rolled out \(in milliseconds\)\. If you don't set this property, the value in log\.roll\.hours is used\. The minimum possible value for this property is 1\. | 
| log\.segment\.bytes | Maximum size of a single log file\. | 
| max\.incremental\.fetch\.session\.cache\.slots | Maximum number of incremental fetch sessions that are maintained\. | 
| message\.max\.bytes |  Largest record batch size allowed by Kafka\. If this is increased and there are consumers older than 0\.10\.2, the consumers' fetch size must also be increased so that the they can fetch record batches this large\. In the latest message format version, records are always grouped into batches for efficiency\. In previous message format versions, uncompressed records are not grouped into batches and this limit only applies to a single record in that case\. This can be set per topic with the topic level max\.message\.bytes config\.  | 
| min\.insync\.replicas |  When a producer sets acks to `"all"` \(or `"-1"`\), min\.insync\.replicas specifies the minimum number of replicas that must acknowledge a write for the write to be considered successful\. If this minimum cannot be met, the producer raises an exception \(either NotEnoughReplicas or NotEnoughReplicasAfterAppend\)\. When used together, min\.insync\.replicas and acks enable you to enforce greater durability guarantees\. A typical scenario would be to create a topic with a replication factor of 3, set min\.insync\.replicas to 2, and produce with acks of `"all"`\. This ensures that the producer raises an exception if a majority of replicas don't receive a write\.  | 
| num\.io\.threads | The number of threads that the server uses for processing requests, which may include disk I/O\. | 
| num\.network\.threads | The number of threads that the server uses for receiving requests from the network and sending responses to it\. | 
| num\.partitions | Default number of log partitions per topic\. | 
| num\.recovery\.threads\.per\.data\.dir | The number of threads per data directory to be used for log recovery at startup and for flushing at shutdown\. | 
| num\.replica\.fetchers | The number of fetcher threads used to replicate messages from a source broker\. Increasing this value can increase the degree of I/O parallelism in the follower broker\. | 
| offsets\.retention\.minutes | After a consumer group loses all its consumers \(that is, it becomes empty\) its offsets are kept for this retention period before getting discarded\. For standalone consumers \(that is, using manual assignment\), offsets are expired after the time of the last commit plus this retention period\. | 
| offsets\.topic\.replication\.factor | The replication factor for the offsets topic \(set higher to ensure availability\)\. Internal topic creation fails until the cluster size meets this replication factor requirement\. | 
| replica\.fetch\.max\.bytes | Number of bytes of messages to attempt to fetch for each partition\. This is not an absolute maximum\. If the first record batch in the first non\-empty partition of the fetch is larger than this value, the record batch is returned to ensure that progress can be made\. The maximum record batch size accepted by the broker is defined via message\.max\.bytes \(broker config\) or max\.message\.bytes \(topic config\)\. | 
| replica\.fetch\.response\.max\.bytes | The maximum number of bytes expected for the entire fetch response\. Records are fetched in batches, and if the first record batch in the first non\-empty partition of the fetch is larger than this value, the record batch will still be returned to ensure that progress can be made\. This isn't an absolute maximum\. The message\.max\.bytes \(broker config\) or max\.message\.bytes \(topic config\) properties specify the maximum record batch size that the broker accepts\. | 
| replica\.lag\.time\.max\.ms | If a follower hasn't sent any fetch requests or hasn't consumed up to the leader's log end offset for at least this number of milliseconds, the leader removes the follower from the ISR\.MinValue: 10000MaxValue \(inclusive\) = 30000 | 
| replica\.selector\.class | The fully\-qualified class name that implements ReplicaSelector\. This is used by the broker to find the preferred read replica\. If you are using Apache Kafka version 2\.4\.1 or higher, and want to allow consumers to fetch from the closest replica, set this property to org\.apache\.kafka\.common\.replica\.RackAwareReplicaSelector\. For more information, see [Apache Kafka version 2\.4\.1 \(use 2\.4\.1\.1 instead\)](supported-kafka-versions.md#2.4.1)\. | 
| replica\.socket\.receive\.buffer\.bytes | The socket receive buffer for network requests\. | 
| socket\.receive\.buffer\.bytes | The SO\_RCVBUF buffer of the socket server sockets\. The minimum value to which you can set this property is \-1\. If the value is \-1, Amazon MSK uses the OS default\. | 
| socket\.request\.max\.bytes | The maximum number of bytes in a socket request\. | 
| socket\.send\.buffer\.bytes | The SO\_SNDBUF buffer of the socket server sockets\. The minimum value to which you can set this property is \-1\. If the value is \-1, Amazon MSK uses the OS default\. | 
| transaction\.max\.timeout\.ms | Maximum timeout for transactions\. If a client's requested transaction time exceed this, the broker returns an error in InitProducerIdRequest\. This prevents a client from too large of a timeout, which can stall consumers reading from topics included in the transaction\. | 
| transaction\.state\.log\.min\.isr | Overridden min\.insync\.replicas config for the transaction topic\. | 
| transaction\.state\.log\.replication\.factor | The replication factor for the transaction topic\. Set it to a higher value to increase availability\. Internal topic creation fails until the cluster size meets this replication factor requirement\. | 
| transactional\.id\.expiration\.ms | The time in milliseconds that the transaction coordinator waits without receiving any transaction status updates for the current transaction before expiring its transactional ID\. This setting also influences producer ID expiration: producer IDs are expired when this time elapses after the last write with the given producer ID\. Producer IDs might expire sooner if the last write from the producer ID is deleted due to the topic's retention settings\. The minimum value for this property is 1 millisecond\. | 
| unclean\.leader\.election\.enable | Indicates whether to enable replicas not in the ISR set to be elected as leader as a last resort, even though doing so may result in data loss\. | 
| zookeeper\.connection\.timeout\.ms | Maximum time that the client waits to establish a connection to ZooKeeper\. If not set, the value in zookeeper\.session\.timeout\.ms is used\. | 
| zookeeper\.session\.timeout\.ms |  The Apache ZooKeeper session timeout in milliseconds\. MinValue = 6000 MaxValue \(inclusive\) = 18000  | 

To learn how you can create a custom MSK configuration, list all configurations, or describe them, see [Amazon MSK configuration operations](msk-configuration-operations.md)\. To create an MSK cluster using a custom MSK configuration or to update a cluster with a new custom configuration, see [Amazon MSK: How it works](operations.md)\.

When you update your existing MSK cluster with a custom MSK configuration, Amazon MSK does rolling restarts when necessary, using best practices to minimize customer downtime\. For example, after Amazon MSK restarts each broker, it tries to let the broker catch up on data that the broker might have missed during the configuration update before it moves to the next broker\.

## Dynamic configuration<a name="msk-dynamic-confinguration"></a>

In addition to the configuration properties that Amazon MSK provides, you can dynamically set cluster\- and broker\-level configuration properties that don't require a broker restart\. You can dynamically set configuration properties that aren't marked as read\-only in the table under [Broker Configs](https://kafka.apache.org/documentation/#brokerconfigs) in the Apache Kafka documentation\. For information about dynamic configuration and example commands, see [Updating Broker Configs](https://kafka.apache.org/documentation/#dynamicbrokerconfigs) in the Apache Kafka documentation\.

**Note**  
You can set the `advertised.listeners` property, but not the `listeners` property\.

## Topic\-level configuration<a name="msk-topic-confinguration"></a>

You can use Apache Kafka commands to set or modify topic\-level configuration properties for new and existing topics\. For more information about topic\-level configuration properties and examples on how to set them, see [Topic\-Level Configs](https://kafka.apache.org/documentation/#topicconfigs) in the Apache Kafka documentation\.

## Configuration states<a name="msk-configuration-states"></a>

Amazon MSK configurations can be in the following states\. To perform an operation on a configuration, the configuration must be in the `ACTIVE` or `DELETE_FAILED` state:
+ `ACTIVE`
+ `DELETING`
+ `DELETE_FAILED`