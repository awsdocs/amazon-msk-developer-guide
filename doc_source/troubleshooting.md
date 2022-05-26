# Troubleshooting Your Amazon MSK Cluster<a name="troubleshooting"></a>

The following information can help you troubleshoot problems that you might have with your Amazon MSK cluster\. You can also post your issue to the [Amazon MSK forum](https://forums.aws.amazon.com/forum.jspa?forumID=315)\.

**Topics**
+ [Consumer group stuck in `PreparingRebalance` state](#consumer-group-rebalance)
+ [Error delivering broker logs to Amazon CloudWatch Logs](#cw-broker-logs-error)
+ [No default security group](#troubleshooting-shared-vpc)
+ [Cluster appears stuck in the CREATING state](#troubleshooting-cluster-stuck)
+ [Cluster state goes from CREATING to FAILED](#troubleshooting-cluster-failed)
+ [Cluster state is ACTIVE but producers cannot send data or consumers cannot receive data](#troubleshooting-nodata)
+ [AWS CLI doesn't recognize Amazon MSK](#troubleshooting-nocli)
+ [Partitions go offline or replicas are out of sync](#troubleshooting-offlinepartitions-outofsyncreplicas)
+ [Disk space is running low](#troubleshooting-lowdiskspace)
+ [Memory running low](#troubleshooting-lowmemory)
+ [Producer Gets NotLeaderForPartitionException](#troubleshooting-NotLeaderForPartitionException)
+ [Under\-Replicated Partitions \(URP\) greater than zero](#troubleshooting-urp)
+ [Cluster has topics called \_\_amazon\_msk\_canary and \_\_amazon\_msk\_canary\_state](#amazon_msk_canary)
+ [Partition replication fails](#partition_replication_fails)
+ [Unable to access cluster that has public access turned on](#public-access-issues)
+ [Unable to access cluster from within AWS: networking issues](#networking-trouble)
+ [Failed authentication: Too many connects](#troubleshoot-too-many-connects)
+ [MSK Serverless: Cluster creation fails](#troubleshoot-serverless-create-cluster-failure)

## Consumer group stuck in `PreparingRebalance` state<a name="consumer-group-rebalance"></a>

If one or more of your consumer groups is stuck in a perpetual rebalancing state, the cause might be Apache Kafka issue [KAFKA\-9752](https://issues.apache.org/jira/browse/KAFKA-9752), which affects Apache Kafka versions 2\.3\.1 and 2\.4\.1\.

To resolve this issue, we recommend that you upgrade your cluster to [Amazon MSK bug\-fix version 2\.4\.1\.1](supported-kafka-versions.md#2.4.1.1), which contains a fix for this issue\. For information about updating an existing cluster to Amazon MSK bug\-fix version 2\.4\.1\.1, see [Updating the Apache Kafka version](version-upgrades.md)\.

 The workarounds for solving this issue without upgrading the cluster to Amazon MSK bug\-fix version 2\.4\.1\.1 are to either set the Kafka clients to use [Static Membership Protocol](#consumer-group-rebalance-static) , or to [Identify and Reboot](#consumer-group-rebalance-reboot) the coordinating broker node of the stuck consumer group\. 

### Implementing Static Membership Protocol<a name="consumer-group-rebalance-static"></a>

To implement Static Membership Protocol in your clients, do the following:

1. Set the `group.instance.id` property of your [ Kafka Consumers](https://kafka.apache.org/26/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html) configuration to a static string that identifies the consumer in the group\. 

1. Ensure that other instances of the configuration are updated to use the static string\.

1. Deploy the changes to your Kafka Consumers\.

Using Static Membership Protocol is more effective if the session timeout in the client configuration is set to a duration that allows the consumer to recover without prematurely triggering a consumer group rebalance\. For example, if your consumer application can tolerate 5 minutes of unavailability, a reasonable value for the session timeout would be 4 minutes instead of the default value of 10 seconds\.

**Note**  
Using Static Membership Protocol only reduces the probability of encountering this issue\. You may still encounter this issue even when using Static Membership Protocol\.

### Rebooting the Coordinating Broker Node<a name="consumer-group-rebalance-reboot"></a>

To reboot the coordinating broker node, do the following:

1. Identify the group coordinator using the `kafka-consumer-groups.sh` command\.

1. Restart the group coordinator of the stuck consumer group using the [ RebootBroker](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn-reboot-broker.html#RebootBroker) API action\.

## Error delivering broker logs to Amazon CloudWatch Logs<a name="cw-broker-logs-error"></a>

When you try to set up your cluster to send broker logs to Amazon CloudWatch Logs, you might get one of two exceptions\.

If you get an `InvalidInput.LengthOfCloudWatchResourcePolicyLimitExceeded` exception, try again but use log groups that start with `/aws/vendedlogs/`\. For more information, see [Enabling Logging from Certain Amazon Web Services](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AWS-logs-and-resource-policy.html)\.

If you get an `InvalidInput.NumberOfCloudWatchResourcePoliciesLimitExceeded` exception, choose an existing Amazon CloudWatch Logs policy in your account, and append the following JSON to it\.

```
{"Sid":"AWSLogDeliveryWrite","Effect":"Allow","Principal":{"Service":"delivery.logs.amazonaws.com"},"Action":["logs:CreateLogStream","logs:PutLogEvents"],"Resource":["*"]}
```

If you try to append the JSON above to an existing policy but get an error that says you've reached the maximum length for the policy you picked, try to append the JSON to another one of your Amazon CloudWatch Logs policies\. After you append the JSON to an existing policy, try once again to set up broker\-log delivery to Amazon CloudWatch Logs\.

## No default security group<a name="troubleshooting-shared-vpc"></a>

If you try to create a cluster and get an error indicating that there's no default security group, it might be because you are using a VPC that was shared with you\. Ask your administrator to grant you permission to describe the security groups on this VPC and try again\. For an example of a policy that allows this action, see [Amazon EC2: Allows Managing EC2 Security Groups Associated With a Specific VPC, Programmatically and in the Console ](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_securitygroups-vpc.html)\.

## Cluster appears stuck in the CREATING state<a name="troubleshooting-cluster-stuck"></a>

Sometimes cluster creation can take up to 30 minutes\. Wait for 30 minutes and check the state of the cluster again\.

## Cluster state goes from CREATING to FAILED<a name="troubleshooting-cluster-failed"></a>

Try creating the cluster again\.

## Cluster state is ACTIVE but producers cannot send data or consumers cannot receive data<a name="troubleshooting-nodata"></a>
+ If the cluster creation succeeds \(the cluster state is `ACTIVE`\), but you can't send or receive data, ensure that your producer and consumer applications have access to the cluster\. For more information, see the guidance in [Step 2: Create a Client Machine](create-client-machine.md)\.
+ If your producers and consumers have access to the cluster but still experience problems producing and consuming data, the cause might be [KAFKA\-7697](https://issues.apache.org/jira/browse/KAFKA-7697), which affects Apache Kafka version 2\.1\.0 and can lead to a deadlock in one or more brokers\. Consider migrating to Apache Kafka 2\.2\.1, which is not affected by this bug\. For information about how to migrate, see [Migrating Clusters Using Apache Kafka's MirrorMaker](migration.md)\.

## AWS CLI doesn't recognize Amazon MSK<a name="troubleshooting-nocli"></a>

If you have the AWS CLI installed, but it doesn't recognize the Amazon MSK commands, upgrade your AWS CLI to the latest version\. For detailed instructions on how to upgrade the AWS CLI, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)\. For information about how to use the AWS CLI to run Amazon MSK commands, see [Amazon MSK: How It Works](operations.md)\.

## Partitions go offline or replicas are out of sync<a name="troubleshooting-offlinepartitions-outofsyncreplicas"></a>

These can be symptoms of low disk space\. See [Disk space is running low](#troubleshooting-lowdiskspace)\.

## Disk space is running low<a name="troubleshooting-lowdiskspace"></a>

See the following best practices for managing disk space: [Monitor disk space](bestpractices.md#bestpractices-monitor-disk-space) and [Adjust data retention parameters](bestpractices.md#bestpractices-retention-period)\.

## Memory running low<a name="troubleshooting-lowmemory"></a>

If you see the `MemoryUsed` metric running high or `MemoryFree` running low, that doesn't mean there's a problem\. Apache Kafka is designed to use as much memory as possible, and it manages it optimally\.

## Producer Gets NotLeaderForPartitionException<a name="troubleshooting-NotLeaderForPartitionException"></a>

This is often a transient error\. Set the producer's `retries` configuration parameter to a value that's higher than its current value\.

## Under\-Replicated Partitions \(URP\) greater than zero<a name="troubleshooting-urp"></a>

The `UnderReplicatedPartitions` metric is an important one to monitor\. In a healthy MSK cluster, this metric has the value 0\. If it's greater than zero, that might be for one of the following reasons\.
+ If `UnderReplicatedPartitions` is spiky, the issue might be that the cluster isn't provisioned at the right size to handle incoming and outgoing traffic\. See [Best Practices](bestpractices.md)\.
+ If `UnderReplicatedPartitions` is consistently greater than 0 including during low\-traffic periods, the issue might be that you've set restrictive ACLs that don’t grant topic access to brokers\. To replicate partitions, brokers must be authorized to both READ and DESCRIBE topics\. DESCRIBE is granted by default with the READ authorization\. For information about setting ACLs, see [Authorization and ACLs](https://kafka.apache.org/documentation/#security_authz) in the Apache Kafka documentation\.

## Cluster has topics called \_\_amazon\_msk\_canary and \_\_amazon\_msk\_canary\_state<a name="amazon_msk_canary"></a>

You might see that your MSK cluster has a topic with the name `__amazon_msk_canary` and another with the name `__amazon_msk_canary_state`\. These are internal topics that Amazon MSK creates and uses for cluster health and diagnostic metrics\. These topics are negligible in size and can't be deleted\.

## Partition replication fails<a name="partition_replication_fails"></a>

Ensure that you haven't set ACLs on CLUSTER\_ACTIONS\.

## Unable to access cluster that has public access turned on<a name="public-access-issues"></a>

If your cluster has public access turned on, but you still cannot access it from the internet, follow these steps:

1. Ensure that the cluster's security group's inbound rules allow your IP address and the cluster's port\. For a list of cluster port numbers, see [Port information](port-info.md)\. Also ensure that the security group's outbound rules allow outbound communications\. For more information about security groups and their inbound and outbound rules, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the Amazon VPC User Guide\.

1. Make sure that your IP address and the cluster's port are allowed in the inbound rules of the cluster's VPC network ACL\. Unlike security groups, network ACLs are stateless\. This means that you must configure both inbound and outbound rules\. In the outbound rules, allow all traffic \(port range: 0–65535\) to your IP address\. For more information, see [Add and delete rules](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#Rules) in the Amazon VPC User Guide\. 

1. Make sure that you are using the public\-access bootstrap\-brokers string to access the cluster\. An MSK cluster that has public access turned on has two different bootstrap\-brokers strings, one for public access, and one for access from within AWS\. For more information, see [Getting the bootstrap brokers using the AWS Management Console](msk-get-bootstrap-brokers.md#get-bootstrap-console)\.

## Unable to access cluster from within AWS: networking issues<a name="networking-trouble"></a>

If you have an Apache Kafka application that is unable to communicate successfully with an MSK cluster, start by performing the following connectivity test\.

1. Use any of the methods described in [Getting the bootstrap brokers for an Amazon MSK Cluster](msk-get-bootstrap-brokers.md) to get the addresses of the bootstrap brokers\.

1. In the following command replace *bootstrap\-broker* with one of the broker addresses that you obtained in the previous step\. Replace *port\-number* with 9094 if the cluster is set up to use TLS authentication\. If the cluster doesn't use TLS authentication, replace *port\-number* with 9092\. Run the command from the client machine\.

   ```
   telnet bootstrap-broker port-number
   ```

1. Repeat the previous command for all the bootstrap brokers\.

1. Use any of the methods described in [Getting the Apache ZooKeeper connection string for an Amazon MSK Cluster](msk-get-connection-string.md) to get the addresses of the cluster's Apache ZooKeeper nodes\.

1. On the client machine run the following command, replacing *Apache\-ZooKeeper\-node* with the address of one of the Apache ZooKeeper nodes that you obtained in the previous step\. The number 2181 is the port number\. Repeat for all the Apache ZooKeeper nodes\.

   ```
   telnet Apache-ZooKeeper-node 2181
   ```

If the client machine is able to access the brokers and the Apache ZooKeeper nodes, this means there are no connectivity issues\. In this case, run the following command to check whether your Apache Kafka client is set up correctly\. To get *bootstrap\-brokers*, use any of the methods described in [Getting the bootstrap brokers for an Amazon MSK Cluster](msk-get-bootstrap-brokers.md)\. Replace *topic* with the name of your topic\.

```
bin/kafka-console-producer.sh --broker-list bootstrap-brokers --producer.config client.properties —topic topic
```

If the previous command succeeds, this means that your client is set up correctly\. If you're still unable to produce and consume from an application, debug the problem at the application level\.

If the client machine is unable to access the brokers and the Apache ZooKeeper nodes, see the following subsections for guidance that is based on your client\-machine setup\. 

### Amazon EC2 client and MSK cluster in the same VPC<a name="troubleshoot-ec2-client-in-cluster-vpc"></a>

If the client machine is in the same VPC as the MSK cluster, make sure the cluster's security group has an inbound rule that accepts traffic from the client machine's security group\. For information about setting up these rules, see [Security Group Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules)\. For an example of how to access a cluster from an Amazon EC2 instance that's in the same VPC as the cluster, see [Getting Started Using Amazon MSK](getting-started.md)\.

### Amazon EC2 client and MSK cluster in different VPCs<a name="troubleshoot-peering-connection"></a>

If the client machine and the cluster are in two different VPCs, ensure the following: 
+ The two VPCs are peered\.
+ The status of the peering connection is active\.
+ The route tables of the two VPCs are set up correctly\.

For information about VPC peering, see [Working with VPC Peering Connections](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html)\.

### On\-premises client<a name="troubleshoot-on-prem-client"></a>

In the case of an on\-premises client that is set up to connect to the MSK cluster using AWS VPN, ensure the following:
+ The VPN connection status is `UP`\. For information about how to check the VPN connection status, see [How do I check the current status of my VPN tunnel?](https://aws.amazon.com/premiumsupport/knowledge-center/check-vpn-tunnel-status/)\.
+ The route table of the cluster's VPC contains the route for an on\-premises CIDR whose target has the format `Virtual private gateway(vgw-xxxxxxxx)`\.
+ The MSK cluster's security group allows traffic on port 2181, port 9092 \(if your cluster accepts plaintext traffic\), and port 9094 \(if your cluster accepts TLS\-encrypted traffic\)\.

For more AWS VPN troubleshooting guidance, see [Troubleshooting Client VPN](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/troubleshooting.html)\.

### AWS Direct Connect<a name="troubleshoot-direct-connect"></a>

If the client uses AWS Direct Connect, see [Troubleshooting AWS Direct Connect](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Troubleshooting.html)\.

If the previous troubleshooting guidance doesn't resolve the issue, ensure that no firewall is blocking network traffic\. For further debugging, use tools like `tcpdump` and `Wireshark` to analyze traffic and to make sure that it is reaching the MSK cluster\.

## Failed authentication: Too many connects<a name="troubleshoot-too-many-connects"></a>

The `Failed authentication ... Too many connects` error indicates that a broker is protecting itself because one or more IAM clients are trying to connect to it at an aggressive rate\. To help brokers accept a higher rate of new IAM connections, you can increase the [https://kafka.apache.org/documentation/#producerconfigs_reconnect.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_reconnect.backoff.ms) configuration parameter\.

To learn more about the rate limits for new connections per broker, see the [Amazon MSK Quota](limits.md) page\.

## MSK Serverless: Cluster creation fails<a name="troubleshoot-serverless-create-cluster-failure"></a>

If you try to create an MSK Serverless cluster and the workflow fails, you may not have permission to create a VPC endpoint\. Verify that your administrator has granted you permission to create a VPC endpoint by allowing the `ec2:CreateVpcEndpoint` action\. 

For a complete list of permissions required to perform all Amazon MSK actions, see [AWS managed policy: AmazonMSKFullAccess](security-iam-awsmanpol.md#security-iam-awsmanpol-AmazonMSKFullAccess)\.