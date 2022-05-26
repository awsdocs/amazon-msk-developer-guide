# Controlling Access to Apache ZooKeeper<a name="zookeeper-security"></a>

For security reasons you can limit access to the Apache ZooKeeper nodes that are part of your Amazon MSK cluster\. To limit access to the nodes, you can assign a separate security group to them\. You can then decide who gets access to that security group\.

**Topics**
+ [To place your Apache ZooKeeper nodes in a separate security group](#zookeeper-security-group)
+ [Using TLS security with Apache ZooKeeper](#zookeeper-security-tls)

## To place your Apache ZooKeeper nodes in a separate security group<a name="zookeeper-security-group"></a>

1. Get the Apache ZooKeeper connection string for your cluster\. To learn how, see [Getting the Apache ZooKeeper connection string for an Amazon MSK Cluster](msk-get-connection-string.md)\. The connection string contains the DNS names of your Apache ZooKeeper nodes\.

1. Use a tool like `host` or `ping` to convert the DNS names you obtained in the previous step to IP addresses\. Save these IP addresses because you need them later in this procedure\.

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left pane, under **NETWORK & SECURITY**, choose **Network Interfaces**\.

1. In the search field above the table of network interfaces, type the name of your cluster, then type return\. This limits the number of network interfaces that appear in the table to those interfaces that are associated with your cluster\.

1. Select the check box at the beginning of the row that corresponds to the first network interface in the list\.

1. In the details pane at the bottom of the page, look for the **Primary private IPv4 IP**\. If this IP address matches one of the IP addresses you obtained in the first step of this procedure, this means that this network interface is assigned to an Apache ZooKeeper node that is part of your cluster\. Otherwise, deselect the check box next to this network interface, and select the next network interface in the list\. The order in which you select the network interfaces doesn't matter\. In the next steps, you will perform the same operations on all network interfaces that are assigned to Apache ZooKeeper nodes, one by one\.

1. When you select a network interface that corresponds to an Apache ZooKeeper node, choose the **Actions** menu at the top of the page, then choose **Change Security Groups**\. Assign a new security group to this network interface\. For information about creating security groups, see [Creating a Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html?shortFooter=true#CreatingSecurityGroups) in the Amazon VPC documentation\.

1. Repeat the previous step to assign the same new security group to all the network interfaces that are associated with the Apache ZooKeeper nodes of your cluster\.

1. You can now choose who has access to this new security group\. For information about setting security group rules, see [Adding, Removing, and Updating Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html?shortFooter=true#AddRemoveRules) in the Amazon VPC documentation\.

## Using TLS security with Apache ZooKeeper<a name="zookeeper-security-tls"></a>

You can use TLS security for encryption in transit between your clients and your Apache ZooKeeper nodes\. To implement TLS security with your Apache ZooKeeper nodes, do the following:
+ Clusters must use Apache Kafka version 2\.5\.1 or later to use TLS security with Apache ZooKeeper\.
+ Enable TLS security when you create or configure your cluster\. Clusters created with Apache Kafka version 2\.5\.1 or later with TLS enabled automatically use TLS security with Apache ZooKeeper endpoints\. For information about setting up TLS security, see [How Do I Get Started with Encryption?](msk-working-with-encryption.md)\.
+ Retrieve the TLS Apache ZooKeeper endpoints using the [DescribeCluster ](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster) operation\.
+ Create an Apache ZooKeeper configuration file for use with the following CLI commands: Config, ACL, and ZooKeeper Shell\. You use the Apache Zookeeper config file with these commands using the `--zk-tls-config-file` parameter\.

  The following example shows a typical Apache ZooKeeper configuration file: 

  ```
  zookeeper.ssl.client.enable=true
  zookeeper.clientCnxnSocket=org.apache.zookeeper.ClientCnxnSocketNetty
  zookeeper.ssl.keystore.location=kafka.jks
  zookeeper.ssl.keystore.password=test1234
  zookeeper.ssl.truststore.location=truststore.jks
  zookeeper.ssl.truststore.password=test1234
  ```
+ For other commands \(such as `kafka-topics`\), you must use the `KAFKA_OPTS` environment variable to configure Apache ZooKeeper parameters\. The following example shows how to configure the `KAFKA_OPTS` environment variable to pass Apache ZooKeeper parameters into other commands:

  ```
  export KAFKA_OPTS="
  -Dzookeeper.clientCnxnSocket=org.apache.zookeeper.ClientCnxnSocketNetty 
  -Dzookeeper.client.secure=true 
  -Dzookeeper.ssl.trustStore.location=/home/ec2-user/kafka.client.truststore.jks
  -Dzookeeper.ssl.trustStore.password=changeit"
  ```

  Once you have configured the `KAFKA_OPTS` environment variable, you can use CLI commands normally\. The following example creates an Apache Kafka topic using the Apache ZooKeeper configuration from the `KAFKA_OPTS` environment variable:

  ```
  bin/kafka-topics.sh --create --zookeeper ZooKeeperTLSConnectString --replication-factor 3 --partitions 1 --topic AWSKafkaTutorialTopic
  ```

**Note**  
The names of the parameters you use in your Apache ZooKeeper configuration file and those you use in your `KAFKA_OPTS` environment variable are not consistent\. Pay attention to which names you use with which parameters in your configuration file and `KAFKA_OPTS` environment variable\.

For more information about accessing your Apache ZooKeeper nodes with TLS, see [ KIP\-515: Enable ZK client to use the new TLS supported authentication](https://cwiki.apache.org/confluence/display/KAFKA/KIP-515%3A+Enable+ZK+client+to+use+the+new+TLS+supported+authentication)\.