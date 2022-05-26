# Step 4: Produce and Consume Data<a name="produce-consume"></a>

In this step of [Getting Started Using Amazon MSK](getting-started.md), you produce and consume data\.

**To produce and consume messages**

1. Go to the `bin` folder of the Apache Kafka installation on the client machine, and create a text file named `client.properties` with the following contents\.

   ```
   security.protocol=PLAINTEXT
   ```

1. Run the following command in the `bin` folder, replacing *BootstrapBrokerString* with the private endpoint string that you obtained in [Step 3: Create a Topic](create-topic.md)\.

   ```
   ./kafka-console-producer.sh --broker-list BootstrapBrokerString --producer.config client.properties --topic MSKTutorialTopic
   ```

1. Enter any message that you want, and press **Enter**\. Repeat this step two or three times\. Every time you enter a line and press **Enter**, that line is sent to your Apache Kafka cluster as a separate message\.

1. Keep the connection to the client machine open, and then open a second, separate connection to that machine in a new window\.

1. In the following command, replace *BootstrapBrokerStringTls* with the value that you saved earlier\. Then, go to the `bin` folder and run the command using your second connection to the client machine\.

   ```
   ./kafka-console-consumer.sh --bootstrap-server BootstrapBrokerStringTls --consumer.config client.properties --topic MSKTutorialTopic --from-beginning
   ```

   You start seeing the messages you entered earlier when you used the console producer command\. These messages are TLS encrypted in transit\.

1. Enter more messages in the producer window, and watch them appear in the consumer window\.

**Next Step**

[Step 5: Use Amazon CloudWatch to View Amazon MSK Metrics](view-metrics.md)