# Username and password authentication with AWS Secrets Manager<a name="msk-password"></a>

You can control access to your Amazon MSK clusters using usernames and passwords that are stored and secured using AWS Secrets Manager\. Storing user credentials in Secrets Manager reduces the overhead of cluster authentication such as auditing, updating, and rotating credentials\. Secrets Manager also lets you share user credentials across clusters\.

**Topics**
+ [How it works](#msk-password-howitworks)
+ [Setting up SASL/SCRAM authentication for an Amazon MSK cluster](#msk-password-tutorial)
+ [Working with users](#msk-password-users)
+ [Limitations](#msk-password-limitations)

## How it works<a name="msk-password-howitworks"></a>

Username and password authentication for Amazon MSK uses SASL/SCRAM \(Simple Authentication and Security Layer/ Salted Challenge Response Mechanism\) authentication\. To set up username and password authentication for a cluster, you create a Secret resource in [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/?id=docs_gateway), and associate user names and passwords with that secret\. 

SASL/SCRAM is defined in [RFC 5802](https://tools.ietf.org/html/rfc5802)\. SCRAM uses secured hashing algorithms, and does not transmit plaintext passwords between client and server\. 

**Note**  
When you set up SASL/SCRAM authentication for your cluster, Amazon MSK turns on TLS encryption for all traffic between clients and brokers\.

## Setting up SASL/SCRAM authentication for an Amazon MSK cluster<a name="msk-password-tutorial"></a>

To set up a secret in AWS Secrets Manager, follow the [Creating and Retrieving a Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/tutorials_basic.html) tutorial in the [AWS Secrets Manager User Guide](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)\.

Note the following requirements when creating a secret for an Amazon MSK cluster:
+ Choose **Other type of secrets \(e\.g\. API key\)** for the secret type\.
+ Your secret name must begin with the prefix **AmazonMSK\_**\.
+ You must either use an existing custom AWS KMS key or create a new custom AWS KMS key for your secret\. Secrets Manager uses the default AWS KMS key for a secret by default\. 
**Important**  
A secret created with the default AWS KMS key cannot be used with an Amazon MSK cluster\.
+ Your user and password data must be in the following format to enter key\-value pairs using the **Plaintext** option\.

  ```
  {
    "username": "alice",
    "password": "alice-secret"
  }
  ```
+ Record the ARN \(Amazon Resource Name\) value for your secret\. 
+ 
**Important**  
You can't associate a Secrets Manager secret with a cluster that exceeds the limits described in [ Right\-size your cluster: Number of partitions per broker](bestpractices.md#partitions-per-broker)\.
+ If you use the AWS CLI to create the secret, specify a key ID or ARN for the `kms-key-id` parameter\. Don't specify an alias\.
+ To associate the secret with your cluster, use either the Amazon MSK console, or the [ BatchAssociateScramSecret](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn-scram-secrets.html#BatchAssociateScramSecret) operation\. 
**Important**  
When you associate a secret with a cluster, Amazon MSK attaches a resource policy to the secret that allows your cluster to access and read the secret values that you defined\. You should not modify this resource policy\. Doing so can prevent your cluster from accessing your secret\.

  The following example JSON input for the `BatchAssociateScramSecret` operation associates a secret with a cluster:

  ```
  {
    "clusterArn" : "arn:aws:kafka:us-west-2:0123456789019:cluster/SalesCluster/abcd1234-abcd-cafe-abab-9876543210ab-4",          
    "secretArnList": [
      "arn:aws:secretsmanager:us-west-2:0123456789019:secret:AmazonMSK_MyClusterSecret"
    ]
  }
  ```

### Connecting to your cluster with a username and password<a name="msk-password-tutorial-connect"></a>

After you create a secret and associate it with your cluster, you can connect your client to the cluster\. The following example steps demonstrate how to connect a client to a cluster that uses SASL/SCRAM authentication, and how to produce to and consume from an example topic\.

1. Retrieve your cluster details with the following command\. Replace *ClusterArn* with the Amazon Resource Name \(ARN\) of your cluster:

   ```
   aws kafka describe-cluster --cluster-arn "ClusterArn"
   ```

   From the JSON result of the command, save the value associated with the string named `ZookeeperConnectString`\.

1. To create an example topic, run the following command on your client machine\. Replace *ZookeeperConnectString* with the string you recorded in the previous step\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-topics.sh --create --zookeeper ZookeeperConnectString --replication-factor 3 --partitions 1 --topic ExampleTopicName
   ```

1. On your client machine, create a JAAS configuration file that contains the user credentials stored in your secret\. For example, for the user **alice**, create a file called `users_jaas.conf` with the following content\.

   ```
   KafkaClient {
      org.apache.kafka.common.security.scram.ScramLoginModule required
      username="alice"
      password="alice-secret";
   };
   ```

1. Use the following command to export your JAAS config file as a `KAFKA_OPTS` environment parameter\.

   ```
   export KAFKA_OPTS=-Djava.security.auth.login.config=<path-to-jaas-file>/users_jaas.conf
   ```

1. Create a file named `kafka.client.truststore.jks` in a `./tmp` directory\.

1. Use the following command to copy the JDK key store file from your JVM `cacerts` folder into the `kafka.client.truststore.jks` file that you created in the previous step\. Replace *JDKFolder* with the name of the JDK folder on your instance\. For example, your JDK folder might be named `java-1.8.0-openjdk-1.8.0.201.b09-0.amzn2.x86_64`\.

   ```
   cp /usr/lib/jvm/JDKFolder/jre/lib/security/cacerts /tmp/kafka.client.truststore.jks
   ```

1. In the `bin` directory of your Apache Kafka installation, create a client properties file called `client_sasl.properties` with the following contents\. This file defines the SASL mechanism and protocol\.

   ```
   security.protocol=SASL_SSL
   sasl.mechanism=SCRAM-SHA-512
   ssl.truststore.location=<path-to-keystore-file>/kafka.client.truststore.jks
   ```

1. Retrieve your bootstrap brokers string with the following command\. Replace *ClusterArn* with the Amazon Resource Name \(ARN\) of your cluster:

   ```
   aws kafka get-bootstrap-brokers --cluster-arn ClusterArn
   ```

   From the JSON result of the command, save the value associated with the string named `BootstrapBrokerStringSaslScram`\. 

1. To produce to the example topic that you created, run the following command on your client machine\. Replace *BootstrapBrokerStringSaslScram* with the value that you retrieved in the previous step\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-console-producer.sh --broker-list BootstrapBrokerStringSaslScram --topic ExampleTopicName --producer.config client_sasl.properties
   ```

1. To consume from the topic you created, run the following command on your client machine\. Replace *BootstrapBrokerStringSaslScram* with the value that you obtained previously\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-console-consumer.sh --bootstrap-server BootstrapBrokerStringSaslScram --topic ExampleTopicName --from-beginning --consumer.config client_sasl.properties
   ```

## Working with users<a name="msk-password-users"></a>

**Creating users:** You create users in your secret as key\-value pairs\. When you use the **Plaintext** option in the Secrets Manager console, you should specify username and password data in the following format\.

```
{
  "username": "alice",
  "password": "alice-secret"
}
```

**Revoking user access:** To revoke a user's credentials to access a cluster, we recommend that you first remove or enforce an ACL on the cluster, and then disassociate the secret\. This is because of the following:
+ Removing a user does not close existing connections\.
+ Changes to your secret take up to 10 minutes to propagate\.

For information about using an ACL with Amazon MSK, see [Apache Kafka ACLs](msk-acls.md)\.

We recommend that you restrict access to your zookeeper nodes to prevent users from modifying ACLs\. For more information, see [Controlling access to Apache ZooKeeper](zookeeper-security.md)\.

## Limitations<a name="msk-password-limitations"></a>

Note the following limitations when using SCRAM secrets:
+ Amazon MSK only supports SCRAM\-SHA\-512 authentication\.
+ An Amazon MSK cluster can have up to 1000 users\.
+ You must use an AWS KMS key with your Secret\. You cannot use a Secret that uses the default Secrets Manager encryption key with Amazon MSK\. For information about creating a KMS key, see [Creating symmetric encryption KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html#create-symmetric-cmk)\.
+ You can't use an asymmetric KMS key with Secrets Manager\.
+ You can associate up to 10 secrets with a cluster at a time using the [ BatchAssociateScramSecret](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn-scram-secrets.html#BatchAssociateScramSecret) operation\.
+ The name of secrets associated with an Amazon MSK cluster must have the prefix **AmazonMSK\_**\.
+ Secrets associated with an Amazon MSK cluster must be in the same Amazon Web Services account and AWS region as the cluster\.