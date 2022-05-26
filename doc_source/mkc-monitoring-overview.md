# Monitoring MSK Connect<a name="mkc-monitoring-overview"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of MSK Connect and your other AWS solutions\. Amazon CloudWatch monitors your AWS resources and the applications that you run on AWS in real time\. You can collect and track metrics, create customized dashboards, and set alarms that notify you or take actions when a specified metric reaches a threshold that you specify\. For example, you can have CloudWatch track CPU usage or other metrics of your connector, so that you can increase its capacity if needed\. For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

The following table shows the metrics that MSK Connect sends to CloudWatch under the `ConnectorName` dimension\. MSK Connect delivers these metrics by default and at no additional cost\. CloudWatch keeps these metrics for 15 months, so that you can access historical information and gain a better perspective on how your connectors are performing\. You can also set alarms that watch for certain thresholds, and send notifications or take actions when those thresholds are met\. For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.


**MSK Connect metrics**  

| Metric name | Description | 
| --- | --- | 
| BytesInPerSec | The total number of bytes received by the connector\. | 
| BytesOutPerSec | The total number of bytes delivered by the connector\. | 
| CpuUtilization | The percentage of CPU consumption by system and user\. | 
| ErroredTaskCount | The number of tasks that have errored out\. | 
| MemoryUtilization | The percentage of memory consumption for the connector\. | 
| RebalanceCompletedTotal | The total number of rebalances completed by this connector\. | 
| RebalanceTimeAvg | The average time in milliseconds spent by the connector on rebalancing\.  | 
| RebalanceTimeMax | The maximum time in milliseconds spent by the connector on rebalancing\. | 
| RebalanceTimeSinceLast |  The time in milliseconds since this connector completed the most recent rebalance\.  | 
| RunningTaskCount | The running number of tasks in the connector\. | 
| SinkRecordReadRate | The average per\-second number of records read from the Apache Kafka or Amazon MSK cluster\. | 
| SinkRecordSendRate | The average per\-second number of records that are output from the transformations and sent to the destination\. This number doesn't include filtered records\. | 
| SourceRecordPollRate | The average per\-second number of records produced or polled\. | 
| SourceRecordWriteRate | The average per\-second number of records output from the transformations and written to the Apache Kafka or Amazon MSK cluster\. | 
| TaskStartupAttemptsTotal | The total number of task startups that the connector has attempted\. You can use this metric to identify anomalies in task startup attempts\. | 
| TaskStartupSuccessPercentage | The average percentage of successful task starts for the connector\. You can use this metric to identify anomalies in task startup attempts\. | 
| WorkerCount | The number of workers that are running in the connector\. | 