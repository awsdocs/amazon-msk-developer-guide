# Amazon MSK Metrics for Monitoring with CloudWatch<a name="metrics-details"></a>

Amazon MSK integrates with Amazon CloudWatch so that you can collect, view, and analyze CloudWatch metrics for your Amazon MSK cluster\. The metrics that you configure for your MSK cluster are automatically collected and pushed to CloudWatch\. You can set the monitoring level for an MSK cluster to one of the following: `DEFAULT`, `PER_BROKER`, `PER_TOPIC_PER_BROKER`, or `PER_TOPIC_PER_PARTITION`\. The tables in the following sections show all the metrics that are available starting at each monitoring level\.

`DEFAULT`\-level metrics are free\. Pricing for other metrics is described in the [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/) page\.

## `DEFAULT` Level Monitoring<a name="default-metrics"></a>

The metrics described in the following table are available at the `DEFAULT` monitoring level\. They are free\.


**Metrics available at the `DEFAULT` monitoring level**  

| Name | When Visible | Dimensions | Description | 
| --- | --- | --- | --- | 
| ActiveControllerCount | After the cluster gets to the ACTIVE state\. | Cluster Name | Only one controller per cluster should be active at any given time\. | 
| BurstBalance |  After the cluster gets to the ACTIVE state\.  |  Cluster Name , Broker ID  |  The remaining balance of input\-output burst credits for EBS volumes in the cluster\. Use it to investigate latency or decreased throughput\. `BurstBalance` is not reported for EBS volumes when the baseline performance of a volume is higher than the maximum burst performance\. For more information, see [I/O Credits and burst performance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#IOcredit)\.  | 
| BytesInPerSec | After you create a topic\. | Cluster Name, Broker ID, Topic | The number of bytes per second received from clients\. This metric is available per broker and also per topic\. | 
| BytesOutPerSec | After you create a topic\. | Cluster Name, Broker ID, Topic | The number of bytes per second sent to clients\. This metric is available per broker and also per topic\. | 
| ConnectionCount |  After the cluster gets to the ACTIVE state\.  |  Cluster Name, Broker ID  |  The number active Apache Kafka connections\.   | 
| CPUCreditBalance  |  After the cluster gets to the ACTIVE state\.  |  Cluster Name, Broker ID  |  This metric can help you monitor CPU credit balance on the brokers\. If your CPU usage is sustained above the baseline level of 20% utilization, you can run out of the CPU credit balance, which can have a negative impact on cluster performance\. You can take steps to reduce CPU load\. For example, you can reduce the number of client requests or update the broker type to an M5 broker type\.  | 
| CpuIdle | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The percentage of CPU idle time\. | 
| CpuSystem | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The percentage of CPU in kernel space\. | 
| CpuUser | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The percentage of CPU in user space\. | 
| GlobalPartitionCount | After the cluster gets to the ACTIVE state\. | Cluster Name | The number of partitions across all topics in the cluster, excluding replicas\. Because GlobalPartitionCount doesn't include replicas, the sum of the PartitionCount values can be higher than GlobalPartitionCount if the replication factor for a topic is greater than 1\. | 
| GlobalTopicCount | After the cluster gets to the ACTIVE state\. | Cluster Name | Total number of topics across all brokers in the cluster\. | 
| EstimatedMaxTimeLag | After consumer group consumes from a topic\. | Consumer Group, Topic | Time estimate \(in seconds\) to drain MaxOffsetLag\. | 
| KafkaAppLogsDiskUsed | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The percentage of disk space used for application logs\. | 
| KafkaDataLogsDiskUsed \(Cluster Name, Broker ID dimension\) | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The percentage of disk space used for data logs\. | 
| KafkaDataLogsDiskUsed \(Cluster Name dimension\) | After the cluster gets to the ACTIVE state\. | Cluster Name | The percentage of disk space used for data logs\. | 
| LeaderCount | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The total number of leaders of partitions per broker, not including replicas\. | 
| MaxOffsetLag | After consumer group consumes from a topic\. | Consumer Group, Topic | The maximum offset lag across all partitions in a topic\. | 
| MemoryBuffered | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The size in bytes of buffered memory for the broker\. | 
| MemoryCached | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The size in bytes of cached memory for the broker\. | 
| MemoryFree | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The size in bytes of memory that is free and available for the broker\. | 
| HeapMemoryAfterGC  |  After the cluster gets to the ACTIVE state\.  |  Cluster Name, Broker ID  | The percentage of total heap memory available after garbage collection\. | 
| MemoryUsed | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The size in bytes of memory that is in use for the broker\. | 
| MessagesInPerSec | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The number of incoming messages per second for the broker\. | 
| NetworkRxDropped | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The number of dropped receive packages\. | 
| NetworkRxErrors | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The number of network receive errors for the broker\. | 
| NetworkRxPackets | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The number of packets received by the broker\. | 
| NetworkTxDropped | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The number of dropped transmit packages\. | 
| NetworkTxErrors | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The number of network transmit errors for the broker\. | 
| NetworkTxPackets | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The number of packets transmitted by the broker\. | 
| OfflinePartitionsCount | After the cluster gets to the ACTIVE state\. | Cluster Name | Total number of partitions that are offline in the cluster\. | 
| PartitionCount | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The total number of topic partitions per broker, including replicas\. | 
| ProduceTotalTimeMsMean | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The mean produce time in milliseconds\. | 
| RequestBytesMean | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The mean number of request bytes for the broker\. | 
| RequestTime | After request throttling is applied\. | Cluster Name, Broker ID | The average time in milliseconds spent in broker network and I/O threads to process requests\. | 
| RootDiskUsed | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The percentage of the root disk used by the broker\. | 
| SumOffsetLag | After consumer group consumes from a topic\. | Consumer Group, Topic | The aggregated offset lag for all the partitions in a topic\. | 
| SwapFree | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The size in bytes of swap memory that is available for the broker\. | 
| SwapUsed  | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The size in bytes of swap memory that is in use for the broker\. | 
| TrafficShaping  |  After the cluster gets to the ACTIVE state\.  |  Cluster Name, Broker ID  |  High\-level metrics indicating the number of packets dropped due to exceeding network allocations\. Finer detail is available with PER\_BROKER metrics\.  | 
| UnderMinIsrPartitionCount | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The number of under minIsr partitions for the broker\. | 
| UnderReplicatedPartitions | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The number of under\-replicated partitions for the broker\. | 
| ZooKeeperRequestLatencyMsMean  | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | The mean latency in milliseconds for Apache ZooKeeper requests from broker\. | 
| ZooKeeperSessionState | After the cluster gets to the ACTIVE state\. | Cluster Name, Broker ID | Connection status of broker's ZooKeeper session which may be one of the following: NOT\_CONNECTED: '0\.0', ASSOCIATING: '0\.1', CONNECTING: '0\.5', CONNECTEDREADONLY: '0\.8', CONNECTED: '1\.0', CLOSED: '5\.0', AUTH\_FAILED: '10\.0'\. | 

## `PER_BROKER` Level Monitoring<a name="broker-metrics"></a>

When you set the monitoring level to `PER_BROKER`, you get the metrics described in the following table in addition to all the `DEFAULT` level metrics\. You pay for the metrics in the following table, whereas the `DEFAULT` level metrics continue to be free\. The metrics in this table have the following dimensions: Cluster Name, Broker ID\.


**Additional metrics that are available starting at the `PER_BROKER` monitoring level**  

| Name | When Visible | Description | 
| --- | --- | --- | 
| BwInAllowanceExceeded | After the cluster gets to the ACTIVE state\. |  The number of packets shaped because the inbound aggregate bandwidth exceeded the maximum for the broker\.  | 
| BwOutAllowanceExceeded | After the cluster gets to the ACTIVE state\. |  The number of packets shaped because the outbound aggregate bandwidth exceeded the maximum for the broker\.  | 
| ConnTrackAllowanceExceeded  | After the cluster gets to the ACTIVE state\. |  The number of packets shaped because the connection tracking exceeded the maximum for the broker\. Connection tracking is related to security groups that track each connection established to ensure that return packets are delivered as expected\.   | 
| ConnectionCloseRate | After the cluster gets to the ACTIVE state\. |  The number of connections closed per second per listener\. This number is aggregated per listener and filtered for the client listeners\.  | 
| ConnectionCreationRate | After the cluster gets to the ACTIVE state\. |  The number of new connections established per second per listener\. This number is aggregated per listener and filtered for the client listeners\.  | 
| CpuCreditUsage | After the cluster gets to the ACTIVE state\. |  This metric can help you monitor CPU credit usage on the instances\. If your CPU usage is sustained above the baseline level of 20%, you can run out of the CPU credit balance, which can have a negative impact on cluster performance\. You can monitor and alarm on this metric to take corrective actions\.  | 
| FetchConsumerLocalTimeMsMean | After there's a producer/consumer\. | The mean time in milliseconds that the consumer request is processed at the leader\. | 
| FetchConsumerRequestQueueTimeMsMean | After there's a producer/consumer\. | The mean time in milliseconds that the consumer request waits in the request queue\. | 
| FetchConsumerResponseQueueTimeMsMean | After there's a producer/consumer\. | The mean time in milliseconds that the consumer request waits in the response queue\. | 
| FetchConsumerResponseSendTimeMsMean | After there's a producer/consumer\. | The mean time in milliseconds for the consumer to send a response\. | 
| FetchConsumerTotalTimeMsMean | After there's a producer/consumer\. | The mean total time in milliseconds that consumers spend on fetching data from the broker\. | 
| FetchFollowerLocalTimeMsMean | After there's a producer/consumer\. | The mean time in milliseconds that the follower request is processed at the leader\. | 
| FetchFollowerRequestQueueTimeMsMean | After there's a producer/consumer\. | The mean time in milliseconds that the follower request waits in the request queue\. | 
| FetchFollowerResponseQueueTimeMsMean | After there's a producer/consumer\. | The mean time in milliseconds that the follower request waits in the response queue\. | 
| FetchFollowerResponseSendTimeMsMean | After there's a producer/consumer\. | The mean time in milliseconds for the follower to send a response\. | 
| FetchFollowerTotalTimeMsMean | After there's a producer/consumer\. | The mean total time in milliseconds that followers spend on fetching data from the broker\. | 
| FetchMessageConversionsPerSec | After you create a topic\. | The number of fetch message conversions per second for the broker\. | 
| FetchThrottleByteRate | After bandwidth throttling is applied\. | The number of throttled bytes per second\. | 
| FetchThrottleQueueSize | After bandwidth throttling is applied\. | The number of messages in the throttle queue\. | 
| FetchThrottleTime | After bandwidth throttling is applied\. | The average fetch throttle time in milliseconds\. | 
| NetworkProcessorAvgIdlePercent | After the cluster gets to the ACTIVE state\. | The average percentage of the time the network processors are idle\. | 
| PpsAllowanceExceeded | After the cluster gets to the ACTIVE state\. |  The number of packets shaped because the bidirectional PPS exceeded the maximum for the broker\.  | 
| ProduceLocalTimeMsMean | After the cluster gets to the ACTIVE state\. | The mean time in milliseconds for the follower to send a response\. | 
| ProduceMessageConversionsPerSec | After you create a topic\. | The number of produce message conversions per second for the broker\. | 
| ProduceMessageConversionsTimeMsMean | After the cluster gets to the ACTIVE state\. | The mean time in milliseconds spent on message format conversions\. | 
| ProduceRequestQueueTimeMsMean | After the cluster gets to the ACTIVE state\. | The mean time in milliseconds that request messages spend in the queue\. | 
| ProduceResponseQueueTimeMsMean | After the cluster gets to the ACTIVE state\. | The mean time in milliseconds that response messages spend in the queue\. | 
| ProduceResponseSendTimeMsMean | After the cluster gets to the ACTIVE state\. | The mean time in milliseconds spent on sending response messages\. | 
| ProduceThrottleByteRate | After bandwidth throttling is applied\. | The number of throttled bytes per second\. | 
| ProduceThrottleQueueSize | After bandwidth throttling is applied\. | The number of messages in the throttle queue\. | 
| ProduceThrottleTime | After bandwidth throttling is applied\. | The average produce throttle time in milliseconds\. | 
| ProduceTotalTimeMsMean | After the cluster gets to the ACTIVE state\. | The mean produce time in milliseconds\. | 
| ReplicationBytesInPerSec | After you create a topic\. | The number of bytes per second received from other brokers\. | 
| ReplicationBytesOutPerSec | After you create a topic\. | The number of bytes per second sent to other brokers\. | 
| RequestExemptFromThrottleTime | After request throttling is applied\. | The average time in milliseconds spent in broker network and I/O threads to process requests that are exempt from throttling\. | 
| RequestHandlerAvgIdlePercent | After the cluster gets to the ACTIVE state\. | The average percentage of the time the request handler threads are idle\. | 
| RequestThrottleQueueSize | After request throttling is applied\. | The number of messages in the throttle queue\. | 
| RequestThrottleTime | After request throttling is applied\. | The average request throttle time in milliseconds\. | 
| TcpConnections | After the cluster gets to the ACTIVE state\. |  Shows number of incoming and outgoing TCP segments with the SYN flag set\.  | 
| TrafficBytes | After the cluster gets to the ACTIVE state\. |  Shows network traffic in overall bytes between clients \(producers and consumers\) and brokers\. Traffic between brokers isn't reported\.  | 
| VolumeQueueLength | After the cluster gets to the ACTIVE state\. |  The number of read and write operation requests waiting to be completed in a specified time period\.  | 
|  VolumeReadBytes  | After the cluster gets to the ACTIVE state\. |  The number of bytes read in a specified time period\.  | 
| VolumeReadOps  | After the cluster gets to the ACTIVE state\. |  The number of read operations in a specified time period\.  | 
| VolumeTotalReadTime  | After the cluster gets to the ACTIVE state\. |  The total number of seconds spent by all read operations that completed in a specified time period\.  | 
| VolumeTotalWriteTime  | After the cluster gets to the ACTIVE state\. |  The total number of seconds spent by all write operations that completed in a specified time period\.  | 
| VolumeWriteBytes  | After the cluster gets to the ACTIVE state\. |  The number of bytes written in a specified time period\.  | 
| VolumeWriteOps  | After the cluster gets to the ACTIVE state\. |  The number of write operations in a specified time period\.  | 

## `PER_TOPIC_PER_BROKER` Level Monitoring<a name="broker-topic-metrics"></a>

When you set the monitoring level to `PER_TOPIC_PER_BROKER`, you get the metrics described in the following table, in addition to all the metrics from the `PER_BROKER` and DEFAULT levels\. Only the `DEFAULT` level metrics are free\. The metrics in this table have the following dimensions: Cluster Name, Broker ID, Topic\.

**Important**  
For an Amazon MSK cluster that uses Apache Kafka 2\.4\.1 or a newer version, the metrics in the following table appear only after their values become nonzero for the first time\. For example, to see `BytesInPerSec`, one or more producers must first send data to the cluster\. 


**Additional metrics that are available starting at the `PER_TOPIC_PER_BROKER` monitoring level**  

| Name | When Visible | Description | 
| --- | --- | --- | 
| FetchMessageConversionsPerSec | After you create a topic\. | The number of fetched messages converted per second\. | 
| MessagesInPerSec | After you create a topic\. | The number of messages received per second\. | 
| ProduceMessageConversionsPerSec | After you create a topic\. | The number of conversions per second for produced messages\. | 

## `PER_TOPIC_PER_PARTITION` Level Monitoring<a name="topic-partition-metrics"></a>

When you set the monitoring level to `PER_TOPIC_PER_PARTITION`, you get the metrics described in the following table, in addition to all the metrics from the `PER_TOPIC_PER_BROKER`, `PER_BROKER`, and DEFAULT levels\. Only the `DEFAULT` level metrics are free\. The metrics in this table have the following dimensions: Consumer Group, Topic, Partition\.


**Additional metrics that are available starting at the `PER_TOPIC_PER_PARTITION` monitoring level**  

| Name | When Visible | Description | 
| --- | --- | --- | 
| EstimatedTimeLag | After consumer group consumes from a topic\. | Time estimate \(in seconds\) to drain the partition offset lag\. | 
| OffsetLag | After consumer group consumes from a topic\. | Partition\-level consumer lag in number of offsets\. | 