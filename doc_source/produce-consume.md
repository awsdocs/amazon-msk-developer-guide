# Step 4: Produce and consume data<a name="produce-consume"></a>

In this step of [Getting Started Using Amazon MSK](getting-started.md), you produce and consume data\.

**To produce and consume messages**

1. Go to the `bin` folder of the Apache Kafka installation on the client machine, and create a text file named `client.properties` with the following contents\.

   ```
   security.protocol=PLAINTEXT
   ```

1. Run the following command to start a console producer\. Replace *BootstrapServerString* with the plaintext connection string that you obtained in [Step 3: Create a topic](create-topic.md)\. For instructions on how to retrieve this connection string, see [Getting the bootstrap brokers for an Amazon MSK cluster](msk-get-bootstrap-brokers.md)\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-console-producer.sh --broker-list BootstrapServerString --producer.config client.properties --topic MSKTutorialTopic
   ```

1. Enter any message that you want, and press **Enter**\. Repeat this step two or three times\. Every time you enter a line and press **Enter**, that line is sent to your Apache Kafka cluster as a separate message\.

1. Keep the connection to the client machine open, and then open a second, separate connection to that machine in a new window\.

1. In the following command, replace *BootstrapServerString* with the plaintext connection string that you saved earlier\. Then, to create a console consumer, run the following command with your second connection to the client machine\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-console-consumer.sh --bootstrap-server BootstrapServerString --consumer.config client.properties --topic MSKTutorialTopic --from-beginning
   ```

   You start seeing the messages you entered earlier when you used the console producer command\.

1. Enter more messages in the producer window, and watch them appear in the consumer window\.

**Next Step**

[Step 5: Use Amazon CloudWatch to view Amazon MSK metrics](view-metrics.md)