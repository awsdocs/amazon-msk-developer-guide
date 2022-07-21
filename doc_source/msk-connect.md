# MSK Connect<a name="msk-connect"></a>

## What is MSK Connect?<a name="what-is-mkc"></a>

MSK Connect is a feature of Amazon MSK that makes it easy for developers to stream data to and from their Apache Kafka clusters\. MSK Connect uses Kafka Connect 2\.7\.1, an open\-source framework for connecting Apache Kafka clusters with external systems such as databases, search indexes, and file systems\. With MSK Connect, you can deploy fully managed connectors built for Kafka Connect that move data into or pull data from popular data stores like Amazon S3 and Amazon OpenSearch Service\. You can deploy connectors developed by 3rd parties like Debezium for streaming change logs from databases into an Apache Kafka cluster, or deploy an existing connector with no code changes\. Connectors automatically scale to adjust for changes in load and you pay only for the resources that you use\.

Use source connectors to import data from external systems into your topics\. With sink connectors, you can export data from your topics to external systems\.

MSK Connect supports connectors for any Apache Kafka cluster with connectivity to an Amazon VPC, whether it is an MSK cluster or an independently hosted Apache Kafka cluster\. 

MSK Connect continuously monitors connector health and delivery state, patches and manages the underlying hardware, and autoscales the connectors to match changes in throughput\.

To get started using MSK Connect, see [Getting started using MSK Connect](msk-connect-getting-started.md)\. 

To learn about the AWS resources that you can create with MSK Connect, see [Connectors](msk-connect-connectors.md), [Plugins](msk-connect-plugins.md), and [Workers](msk-connect-workers.md)\.

For information about the MSK Connect API, see the [Amazon MSK Connect API Reference](https://docs.aws.amazon.com/MSKC/latest/mskc/Welcome.html)\. 