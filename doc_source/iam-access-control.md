# IAM access control<a name="iam-access-control"></a>

IAM access control for Amazon MSK enables you to handle both authentication and authorization for your MSK cluster\. This eliminates the need to use one mechanism for authentication and another for authorization\. For example, when a client tries to write to your cluster, Amazon MSK uses IAM to check whether that client is an authenticated identity and also whether it is authorized to produce to your cluster\.

Amazon MSK logs access events so you can audit them\. For more information, see [Logging API calls with AWS CloudTrail](msk-logging.md#logging-using-cloudtrail)\.

To make IAM access control possible, Amazon MSK makes minor modifications to Apache Kafka source code\. These modifications won't cause a noticeable difference in your Apache Kafka experience\.

**Important**  
IAM access control doesn't apply to Apache ZooKeeper nodes\. For information about how you can control access to those nodes, see [Controlling Access to Apache ZooKeeper](zookeeper-security.md)\.

**Important**  
The `allow.everyone.if.no.acl.found` Apache Kafka setting has no effect if your cluster uses IAM access control\. 

**Important**  
You can invoke Apache Kafka ACL APIs for an MSK cluster that uses IAM access control\. However, Apache Kafka ACLs stored in Apache ZooKeeper have no effect on authorization for IAM roles\. You must use IAM policies to control access for IAM roles\.

## How IAM access control for Amazon MSK works<a name="how-to-use-iam-access-control"></a>

To use IAM access control for Amazon MSK, perform the following steps, which are described in detail in the rest of this section\.
+ [Create a cluster that uses IAM access control](#create-iam-access-control-cluster-in-console) 
+ [Configure clients for IAM access control](#configure-clients-for-iam-access-control)
+ [Create authorization policies](#create-iam-access-control-policies)
+ [Get the bootstrap brokers for IAM access control](#get-bootstrap-brokers-for-iam)

### Create a cluster that uses IAM access control<a name="create-iam-access-control-cluster-in-console"></a>

This section explains how you can use the AWS Management Console, the API, or the AWS CLI to create a cluster that uses IAM access control\. For information about how to turn on IAM access control for an existing cluster, see [Updating a cluster's security settings](msk-update-security.md)\.

**Use the AWS Management Console to create a cluster that uses IAM access control**

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose **Create cluster**\.

1. Choose **Create cluster with custom settings**\.

1. In the **Authentication** section, choose **IAM access control**\.

1. Complete the rest of the workflow for creating a cluster\.

**Use the API or the AWS CLI to create a cluster that uses IAM access control**
+ To create a cluster with IAM access control enabled, use the [CreateCluster](https://docs.aws.amazon.com/msk/1.0/apireference/clusters.html#CreateCluster) API or the [create\-cluster](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kafka/create-cluster.html) CLI command, and pass the following JSON for the `ClientAuthentication` parameter: `"ClientAuthentication": { "Sasl": { "Iam": { "Enabled": true } }`\. 

### Configure clients for IAM access control<a name="configure-clients-for-iam-access-control"></a>

To enable clients to communicate with an MSK cluster that uses IAM access control, configure them as described in the following steps\.

1. Add the following to the `client.properties` file\. Replace *<PATH\_TO\_TRUST\_STORE\_FILE>* with the fully\-qualified path to the trust store file on the client\.
**Note**  
If you don't want to use a specific certificate, you can remove `ssl.truststore.location=<PATH_TO_TRUST_STORE_FILE>` from your `client.properties` file\. When you don't specify a value for `ssl.truststore.location`, the Java process uses the default certificate\.

   ```
   ssl.truststore.location=<PATH_TO_TRUST_STORE_FILE>
   security.protocol=SASL_SSL
   sasl.mechanism=AWS_MSK_IAM
   sasl.jaas.config=software.amazon.msk.auth.iam.IAMLoginModule required;
   sasl.client.callback.handler.class=software.amazon.msk.auth.iam.IAMClientCallbackHandler
   ```

   To use a named profile that you created for AWS credentials, include `awsProfileName="your profile name";` in your client configuration file\. For information about named profiles, see [Named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html) in the AWS CLI documentation\.

1. Download the latest stable [aws\-msk\-iam\-auth](https://github.com/aws/aws-msk-iam-auth/releases) JAR file, and place it in the class path\. If you use Maven, add the following dependency, adjusting the version number as needed:

   ```
   <dependency>
       <groupId>software.amazon.msk</groupId>
       <artifactId>aws-msk-iam-auth</artifactId>
       <version>1.0.0</version>
   </dependency>
   ```

The Amazon MSK client plugin is open\-sourced under the Apache 2\.0 license\.

### Create authorization policies<a name="create-iam-access-control-policies"></a>

Attach an authorization policy to the IAM role that corresponds to the client\. In an authorization policy, you specify which actions to allow or deny for the role\. If your client is on an Amazon EC2 instance, associate the authorization policy with the IAM role for that Amazon EC2 instance\. Alternatively, you can configure your client to use a named profile, and then you associate the authorization policy with the role for that named profile\. [Configure clients for IAM access control](#configure-clients-for-iam-access-control) describes how to configure a client to use a named profile\.

For information about how to create an IAM policy, see [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html)\. 

The following is an example authorization policy for a cluster named MyTestCluster\. To understand the semantics of the `Action` and `Resource` elements, see [Semantics of actions and resources](#kafka-actions)\.

**Important**  
Changes that you make to an IAM policy are reflected in the IAM APIs and the AWS CLI immediately\. However, it can take noticeable time for the policy change to take effect\. In most cases, policy changes take effect in less than a minute\. Network conditions may sometimes increase the delay\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kafka-cluster:Connect",
                "kafka-cluster:AlterCluster",
                "kafka-cluster:DescribeCluster"
            ],
            "Resource": [
                "arn:aws:kafka:us-east-1:0123456789012:cluster/MyTestCluster/abcd1234-0123-abcd-5678-1234abcd-1"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "kafka-cluster:*Topic*",
                "kafka-cluster:WriteData",
                "kafka-cluster:ReadData"
            ],
            "Resource": [
                "arn:aws:kafka:us-east-1:0123456789012:topic/MyTestCluster/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "kafka-cluster:AlterGroup",
                "kafka-cluster:DescribeGroup"
            ],
            "Resource": [
                "arn:aws:kafka:us-east-1:0123456789012:group/MyTestCluster/*"
            ]
        }
    ]
}
```

To learn how to create a policy with action elements that correspond to common Apache Kafka use cases, like producing and consuming data, see [Common use cases](#iam-access-control-use-cases)\.

### Get the bootstrap brokers for IAM access control<a name="get-bootstrap-brokers-for-iam"></a>

See [Getting the bootstrap brokers for an Amazon MSK Cluster](msk-get-bootstrap-brokers.md)\.

## Semantics of actions and resources<a name="kafka-actions"></a>

This section explains the semantics of the action and resource elements that you can use in an IAM authorization policy\. For an example policy, see [Create authorization policies](#create-iam-access-control-policies)\.

### Actions<a name="actions"></a>

The following table lists the actions that you can include in an authorization policy when you use IAM access control for Amazon MSK\. When you include in your authorization policy an action from the *Action* column of the table, you must also include the corresponding actions from the *Required actions* column\. 


| Action | Description | Required actions | Required resources | Applicable to serverless clusters | 
| --- | --- | --- | --- | --- | 
| kafka\-cluster:Connect | Grants permission to connect and authenticate to the cluster\. | None | cluster | Yes | 
| kafka\-cluster:DescribeCluster | Grants permission to describe various aspects of the cluster, equivalent to Apache Kafka's DESCRIBE CLUSTER ACL\. |  `kafka-cluster:Connect`  | cluster | Yes | 
| kafka\-cluster:AlterCluster | Grants permission to alter various aspects of the cluster, equivalent to Apache Kafka's ALTER CLUSTER ACL\. |  `kafka-cluster:Connect` `kafka-cluster:DescribeCluster`  | cluster | No | 
| kafka\-cluster:DescribeClusterDynamicConfiguration | Grants permission to describe the dynamic configuration of a cluster, equivalent to Apache Kafka's DESCRIBE\_CONFIGS CLUSTER ACL\. |  `kafka-cluster:Connect`  | cluster | No | 
| kafka\-cluster:AlterClusterDynamicConfiguration | Grants permission to alter the dynamic configuration of a cluster, equivalent to Apache Kafka's ALTER\_CONFIGS CLUSTER ACL\. |  `kafka-cluster:Connect` `kafka-cluster:DescribeClusterDynamicConfiguration`  | cluster | No | 
| kafka\-cluster:WriteDataIdempotently | Grants permission to write data idempotently on a cluster, equivalent to Apache Kafka's IDEMPOTENT\_WRITE CLUSTER ACL\. |  `kafka-cluster:Connect` `kafka-cluster:WriteData`  | cluster | Yes | 
| kafka\-cluster:CreateTopic | Grants permission to create topics on a cluster, equivalent to Apache Kafka's CREATE CLUSTER/TOPIC ACL\. |  `kafka-cluster:Connect`  | topic | Yes | 
| kafka\-cluster:DescribeTopic | Grants permission to describe topics on a cluster, equivalent to Apache Kafka's DESCRIBE TOPIC ACL\. |  `kafka-cluster:Connect`  | topic | Yes | 
| kafka\-cluster:AlterTopic | Grants permission to alter topics on a cluster, equivalent to Apache Kafka's ALTER TOPIC ACL\. |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopic`  | topic | Yes | 
| kafka\-cluster:DeleteTopic | Grants permission to delete topics on a cluster, equivalent to Apache Kafka's DELETE TOPIC ACL\. |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopic`  | topic | Yes | 
| kafka\-cluster:DescribeTopicDynamicConfiguration | Grants permission to describe the dynamic configuration of topics on a cluster, equivalent to Apache Kafka's DESCRIBE\_CONFIGS TOPIC ACL\. |  `kafka-cluster:Connect`  | topic | Yes | 
| kafka\-cluster:AlterTopicDynamicConfiguration | Grants permission to alter the dynamic configuration of topics on a cluster, equivalent to Apache Kafka's ALTER\_CONFIGS TOPIC ACL\. |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopicDynamicConfiguration`  | topic | Yes | 
| kafka\-cluster:ReadData | Grants permission to read data from topics on a cluster, equivalent to Apache Kafka's READ TOPIC ACL\. |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopic` `kafka-cluster:AlterGroup`  | topic | Yes | 
| kafka\-cluster:WriteData | Grants permission to write data to topics on a cluster, equivalent to Apache Kafka's WRITE TOPIC ACL |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopic`  | topic | Yes | 
| kafka\-cluster:DescribeGroup | Grants permission to describe groups on a cluster, equivalent to Apache Kafka's DESCRIBE GROUP ACL\. |  `kafka-cluster:Connect`  | group | Yes | 
| kafka\-cluster:AlterGroup | Grants permission to join groups on a cluster, equivalent to Apache Kafka's READ GROUP ACL\. |  `kafka-cluster:Connect` `kafka-cluster:DescribeGroup`  | group | Yes | 
| kafka\-cluster:DeleteGroup | Grants permission to delete groups on a cluster, equivalent to Apache Kafka's DELETE GROUP ACL\. |  `kafka-cluster:Connect` `kafka-cluster:DescribeGroup`  | group | Yes | 
| kafka\-cluster:DescribeTransactionalId | Grants permission to describe transactional IDs on a cluster, equivalent to Apache Kafka's DESCRIBE TRANSACTIONAL\_ID ACL\. |  `kafka-cluster:Connect`  | transactional\-id | Yes | 
| kafka\-cluster:AlterTransactionalId | Grants permission to alter transactional IDs on a cluster, equivalent to Apache Kafka's WRITE TRANSACTIONAL\_ID ACL\. |  `kafka-cluster:Connect` `kafka-cluster:DescribeTransactionalId` `kafka-cluster:WriteData`  | transactional\-id | Yes | 

You can use the asterisk \(\*\) wildcard any number of times in an action after the colon\. The following are examples\.
+ `kafka-cluster:*Topic` stands for `kafka-cluster:CreateTopic`, `kafka-cluster:DescribeTopic`, `kafka-cluster:AlterTopic`, and `kafka-cluster:DeleteTopic`\. It doesn't include `kafka-cluster:DescribeTopicDynamicConfiguration` or `kafka-cluster:AlterTopicDynamicConfiguration`\.
+ `kafka-cluster:*` stands for all permissions\.

### Resources<a name="msk-iam-resources"></a>

The following table shows the four types of resources that you can use in an authorization policy when you use IAM access control for Amazon MSK\. You can get the cluster Amazon Resource Name \(ARN\) from the AWS Management Console or by using the [DescribeCluster](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster) API or the [describe\-cluster](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kafka/describe-cluster.html) AWS CLI command\. You can then use the cluster ARN to construct topic, group, and transaction ID ARNs\. To specify a resource in an authorization policy, use that resource's ARN\.


| Resource | ARN format | 
| --- | --- | 
| Cluster | arn:aws:kafka:region:account\-id:cluster/cluster\-name/cluster\-uuid | 
| Topic | arn:aws:kafka:region:account\-id:topic/cluster\-name/cluster\-uuid/topic\-name | 
| Group | arn:aws:kafka:region:account\-id:group/cluster\-name/cluster\-uuid/group\-name | 
| Transaction ID | arn:aws:kafka:region:account\-id:transactional\-id/cluster\-name/cluster\-uuid/transactional\-id | 

You can use the asterisk \(\*\) wildcard any number of times anywhere in the part of the ARN that comes after `:cluster/`, `:topic/`, `:group/`, and `:transaction-id/`\. The following are some examples of how you can use the asterisk \(\*\) wildcard to refer to multiple resources:
+ `arn:aws:kafka:us-east-1:0123456789012:topic/MyTestCluster/*`: all the topics in any cluster named MyTestCluster, regardless of the cluster's UUID\.
+ `arn:aws:kafka:us-east-1:0123456789012:topic/MyTestCluster/abcd1234-0123-abcd-5678-1234abcd-1/*_test`: all topics whose name ends with "\_test" in the cluster whose name is MyTestCluster and whose UUID is abcd1234\-0123\-abcd\-5678\-1234abcd\-1\.
+ `arn:aws:kafka:us-east-1:0123456789012:transactional-id/MyTestCluster/*/5555abcd-1111-abcd-1234-abcd1234-1`: all transactions whose transactional ID is 5555abcd\-1111\-abcd\-1234\-abcd1234\-1, across all incarnations of a cluster named MyTestCluster in your account\. This means that if you create a cluster named MyTestCluster, then delete it, and then create another cluster by the same name, you can use this resource ARN to represent the same transactions ID on both clusters\. However, the deleted cluster isn't accessible\.

## Common use cases<a name="iam-access-control-use-cases"></a>

The first column in the following table shows some common use cases\. To authorize a client to carry out a given use case, include the required actions for that use case in the client's authorization policy, and set `Effect` to `Allow`\.

For information about all the actions that are part of IAM access control for Amazon MSK, see [Semantics of actions and resources](#kafka-actions)\.

**Note**  
Actions are denied by default\. You must explicitly allow every action that you want to authorize the client to perform\.


****  

| Use case | Required actions | 
| --- | --- | 
| Admin |  `kafka-cluster:*`  | 
| Create a topic |  `kafka-cluster:Connect` `kafka-cluster:CreateTopic`  | 
| Produce data |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopic` `kafka-cluster:WriteData`  | 
| Consume data |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopic` `kafka-cluster:DescribeGroup` `kafka-cluster:AlterGroup` `kafka-cluster:ReadData`  | 
| Produce data idempotently |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopic` `kafka-cluster:WriteData` `kafka-cluster:WriteDataIdempotently`  | 
| Produce data transactionally |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopic` `kafka-cluster:WriteData` `kafka-cluster:DescribeTransactionalId` `kafka-cluster:AlterTransactionalId`  | 
| Describe the configuration of a cluster |  `kafka-cluster:Connect` `kafka-cluster:DescribeClusterDynamicConfiguration`  | 
| Update the configuration of a cluster |  `kafka-cluster:Connect` `kafka-cluster:DescribeClusterDynamicConfiguration` `kafka-cluster:AlterClusterDynamicConfiguration`  | 
| Describe the configuration of a topic |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopicDynamicConfiguration` | 
| Update the configuration of a topic |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopicDynamicConfiguration` `kafka-cluster:AlterTopicDynamicConfiguration`  | 
| Alter a topic |  `kafka-cluster:Connect` `kafka-cluster:DescribeTopic` `kafka-cluster:AlterTopic`  | 