# The Default Amazon MSK Configuration<a name="msk-default-configuration"></a>

When you create an MSK cluster without specifying a custom MSK configuration, Amazon MSK creates and uses a default configuration with the values shown in the following table\. For properties that aren't in this table, Amazon MSK uses the defaults associated with your version of Apache Kafka\. For a list of these default values, see [Apache Kafka Configuration](https://kafka.apache.org/documentation/#configuration)\. 


**Default Configuration Values**  

| Name | Description | Default Value | 
| --- | --- | --- | 
| allow\.everyone\.if\.no\.acl\.found | If no resource patterns match a specific resource, the resource has no associated ACLs\. In this case, if this property is set to true, everyone is allowed to access the resource, not just the super users\. | true | 
| auto\.create\.topics\.enable | Enables autocreation of a topic on the server\. | false | 
| auto\.leader\.rebalance\.enable | Enables auto leader balancing\. A background thread checks and triggers leader balance if required at regular intervals\. | true | 
| default\.replication\.factor | Default replication factors for automatically created topics\. | 3 for 3\-AZ clusters, 2 for 2\-AZ clusters | 
| min\.insync\.replicas |  When a producer sets acks to `"all"` \(or `"-1"`\), min\.insync\.replicas specifies the minimum number of replicas that must acknowledge a write for the write to be considered successful\. If this minimum can't be met, the producer raises an exception \(either NotEnoughReplicas or NotEnoughReplicasAfterAppend\)\. When used together, min\.insync\.replicas and acks enable you to enforce greater durability guarantees\. A typical scenario would be to create a topic with a replication factor of 3, set min\.insync\.replicas to 2, and produce with acks of `"all"`\. This ensures that the producer raises an exception if a majority of replicas do not receive a write\.  | 2 for 3\-AZ clusters, 1 for 2\-AZ clusters | 
| num\.io\.threads | Number of threads that the server uses for processing requests, which may include disk I/O\. | 8 | 
| num\.network\.threads | Number of threads that the server uses for receiving requests from the network and sending responses to the network\. | 5 | 
| num\.partitions | Default number of log partitions per topic\. | 1 | 
| num\.replica\.fetchers | Number of fetcher threads used to replicate messages from a source broker\. Increasing this value can increase the degree of I/O parallelism in the follower broker\. | 2 | 
| replica\.lag\.time\.max\.ms | If a follower hasn't sent any fetch requests or hasn't consumed up to the leader's log end offset for at least this number of milliseconds, the leader removes the follower from the ISR\. | 30000 | 
| socket\.receive\.buffer\.bytes | SO\_RCVBUF buffer of the socket sever sockets\. If the value is \-1, the OS default is used\. | 102400 | 
| socket\.request\.max\.bytes | Maximum number of bytes in a socket request\. | 104857600 | 
| socket\.send\.buffer\.bytes | SO\_SNDBUF buffer of the socket sever sockets\. If the value is \-1, the OS default is used\. | 102400 | 
| unclean\.leader\.election\.enable | Indicates whether to enable replicas not in the ISR set to be elected as leader as a last resort, even though doing so may result in data loss\. | true | 
| zookeeper\.session\.timeout\.ms |  The Apache ZooKeeper session timeout in milliseconds\.  | 18000 | 
| zookeeper\.set\.acl | Set client to use secure ACLs\. | false | 

For information about how to specify custom configuration values, see [Custom MSK Configurations](msk-configuration-properties.md)\.