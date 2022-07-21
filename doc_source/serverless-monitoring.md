# Monitoring serverless clusters<a name="serverless-monitoring"></a>

Amazon MSK integrates with Amazon CloudWatch so that you can collect, view, and analyze metrics for your MSK Serverless cluster\. The metrics shown in the following table are available for all serverless clusters\. As these metrics are published as individual data points for each partition in the topic, we recommend viewing them as a 'SUM' statistic to get the topic\-level view\.

Amazon MSK publishes `PerSec` metrics to CloudWatch at a frequency of once per minute\. This means that the 'SUM' statistic for a one\-minute period accurately represents per\-second data for `PerSec` metrics\. To collect per\-second data for a period of longer than one minute, use the following CloudWatch math expression: `m1 * 60/PERIOD(m1)`\.


**Metrics available at the DEFAULT monitoring level**  

| Name | When visible | Dimensions | Description | 
| --- | --- | --- | --- | 
| BytesInPerSec | After a producer writes to a topic | Cluster Name, Topic | The number of bytes per second received from clients\. This metric is available for each broker and also for each topic\. | 
| BytesOutPerSec | After a consumer group consumes from a topic | Cluster Name, Topic | The number of bytes per second sent to clients\. This metric is available for each broker and also for each topic\. | 
| FetchMessageConversionsPerSec | After a consumer group consumes from a topic | Cluster Name, Topic | The number of fetch message conversions per second for the broker\. | 
| MaxEstimatedTimeLag | After a consumer group consumes from a topic | Cluster Name, Consumer Group, Topic  | A time estimate of the MaxOffsetLag metric\. | 
| MaxOffsetLag | After a consumer group consumes from a topic | Cluster Name, Consumer Group, Topic  | The maximum offset lag across all partitions in a topic\. | 
| MessagesInPerSec | After a producer writes to a topic | Cluster Name, Topic | The number of incoming messages per second for the broker\. | 
| ProduceMessageConversionsPerSec | After a producer writes to a topic | Cluster Name, Topic | The number of produce message conversions per second for the broker\. | 
| SumOffsetLag | After a consumer group consumes from a topic | Cluster Name, Consumer Group, Topic  | The aggregated offset lag for all the partitions in a topic\. | 

**To view MSK Serverless metrics**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, under **Metrics**, choose **All metrics**\.

1. In the metrics search for the term **kafka**\.

1. Choose **AWS/Kafka / Cluster Name, Topic** or **AWS/Kafka / Cluster Name, Consumer Group, Topic** to see different metrics\.