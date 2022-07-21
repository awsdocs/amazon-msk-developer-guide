# MSK Serverless<a name="serverless"></a>

**Note**  
MSK Serverless is available in the US East \(Ohio\), US East \(N\. Virginia\), US West \(Oregon\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), Europe \(Frankfurt\), Europe \(Stockholm\) and Europe \(Ireland\) Regions\.

MSK Serverless is a cluster type for Amazon MSK that makes it possible for you to run Apache Kafka without having to manage and scale cluster capacity\. It automatically provisions and scales capacity while managing the partitions in your topic, so you can stream data without thinking about right\-sizing or scaling clusters\. MSK Serverless offers a throughput\-based pricing model, so you pay only for what you use\. Consider using a serverless cluster if your applications need on\-demand streaming capacity that scales up and down automatically\.

MSK Serverless is fully compatible with Apache Kafka, so you can use any compatible client applications to produce and consume data\. It also integrates with the following services:
+ AWS PrivateLink to provide private connectivity
+ AWS Identity and Access Management \(IAM\) for authentication and authorization 
+ AWS Glue Schema Registry for schema management
+ Amazon Kinesis Data Analytics for Apache Flink\-based stream processing
+  AWS Lambda for event processing

MSK Serverless requires IAM access control for all clusters\. For more information, see [IAM access control](iam-access-control.md)\.

For information about the service quota that apply to MSK Serverless, see [MSK Serverless quota](limits.md#serverless-quota)\.

To help you get started with serverless clusters, and to learn more about configuration and monitoring options for serverless clusters, see the following\.

**Topics**
+ [Getting started using MSK Serverless clusters](serverless-getting-started.md)
+ [Configuration for serverless clusters](serverless-config.md)
+ [Monitoring serverless clusters](serverless-monitoring.md)