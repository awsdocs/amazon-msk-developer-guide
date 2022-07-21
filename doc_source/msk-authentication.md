# Mutual TLS authentication<a name="msk-authentication"></a>

You can enable client authentication with TLS for connections from your applications to your Amazon MSK brokers and ZooKeeper nodes\. To use client authentication, you need an ACM Private CA\. The ACM Private CA can be either in the same AWS account as your cluster, or in a different account\. For information about private CAs, see [Creating and Managing a Private CA](https://docs.aws.amazon.com/acm-pca/latest/userguide/create-CA.html)\.

**Note**  
TLS authentication is not currently available in the Beijing and Ningxia Regions\.

Amazon MSK doesn't support certificate revocation lists \(CRLs\)\. To control access to your cluster topics or block compromised certificates, use Apache Kafka ACLs and AWS security groups\. For information about using Apache Kafka ACLs, see [Apache Kafka ACLs](msk-acls.md)\.

**Topics**
+ [To create a cluster that supports client authentication](#msk-authentication-cluster)
+ [To set up a client to use authentication](#msk-authentication-client)
+ [To produce and consume messages using authentication](#msk-authentication-messages)

## To create a cluster that supports client authentication<a name="msk-authentication-cluster"></a>

This procedure shows you how to enable client authentication using a CA that is hosted by ACM\.
**Note**  
We highly recommend using independent ACM PCAs for each MSK cluster when you use mutual TLS to control access\. Doing so will ensure that TLS certificates signed by PCAs only authenticate with a single MSK cluster\.

1. Create a file named `clientauthinfo.json` with the following contents\. Replace *Private\-CA\-ARN* with the ARN of your PCA\.

   ```
   {
      "Tls": {
          "CertificateAuthorityArnList": ["Private-CA-ARN"]
       }
   }
   ```

1. Create a file named `brokernodegroupinfo.json` as described in [Creating a cluster using the AWS CLI](msk-create-cluster.md#create-cluster-cli)\.

1. Client authentication requires that you also enable encryption in transit between clients and brokers\. Create a file named `encryptioninfo.json` with the following contents\. Replace *KMS\-Key\-ARN* with the ARN of your KMS key\. You can set `ClientBroker` to `TLS` or `TLS_PLAINTEXT`\.

   ```
   {
      "EncryptionAtRest": {
          "DataVolumeKMSKeyId": "KMS-Key-ARN"
       },
      "EncryptionInTransit": {
           "InCluster": true,
           "ClientBroker": "TLS"
       }
   }
   ```

   For more information about encryption, see [Amazon MSK encryption](msk-encryption.md)\.

1. On a machine where you have the AWS CLI installed, run the following command to create a cluster with authentication and in\-transit encryption enabled\. Save the cluster ARN provided in the response\.

   ```
   aws kafka create-cluster --cluster-name "AuthenticationTest" --broker-node-group-info file://brokernodegroupinfo.json --encryption-info file://encryptioninfo.json --client-authentication file://clientauthinfo.json --kafka-version "2.2.1" --number-of-broker-nodes 3
   ```

## To set up a client to use authentication<a name="msk-authentication-client"></a>

1. Create an Amazon EC2 instance to use as a client machine\. For simplicity, create this instance in the same VPC you used for the cluster\. See [Step 2: Create a client machine](create-client-machine.md) for an example of how to create such a client machine\.

1. Create a topic\. For an example, see the instructions under [Step 3: Create a topic](create-topic.md)\.

1. On a machine where you have the AWS CLI installed, run the following command to get the bootstrap brokers of the cluster\. Replace *Cluster\-ARN* with the ARN of your cluster\.

   ```
   aws kafka get-bootstrap-brokers --cluster-arn Cluster-ARN
   ```

   Save the string associated with `BootstrapBrokerStringTls` in the response\.

1. On your client machine, run the following command to use the JVM trust store to create your client trust store\. If your JVM path is different, adjust the command accordingly\. 

   ```
   cp /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.amzn2.x86_64/jre/lib/security/cacerts kafka.client.truststore.jks
   ```

1. On your client machine, run the following command to create a private key for your client\. Replace *Distinguished\-Name*, *Example\-Alias*, *Your\-Store\-Pass*, and *Your\-Key\-Pass* with strings of your choice\.

   ```
   keytool -genkey -keystore kafka.client.keystore.jks -validity 300 -storepass Your-Store-Pass -keypass Your-Key-Pass -dname "CN=Distinguished-Name" -alias Example-Alias -storetype pkcs12
   ```

1. On your client machine, run the following command to create a certificate request with the private key you created in the previous step\.

   ```
   keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias Example-Alias -storepass Your-Store-Pass -keypass Your-Key-Pass
   ```

1. Open the `client-cert-sign-request` file and ensure that it starts with `-----BEGIN CERTIFICATE REQUEST-----` and ends with `-----END CERTIFICATE REQUEST-----`\. If it starts with `-----BEGIN NEW CERTIFICATE REQUEST-----`, delete the word `NEW` \(and the single space that follows it\) from the beginning and the end of the file\.

1. On a machine where you have the AWS CLI installed, run the following command to sign your certificate request\. Replace *Private\-CA\-ARN* with the ARN of your PCA\. You can change the validity value if you want\. Here we use 300 as an example\.

   ```
   aws acm-pca issue-certificate --certificate-authority-arn Private-CA-ARN --csr fileb://client-cert-sign-request --signing-algorithm "SHA256WITHRSA" --validity Value=300,Type="DAYS"
   ```

   Save the certificate ARN provided in the response\.
**Note**  
To retrieve your client certificate, use the `acm-pca get-certificate` command and specify your certificate ARN\. For more information, see [get\-certificate](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/acm-pca/get-certificate.html) in the *AWS CLI Command Reference*\.  
 

1. Run the following command to get the certificate that ACM signed for you\. Replace *Certificate\-ARN* with the ARN you obtained from the response to the previous command\.

   ```
   aws acm-pca get-certificate --certificate-authority-arn Private-CA-ARN --certificate-arn Certificate-ARN
   ```

1. From the JSON result of running the previous command, copy the strings associated with `Certificate` and `CertificateChain`\. Paste these two strings in a new file named signed\-certificate\-from\-acm\. Paste the string associated with `Certificate` first, followed by the string associated with `CertificateChain`\. Replace the `\n` characters with new lines\. The following is the structure of the file after you paste the certificate and certificate chain in it\. 

   ```
   -----BEGIN CERTIFICATE-----
   ...
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   ...
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   ...
   -----END CERTIFICATE-----
   ```

1. Run the following command on the client machine to add this certificate to your keystore so you can present it when you talk to the MSK brokers\.

   ```
   keytool -keystore kafka.client.keystore.jks -import -file signed-certificate-from-acm -alias Example-Alias -storepass Your-Store-Pass -keypass Your-Key-Pass
   ```

1. Create a file named `client.properties` with the following contents\. Adjust the truststore and keystore locations to the paths where you saved `kafka.client.truststore.jks`\.

   ```
   security.protocol=SSL
   ssl.truststore.location=/tmp/kafka_2.12-2.2.1/kafka.client.truststore.jks
   ssl.keystore.location=/tmp/kafka_2.12-2.2.1/kafka.client.keystore.jks
   ssl.keystore.password=Your-Store-Pass
   ssl.key.password=Your-Key-Pass
   ```

## To produce and consume messages using authentication<a name="msk-authentication-messages"></a>

1. Run the following command to create a topic\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-topics.sh --create --zookeeper ZooKeeper-Connection-String --replication-factor 3 --partitions 1 --topic ExampleTopic
   ```

1. Run the following command to start a console producer\. The file named `client.properties` is the one you created in the previous procedure\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-console-producer.sh --broker-list BootstrapBroker-String --topic ExampleTopic --producer.config client.properties
   ```

1. In a new command window on your client machine, run the following command to start a console consumer\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-console-consumer.sh --bootstrap-server BootstrapBroker-String --topic ExampleTopic --consumer.config client.properties
   ```

1. Type messages in the producer window and watch them appear in the consumer window\.