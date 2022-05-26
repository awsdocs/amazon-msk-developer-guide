# Connectors<a name="msk-connect-connectors"></a>

A connector integrates external systems and Amazon services with Apache Kafka by continuously copying streaming data from a data source into your Apache Kafka cluster, or continuously copying data from your cluster into a data sink\. A connector can also perform lightweight logic such as transformation, format conversion, or filtering data before delivering the data to a destination\. Source connectors pull data from a data source and push this data into the cluster, while sink connectors pull data from the cluster and push this data into a data sink\.

The following diagram shows the architecture of a connector\. A worker is a Java virtual machine \(JVM\) process that runs the connector logic\. Each worker creates a set of tasks that run in parallel threads and do the work of copying the data\. Tasks don't store state, and can therefore be started, stopped, or restarted at any time in order to provide a resilient and scalable data pipeline\.

![\[Diagram showing the architecture of a connector cluster.\]](http://docs.aws.amazon.com/msk/latest/developerguide/images/mkc-worker-architecture.png)

## Connector capacity<a name="msk-connect-capacity"></a>

The total capacity of a connector depends on the number of workers that the connector has, as well as on the number of MSK Connect Units \(MCUs\) per worker\. Each MCU represents 1 vCPU of compute and 4 GiB of memory\. To create a connector, you must choose between one of the following two capacity modes\.
+ *Provisioned* \- Choose this mode if you know the capacity requirements for your connector\. You specify two values:
  + The number of workers\.
  + The number of MCUs per worker\.
+ *Autoscaled* \- Choose this mode if the capacity requirements for your connector are variable or if you don't know them in advance\. When you use autoscaled mode, Amazon MSK Connect overrides your connector's `tasks.max` property with a value that is proportional to the number of workers running in the connector and the number of MCUs per worker\. 

  You specify three sets of values:
  + The minimum and maximum number of workers\.
  + The scale\-in and scale\-out percentages for CPU utilization, which is determined by the `CpuUtilization` metric\. When the `CpuUtilization` metric for the connector exceeds the scale\-out percentage, MSK Connect increases the number of workers that are running in the connector\. When the `CpuUtilization` metric goes below the scale\-in percentage, MSK Connect decreases the number of workers\. The number of workers always remains within the minimum and maximum numbers that you specify when you create the connector\.
  + The number of MCUs per worker\.

For more information about workers, see [Workers](msk-connect-workers.md)\. To learn about MSK Connect metrics, see [Monitoring MSK Connect](mkc-monitoring-overview.md)\.

## Creating a connector<a name="mkc-create-connector-intro"></a>

**Creating a connector using the AWS Management Console**

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. In the left pane, under **MSK Connect**, choose **Connectors**\.

1. Choose **Create connector**\.

1. You can choose between using an existing custom plugin to create the connector, or creating a new custom plugin first\. For information on custom plugins and how to create them, see [Plugins](msk-connect-plugins.md)\. In this procedure, let's assume you have a custom plugin that you want to use\. In the list of custom plugins, find the one that you want to use, and select the box to its left, then choose **Next**\.

1. Enter a name and, optionally, a description\.

1. Choose the cluster that you want to connect to\.

1. Specify the connector configuration\. The configuration parameters that you need to specify depend on the type of connector that you want to create\. However, some parameters are common to all connectors, for example, the `connector.class` and `tasks.max` parameters\. The following is an example configuration for the [Confluent Amazon S3 Sink Connector](https://www.confluent.io/hub/confluentinc/kafka-connect-s3)\.

   ```
   connector.class=io.confluent.connect.s3.S3SinkConnector
   tasks.max=2
   topics=my-example-topic
   s3.region=us-east-1
   s3.bucket.name=my-destination-bucket
   flush.size=1
   storage.class=io.confluent.connect.s3.storage.S3Storage
   format.class=io.confluent.connect.s3.format.json.JsonFormat
   partitioner.class=io.confluent.connect.storage.partitioner.DefaultPartitioner
   key.converter=org.apache.kafka.connect.storage.StringConverter
   value.converter=org.apache.kafka.connect.storage.StringConverter
   schema.compatibility=NONE
   ```

1. Next, you configure your connector capacity\. You can choose between two capacity modes: provisioned and auto scaled\. For information about these two options, see [Connector capacity](#msk-connect-capacity)\.

1. Choose either the default worker configuration or a custom worker configuration\. For information about creating custom worker configurations, see [Workers](msk-connect-workers.md)\.

1. Next, you specify the service execution role\. This must be an IAM role that MSK Connect can assume, and that grants the connector all the permissions that it needs to access the necessary AWS resources\. Those permissions depend on the logic of the connector\. For information about how to create this role, see [Service execution role](msk-connect-service-execution-role.md)\.

1. Choose **Next**, review the security information, then choose **Next** again\.

1. Specify the logging options that you want, then choose **Next**\. For information about logging, see [Logging for MSK Connect](msk-connect-logging.md)\.

1. Choose **Create connector**\.

To use the MSK Connect API to create a connector, see [CreateConnector](https://docs.aws.amazon.com/MSKC/latest/mskc/API_CreateConnector.html)\.