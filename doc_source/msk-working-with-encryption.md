# How Do I Get Started with Encryption?<a name="msk-working-with-encryption"></a>

When creating an MSK cluster, you can specify encryption settings in JSON format\. The following is an example\.

```
{
   "EncryptionAtRest": {
       "DataVolumeKMSKeyId": "arn:aws:kms:us-east-1:123456789012:key/abcdabcd-1234-abcd-1234-abcd123e8e8e"
    },
   "EncryptionInTransit": {
        "InCluster": true,
        "ClientBroker": "TLS"
    }
}
```

For `DataVolumeKMSKeyId`, you can specify a [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) or the AWS managed key for MSK in your account \(`alias/aws/kafka`\)\. If you don't specify `EncryptionAtRest`, Amazon MSK still encrypts your data at rest under the AWS managed key\. To determine which key your cluster is using, send a `GET` request or invoke the `DescribeCluster` API operation\. 

For `EncryptionInTransit`, the default value of `InCluster` is true, but you can set it to false if you don't want Amazon MSK to encrypt your data as it passes between brokers\.

To specify the encryption mode for data in transit between clients and brokers, set `ClientBroker` to one of three values: `TLS`, `TLS_PLAINTEXT`, or `PLAINTEXT`\.

**To specify encryption settings when creating a cluster**

1. Save the contents of the previous example in a file and give the file any name that you want\. For example, call it `encryption-settings.json`\.

1. Run the `create-cluster` command and use the `encryption-info` option to point to the file where you saved your configuration JSON\. The following is an example\.

   ```
   aws kafka create-cluster --cluster-name "ExampleClusterName" --broker-node-group-info file://brokernodegroupinfo.json --encryption-info file://encryptioninfo.json --kafka-version "2.2.1" --number-of-broker-nodes 3
   ```

   The following is an example of a successful response after running this command\.

   ```
   {
       "ClusterArn": "arn:aws:kafka:us-east-1:123456789012:cluster/SecondTLSTest/abcdabcd-1234-abcd-1234-abcd123e8e8e",
       "ClusterName": "ExampleClusterName",
       "State": "CREATING"
   }
   ```

**To test TLS encryption**

1. Create a client machine following the guidance in [Step 2: Create a Client Machine](create-client-machine.md)\.

1. Install Apache Kafka on the client machine\.

1. Run the following command on a machine that has the AWS CLI installed, replacing *clusterARN* with the ARN of your cluster \(a cluster created with `ClientBroker` set to `TLS` like the example in the previous procedure\)\.

   ```
   aws kafka describe-cluster --cluster-arn clusterARN
   ```

   In the result, look for the value of `ZookeeperConnectString` and save it because you need it in the next step\.

1. Go to the `bin` folder of the Apache Kafka installation on the client machine\. To create a topic, run the following command, replacing *ZookeeperConnectString* with the value you obtained for `ZookeeperConnectString` in the previous step\.

   ```
   kafka-topics.sh --create --zookeeper ZookeeperConnectString --replication-factor 3 --partitions 1 --topic TLSTestTopic
   ```

1. In this example we use the JVM truststore to talk to the MSK cluster\. To do this, first create a folder named `/tmp` on the client machine\. Then, go to the `bin` folder of the Apache Kafka installation, and run the following command\. \(Your JVM path might be different\.\)

   ```
   cp /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.amzn2.x86_64/jre/lib/security/cacerts /tmp/kafka.client.truststore.jks
   ```

1. While still in the `bin` folder of the Apache Kafka installation on the client machine, create a text file named `client.properties` with the following contents\.

   ```
   security.protocol=SSL
   ssl.truststore.location=/tmp/kafka.client.truststore.jks
   ```

1. Run the following command on a machine that has the AWS CLI installed, replacing *clusterARN* with the ARN of your cluster\.

   ```
   aws kafka get-bootstrap-brokers --cluster-arn clusterARN
   ```

   A successful result looks like the following\. Save this result because you need it for the next step\.

   ```
   {
       "BootstrapBrokerStringTls": "a-1.example.g7oein.c2.kafka.us-east-1.amazonaws.com:0123,a-3.example.g7oein.c2.kafka.us-east-1.amazonaws.com:0123,a-2.example.g7oein.c2.kafka.us-east-1.amazonaws.com:0123"
   }
   ```

1. In the `bin` folder of the Apache Kafka installation on the client machine, run the following, replacing *BootstrapBrokerStringTls* with the value you obtained in the previous step\. Leave this producer command running\.

   ```
   kafka-console-producer.sh --broker-list BootstrapBrokerStringTls --producer.config client.properties --topic TLSTestTopic
   ```

1. Open a new command window on the same client machine, go to the `bin` folder of the Apache Kafka installation, and run the following command to create a consumer\.

   ```
   kafka-console-consumer.sh --bootstrap-server BootstrapBrokerStringTls --consumer.config client.properties --topic TLSTestTopic
   ```

1. In the producer window, type a text message followed by a return, and look for the same message in the consumer window\. Amazon MSK encrypted this message in transit\.



For more information about configuring Apache Kafka clients to work with encrypted data, see [Configuring Kafka Clients](https://kafka.apache.org/documentation/#security_configclients)\.