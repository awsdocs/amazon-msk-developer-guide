# Step 5: Produce and consume data<a name="msk-serverless-produce-consume"></a>

In this step, you produce and consume data using the topic that you created in the previous step\.

**To produce and consume messages**

1. Run the following command in the `bin` folder\. Replace *my\-endpoint* with the bootstrap\-server string you that you saved after you created the cluster\.

   ```
   ./kafka-console-producer.sh --broker-list my-endpoint --producer.config client.properties --topic msk-serverless-tutorial
   ```

1. Enter any message that you want, and press **Enter**\. Repeat this step two or three times\. Every time you enter a line and press **Enter**, that line is sent to your cluster as a separate message\.

1. Keep the connection to the client machine open, and then open a second, separate connection to that machine in a new window\.

1. Go to the `bin` folder and run the command using your second connection to the client machine\. Replace *my\-endpoint* with the bootstrap\-server string you that you saved after you created the cluster\.

   ```
   ./kafka-console-consumer.sh --bootstrap-server my-endpoint --consumer.config client.properties --topic msk-serverless-tutorial --from-beginning
   ```

   You start seeing the messages you entered earlier when you used the console producer command\.

1. Enter more messages in the producer window, and watch them appear in the consumer window\.

**Next Step**

[Step 6: Delete resources](delete-resources.md)