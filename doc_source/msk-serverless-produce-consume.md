# Step 5: Produce and consume data<a name="msk-serverless-produce-consume"></a>

In this step, you produce and consume data using the topic that you created in the previous step\.

**To produce and consume messages**

1. Run the following command to create a console producer\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-console-producer.sh --broker-list $BS --producer.config client.properties --topic msk-serverless-tutorial
   ```

1. Enter any message that you want, and press **Enter**\. Repeat this step two or three times\. Every time you enter a line and press **Enter**, that line is sent to your cluster as a separate message\.

1. Keep the connection to the client machine open, and then open a second, separate connection to that machine in a new window\.

1. Use your second connection to the client machine to create a console consumer with the following command\. Replace *my\-endpoint* with the bootstrap server string that you saved after you created the cluster\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-console-consumer.sh --bootstrap-server my-endpoint --consumer.config client.properties --topic msk-serverless-tutorial --from-beginning
   ```

   You start seeing the messages you entered earlier when you used the console producer command\.

1. Enter more messages in the producer window, and watch them appear in the consumer window\.

**Next Step**

[Step 6: Delete resources](delete-resources.md)