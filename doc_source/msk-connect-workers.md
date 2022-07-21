# Workers<a name="msk-connect-workers"></a>

A worker is a Java virtual machine \(JVM\) process that runs the connector logic\. Each worker creates a set of tasks that run in parallel threads and do the work of copying the data\. Tasks don't store state, and can therefore be started, stopped, or restarted at any time in order to provide a resilient and scalable data pipeline\. Changes to the number of workers, whether due to a scaling event or due to unexpected failures, are automatically detected by the remaining workers\. They coordinate to rebalance tasks across the set of remaining workers\. Connect workers use Apache Kafka's consumer groups to coordinate and rebalance\.

If your connector's capacity requirements are variable or difficult to estimate, you can let MSK Connect scale the number of workers as needed between a lower limit and an upper limit that you specify\. Alternatively, you can specify the exact number of workers that you want to run your connector logic\. For more information, see [Connector capacity](msk-connect-connectors.md#msk-connect-capacity)\.

**Topics**
+ [Default worker configuration](#msk-connect-default-worker-config)
+ [Supported worker configuration properties](#msk-connect-supported-worker-config-properties)
+ [Creating a custom worker configuration](#msk-connect-create-custom-worker-config)
+ [Managing source connector offsets using `offset.storage.topic`](#msk-connect-manage-connector-offsets)

## Default worker configuration<a name="msk-connect-default-worker-config"></a>

MSK Connect provides the following default worker configuration:

```
key.converter=org.apache.kafka.connect.storage.StringConverter
value.converter=org.apache.kafka.connect.storage.StringConverter
```

## Supported worker configuration properties<a name="msk-connect-supported-worker-config-properties"></a>

MSK Connect provides a default worker configuration\. You also have the option to create a custom worker configuration to use with your connectors\. The following list includes information about the worker configuration properties that Amazon MSK Connect does or does not support\.
+ The `key.converter` and `value.converter` properties are required\.
+ MSK Connect supports the following `producer.` configuration properties\.

  ```
  producer.acks
  producer.batch.size
  producer.buffer.memory
  producer.compression.type
  producer.enable.idempotence
  producer.key.serializer
  producer.max.request.size
  producer.metadata.max.age.ms
  producer.metadata.max.idle.ms
  producer.reconnect.backoff.max.ms
  producer.reconnect.backoff.ms
  producer.request.timeout.ms
  producer.retry.backoff.ms
  producer.value.serializer
  ```
+ MSK Connect supports the following `consumer.` configuration properties\.

  ```
  consumer.allow.auto.create.topics
  consumer.auto.offset.reset
  consumer.check.crcs
  consumer.fetch.max.bytes
  consumer.fetch.max.wait.ms
  consumer.fetch.min.bytes
  consumer.heartbeat.interval.ms
  consumer.key.deserializer
  consumer.max.partition.fetch.bytes
  consumer.max.poll.records
  consumer.metadata.max.age.ms
  consumer.partition.assignment.strategy
  consumer.reconnect.backoff.max.ms
  consumer.reconnect.backoff.ms
  consumer.request.timeout.ms
  consumer.retry.backoff.ms
  consumer.session.timeout.ms
  consumer.value.deserializer
  ```
+ All other configuration properties that don't start with the `producer.` or `consumer.` prefixes are supported *except* for the following properties\. 

  ```
  access.control.
  admin.
  admin.listeners.https.
  client.
  connect.
  inter.worker.
  internal.
  listeners.https.
  metrics.
  metrics.context.
  rest.
  sasl.
  security.
  socket.
  ssl.
  topic.tracking.
  worker.
  bootstrap.servers
  config.storage.topic
  connections.max.idle.ms
  connector.client.config.override.policy
  group.id
  listeners
  metric.reporters
  plugin.path
  receive.buffer.bytes
  response.http.headers.config
  scheduled.rebalance.max.delay.ms
  send.buffer.bytes
  status.storage.topic
  ```

For more information about worker configuration properties and what they represent, see [Kafka Connect Configs](https://kafka.apache.org/documentation/#connectconfigs) in the Apache Kafka documentation\.

## Creating a custom worker configuration<a name="msk-connect-create-custom-worker-config"></a>

**Creating a custom worker configuration using the AWS Management Console**

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. In the left pane, under **MSK Connect**, choose **Worker configurations**\.

1. Choose **Create worker configuration**\.

1. Enter a name and an optional description, then add the properties and values that you want to set them to\.

1. Choose **Create worker configuration**\.

To use the MSK Connect API to create a worker configuration, see [CreateWorkerConfiguration](https://docs.aws.amazon.com/MSKC/latest/mskc/API_CreateWorkerConfiguration.html)\.

## Managing source connector offsets using `offset.storage.topic`<a name="msk-connect-manage-connector-offsets"></a>

This section provides information to help you manage source connector offsets using the *offset storage topic*\. The offset storage topic is an internal topic that Kafka Connect uses to store connector and task configuration offsets\.

### Using the default offset storage topic<a name="msk-connect-default-offset-storage-topic"></a>

By default, Amazon MSK Connect generates a new offset storage topic on your Kafka cluster for each connector that you create\. MSK constructs the default topic name using parts of the connector ARN\. For example, `__amazon_msk_connect_offsets_my-mskc-connector_12345678-09e7-4abc-8be8-c657f7e4ff32-2`\. 

### Specifying your own offset storage topic<a name="msk-connect-set-offset-storage-topic"></a>

To provide offset continuity between source connectors, you can use an offset storage topic of your choice instead of the default topic\. Specifying an offset storage topic helps you accomplish tasks like creating a source connector that resumes reading from the last offset of a previous connector\.

To specify an offset storage topic, you supply a value for the `offset.storage.topic` property in your worker configuration before you create a connector\. If you want to reuse the offset storage topic to consume offsets from a previously created connector, you must give the new connector the same name as the old connector\. If you create a custom offset storage topic, you must set [https://kafka.apache.org/27/documentation.html#topicconfigs_cleanup.policy](https://kafka.apache.org/27/documentation.html#topicconfigs_cleanup.policy) to `compact` in your topic configuration\.

**Note**  
If you specify an offset storage topic when you create a *sink* connector, MSK Connect creates the topic if it does not already exist\. However, the topic will not be used to store connector offsets\.   
Sink connector offsets are instead managed using the Kafka consumer group protocol\. Each sink connector creates a group named `connect-{CONNECTOR_NAME}`\. As long as the consumer group exists, any successive sink connectors that you create with the same `CONNECTOR_NAME` value will continue from the last committed offset\.

**Example : Specifying an offset storage topic to recreate a source connector with an updated configuration**  
Suppose you have a change data capture \(CDC\) connector and you want to modify the connector configuration without losing your place in the CDC stream\. You can't update the existing connector configuration, but you can delete the connector and create a new one with the same name\. To tell the new connector where to start reading in the CDC stream, you can specify the old connector's offset storage topic in your worker configuration\. The following steps demonstrate how to accomplish this task\.  

1. On your client machine, run the following command to find the name of your connector's offset storage topic\. Replace `<bootstrapBrokerString>` with your cluster's bootstrap broker string\. For instructions on getting your bootstrap broker string, see [Getting the bootstrap brokers for an Amazon MSK cluster](msk-get-bootstrap-brokers.md)\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-topics.sh --list --bootstrap-server <bootstrapBrokerString>
   ```

   The following output shows a list of all cluster topics, including any default internal connector topics\. In this example, the existing CDC connector uses the [default offset storage topic](#msk-connect-default-offset-storage-topic) created by MSK Connect\. This is why the offset storage topic is called `__amazon_msk_connect_offsets_my-mskc-connector_12345678-09e7-4abc-8be8-c657f7e4ff32-2`\.

   ```
   __consumer_offsets
   __amazon_msk_canary
   __amazon_msk_connect_configs_my-mskc-connector_12345678-09e7-4abc-8be8-c657f7e4ff32-2
   __amazon_msk_connect_offsets_my-mskc-connector_12345678-09e7-4abc-8be8-c657f7e4ff32-2
   __amazon_msk_connect_status_my-mskc-connector_12345678-09e7-4abc-8be8-c657f7e4ff32-2
   my-msk-topic-1
   my-msk-topic-2
   ```

1. Open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/](https://console.aws.amazon.com/msk)\.

1. Choose your connector from the **Connectors** list\. Copy and save the contents of the **Connector configuration** field so that you can modify it and use it to create the new connector\.

1. Choose **Delete** to delete the connector\. Then enter the connector name in the text input field to confirm deletion\.

1. Create a custom worker configuration with values that fit your scenario\. For instructions, see [Creating a custom worker configuration](#msk-connect-create-custom-worker-config)\.

   In your worker configuration, you must specify the name of the offset storage topic that you previously retrieved as the value for `offset.storage.topic` like in the following configuration\. 

   ```
   config.providers.secretManager.param.aws.region=us-east-1
   key.converter=<org.apache.kafka.connect.storage.StringConverter>
   value.converter=<org.apache.kafka.connect.storage.StringConverter>
   config.providers.secretManager.class=com.github.jcustenborder.kafka.config.aws.SecretsManagerConfigProvider
   config.providers=secretManager
   offset.storage.topic=__amazon_msk_connect_offsets_my-mskc-connector_12345678-09e7-4abc-8be8-c657f7e4ff32-2
   ```

1. 
**Important**  
You must give your new connector the same name as the old connector\.

   Create a new connector using the worker configuration that you set up in the previous step\. For instructions, see [Creating a connector](msk-connect-connectors.md#mkc-create-connector-intro)\.

### Considerations<a name="msk-connect-manage-connector-offsets-considerations"></a>

Consider the following when you manage source connector offsets\.
+ To specify an offset storage topic, provide the name of the Kafka topic where connector offsets are stored as the value for `offset.storage.topic` in your worker configuration\.
+ If you want to reuse the offset storage topic to consume offsets from a previously created connector, you must give the new connector the same name as the old connector\.
+ Use caution when you make changes to a connector configuration\. Changing configuration values may result in unintended connector behavior if a source connector uses values from the configuration to key offset records\. We recommend that you refer to your plugin's documentation for guidance\.