# Step 4: Create an Apache Kafka topic<a name="msk-serverless-create-topic"></a>

In this step, you use the previously created client machine to create a topic on the serverless cluster\.

**To create a topic and write data to it**

1. In the following `export` command, replace *my\-endpoint* with the bootstrap\-server string you that you saved after you created the cluster\. Then, go to the `kafka_2.12-2.8.1/bin` directory on the client machine, and run the `export` command\.

   ```
   export BS=my-endpoint
   ```

1. Run the following command to create a topic named `msk-serverless-tutorial` on your serverless cluster\. Replace *my\-endpoint* with the bootstrap\-server string you that you saved after you created the cluster\.

   ```
   ./kafka-topics.sh --bootstrap-server my-endpoint --command-config client.properties --create --topic msk-serverless-tutorial --partitions 6
   ```

**Next Step**

[Step 5: Produce and consume data](msk-serverless-produce-consume.md)