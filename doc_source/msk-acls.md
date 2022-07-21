# Apache Kafka ACLs<a name="msk-acls"></a>

Apache Kafka has a pluggable authorizer and ships with an out\-of\-box authorizer implementation that uses Apache ZooKeeper to store all ACLs\. Amazon MSK enables this authorizer in the `server.properties` file on the brokers\. For Apache Kafka version 2\.4\.1, the authorizer is AclAuthorizer\. For earlier versions of Apache Kafka, it is SimpleAclAuthorizer\.

Apache Kafka ACLs have the format "Principal P is \[Allowed/Denied\] Operation O From Host H on any Resource R matching ResourcePattern RP"\. If RP doesn't match a specific resource R, then R has no associated ACLs, and therefore no one other than super users is allowed to access R\. To change this Apache Kafka behavior, you set the property `allow.everyone.if.no.acl.found` to true\. Amazon MSK sets it to true by default\. This means that with Amazon MSK clusters, if you don't explicitly set ACLs on a resource, all principals can access this resource\. If you enable ACLs on a resource, only the authorized principals can access it\. If you want to restrict access to a topic and authorize a client using TLS mutual authentication, add ACLs using the Apache Kafka authorizer CLI\. For more information about adding, removing, and listing ACLs, see [Kafka Authorization Command Line Interface](https://cwiki.apache.org/confluence/display/KAFKA/Kafka+Authorization+Command+Line+Interface)\.

In addition to the client, you also need to grant all your brokers access to your topics so that the brokers can replicate messages from the primary partition\. If the brokers don't have access to a topic, replication for the topic fails\.

**To add or remove read and write access to a topic**

1. Add your brokers to the ACL table to allow them to read from all topics that have ACLs in place\. To grant your brokers read access to a topic, run the following command on a client machine that can communicate with the MSK cluster\. 

   Replace *ZooKeeper\-Connection\-String* with your Apache ZooKeeper connection string\. For information on how to get this string, see [Getting the Apache ZooKeeper connection string for an Amazon MSK cluster](msk-get-connection-string.md)\. 

   Replace *Distinguished\-Name* with the DNS of any of your cluster's bootstrap brokers, then replace the string before the first period in this distinguished name by an asterisk \(`*`\)\. For example, if one of your cluster's bootstrap brokers has the DNS `b-6.mytestcluster.67281x.c4.kafka.us-east-1.amazonaws.com`, replace *Distinguished\-Name* in the following command with `*.mytestcluster.67281x.c4.kafka.us-east-1.amazonaws.com`\. For information on how to get the bootstrap brokers, see [Getting the bootstrap brokers for an Amazon MSK cluster](msk-get-bootstrap-brokers.md)\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-acls.sh --authorizer-properties zookeeper.connect=ZooKeeper-Connection-String --add --allow-principal "User:CN=Distinguished-Name" --operation Read --group=* --topic Topic-Name
   ```

1. To grant read access to a topic, run the following command on your client machine\. If you use mutual TLS authentication, use the same *Distinguished\-Name* you used when you created the private key\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-acls.sh --authorizer-properties zookeeper.connect=ZooKeeper-Connection-String --add --allow-principal "User:CN=Distinguished-Name" --operation Read --group=* --topic Topic-Name
   ```

   To remove read access, you can run the same command, replacing `--add` with `--remove`\.

1. To grant write access to a topic, run the following command on your client machine\. If you use mutual TLS authentication, use the same *Distinguished\-Name* you used when you created the private key\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-acls.sh --authorizer-properties zookeeper.connect=ZooKeeper-Connection-String --add --allow-principal "User:CN=Distinguished-Name" --operation Write --topic Topic-Name
   ```

   To remove write access, you can run the same command, replacing `--add` with `--remove`\.