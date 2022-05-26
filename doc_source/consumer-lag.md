# Consumer\-Lag Monitoring<a name="consumer-lag"></a>

Monitoring consumer lag allows you to identify slow or stuck consumers that aren't keeping up with the latest data available in a topic\. When necessary, you can then take remedial actions, such as scaling or rebooting those consumers\. To monitor consumer lag, you can use Amazon CloudWatch or open monitoring with Prometheus\.

Consumer lag metrics quantify the difference between the latest data written to your topics and the data read by your applications\. Amazon MSK provides the following consumer\-lag metrics, which you can get through Amazon CloudWatch or through open monitoring with Prometheus: `EstimatedMaxTimeLag`, `EstimatedTimeLag`, `MaxOffsetLag`, `OffsetLag`, and `SumOffsetLag`\. For information about these metrics, see [Amazon MSK Metrics for Monitoring with CloudWatch](metrics-details.md)\.

Amazon MSK supports consumer lag metrics for clusters with Apache Kafka 2\.2\.1 or a later version\.

**Note**  
To turn on consumer\-lag monitoring for a cluster that was created before November 23, 2020, ensure that the cluster is running Apache Kafka 2\.2\.1 or a later version, then [create a support case](https://console.aws.amazon.com/support/home#/)\. 