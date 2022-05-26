# Step 5: Send data<a name="mkc-send-data"></a>

In this step you send data to the Apache Kafka topic that you created earlier, and then look for that same data in the destination S3 bucket\.

**To send data to the MSK cluster**

1. In the `bin` folder of the Apache Kafka installation on the client instance, create a text file named `client.properties` with the following contents\.

   ```
   security.protocol=PLAINTEXT
   ```

1. Run the following command in the `bin` folder, replacing *BootstrapBrokerString* with the value that you obtained when you ran the previous command\.

   ```
   ./kafka-console-producer.sh --broker-list BootstrapBrokerString --producer.config client.properties --topic mkc-tutorial-topic
   ```

1. Enter any message that you want, and press **Enter**\. Repeat this step two or three times\. Every time you enter a line and press **Enter**, that line is sent to your Apache Kafka cluster as a separate message\.

1. Look in the destination Amazon S3 bucket to find the messages that you sent in the previous step\.