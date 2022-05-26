# Logging<a name="msk-logging"></a>

You can deliver Apache Kafka broker logs to one or more of the following destination types: Amazon CloudWatch Logs, Amazon S3, Amazon Kinesis Data Firehose\. You can also log Amazon MSK API calls with AWS CloudTrail\.

## Broker logs<a name="broker-logs"></a>

Broker logs enable you to troubleshoot your Apache Kafka applications and to analyze their communications with your MSK cluster\. You can configure your new or existing MSK cluster to deliver INFO\-level broker logs to one or more of the following types of destination resources: a CloudWatch log group, an S3 bucket, a Kinesis Data Firehose delivery stream\. Through Kinesis Data Firehose you can then deliver the log data from your delivery stream to OpenSearch Service\. You must create a destination resource before you configure your cluster to deliver broker logs to it\. Amazon MSK doesn't create these destination resources for you if they don't already exist\. For information about these three types of destination resources and how to create them, see the following documentation:
+ [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)
+ [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/Welcome.html)
+ [Amazon Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html)

**Note**  
Amazon MSK does not support delivering broker logs to Kinesis Data Firehose in the Asia Pacific \(Osaka\) Region\.

### Required permissions<a name="broker-logs-perms"></a>

To configure a destination for Amazon MSK broker logs, the IAM identity that you use for Amazon MSK actions must have the permissions described in the [AWS managed policy: AmazonMSKFullAccess](security-iam-awsmanpol.md#security-iam-awsmanpol-AmazonMSKFullAccess) policy\. 

To stream broker logs to an S3 bucket, you also need the `s3:PutBucketPolicy` permission\. For information about S3 bucket policies, see [How Do I Add an S3 Bucket Policy?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html) in the Amazon S3 Console User Guide\. For information about IAM policies in general, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the IAM User Guide\.

### Required KMS key policy for use with SSE\-KMS buckets<a name="sse-kms-buckets"></a>

If you enabled server\-side encryption for your S3 bucket using AWS KMS\-managed keys \(SSE\-KMS\) with a customer managed key, add the following to the key policy for your KMS key so that Amazon MSK can write broker files to the bucket\.

```
{
  "Sid": "Allow Amazon MSK to use the key.",
  "Effect": "Allow",
  "Principal": {
    "Service": [
      "delivery.logs.amazonaws.com"
    ]
  },
  "Action": [
    "kms:Encrypt",
    "kms:Decrypt",
    "kms:ReEncrypt*",
    "kms:GenerateDataKey*",
    "kms:DescribeKey"
  ],
  "Resource": "*"
}
```

### Configuring broker logs using the AWS Management Console<a name="broker-logs-console"></a>

If you are creating a new cluster, look for the **Broker log delivery** heading in the **Monitoring** section\. You can specify the destinations to which you want Amazon MSK to deliver your broker logs\. 

For an existing cluster, choose the cluster from your list of clusters, then choose the **Details** tab\. Scroll down to the **Monitoring** section and then choose its **Edit** button\. You can specify the destinations to which you want Amazon MSK to deliver your broker logs\.

### Configuring broker logs using the AWS CLI<a name="broker-logs-cli"></a>

When you use the `create-cluster` or the `update-monitoring` commands, you can optionally specify the `logging-info` parameter and pass to it a JSON structure like the following example\. In this JSON, all three destination types are optional\.

```
{
  "BrokerLogs": {
    "S3": {
      "Bucket": "ExampleBucketName",
      "Prefix": "ExamplePrefix",
      "Enabled": true
    },
    "Firehose": {
      "DeliveryStream": "ExampleDeliveryStreamName",
      "Enabled": true
    },
    "CloudWatchLogs": {
      "Enabled": true,
      "LogGroup": "ExampleLogGroupName"
    }
  }
}
```

### Configuring broker logs using the API<a name="broker-logs-api"></a>

You can specify the optional `loggingInfo` structure in the JSON that you pass to the [CreateCluster](https://docs.aws.amazon.com/msk/1.0/apireference/clusters.html#CreateCluster) or [UpdateMonitoring](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn-monitoring.html#UpdateMonitoring) operations\.

**Note**  
By default, when broker logging is enabled, Amazon MSK logs `INFO` level logs to the specified destinations\. However, users of Apache Kafka 2\.4\.X and later can dynamically set the broker log level to any of the [log4j log levels](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/Level.html)\. For information about dynamically setting the broker log level, see [ KIP\-412: Extend Admin API to support dynamic application log levels](https://cwiki.apache.org/confluence/display/KAFKA/KIP-412%3A+Extend+Admin+API+to+support+dynamic+application+log+levels)\. If you dynamically set the log level to `DEBUG` or `TRACE`, we recommend using Amazon S3 or Kinesis Data Firehose as the log destination\. If you use CloudWatch Logs as a log destination and you dynamically enable `DEBUG` or `TRACE` level logging, Amazon MSK may continuously deliver a sample of logs\. This can significantly impact broker performance and should only be used when the `INFO` log level is not verbose enough to determine the root cause of an issue\.

## Logging API calls with AWS CloudTrail<a name="logging-using-cloudtrail"></a>



**Note**  
AWS CloudTrail logs are available for Amazon MSK only when you use [IAM access control](iam-access-control.md)\.

Amazon MSK is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in Amazon MSK\. CloudTrail captures API calls for as events\. The calls captured include calls from the Amazon MSK console and code calls to the Amazon MSK API operations\. It also captures Apache Kafka actions such as creating and altering topics and groups\.

If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for Amazon MSK\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to Amazon MSK or the Apache Kafka action, the IP address from which the request was made, who made the request, when it was made, and additional details\. 

To learn more about CloudTrail, including how to configure and enable it, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

### Amazon MSK information in CloudTrail<a name="msk-info-in-cloudtrail"></a>

CloudTrail is enabled on your Amazon Web Services account when you create the account\. When supported event activity occurs in an MSK cluster, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your Amazon Web Services account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your Amazon Web Services account, including events for Amazon MSK, create a trail\. A *trail* enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all Regions\. The trail logs events from all Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other Amazon services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see the following: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

Amazon MSK logs all [Amazon MSK operations](https://docs.aws.amazon.com//msk/1.0/apireference/operations.html) as events in CloudTrail log files\. In addition, it logs the following Apache Kafka actions\.
+ kafka\-cluster:DescribeClusterDynamicConfiguration 
+ kafka\-cluster:AlterClusterDynamicConfiguration 
+ kafka\-cluster:CreateTopic 
+ kafka\-cluster:DescribeTopicDynamicConfiguration 
+ kafka\-cluster:AlterTopic 
+ kafka\-cluster:AlterTopicDynamicConfiguration 
+ kafka\-cluster:DeleteTopic

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or AWS Identity and Access Management \(IAM\) user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

### Example: Amazon MSK log file entries<a name="understanding-msk-entries"></a>

A trail is a configuration that enables delivery of events as log files to an Amazon S3 bucket that you specify\. CloudTrail log files contain one or more log entries\. An event represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files aren't an ordered stack trace of the public API calls and Apache Kafka actions, so they don't appear in any specific order\.

The following example shows CloudTrail log entries that demonstrate the `DescribeCluster` and `DeleteCluster` Amazon MSK actions\.

```
{
  "Records": [
    {
      "eventVersion": "1.05",
      "userIdentity": {
        "type": "IAMUser",
        "principalId": "ABCDEF0123456789ABCDE",
        "arn": "arn:aws:iam::012345678901:user/Joe",
        "accountId": "012345678901",
        "accessKeyId": "AIDACKCEVSQ6C2EXAMPLE",
        "userName": "Joe"
      },
      "eventTime": "2018-12-12T02:29:24Z",
      "eventSource": "kafka.amazonaws.com",
      "eventName": "DescribeCluster",
      "awsRegion": "us-east-1",
      "sourceIPAddress": "192.0.2.0",
      "userAgent": "aws-cli/1.14.67 Python/3.6.0 Windows/10 botocore/1.9.20",
      "requestParameters": {
        "clusterArn": "arn%3Aaws%3Akafka%3Aus-east-1%3A012345678901%3Acluster%2Fexamplecluster%2F01234567-abcd-0123-abcd-abcd0123efa-2"
      },
      "responseElements": null,
      "requestID": "bd83f636-fdb5-abcd-0123-157e2fbf2bde",
      "eventID": "60052aba-0123-4511-bcde-3e18dbd42aa4",
      "readOnly": true,
      "eventType": "AwsApiCall",
      "recipientAccountId": "012345678901"
    },
    {
      "eventVersion": "1.05",
      "userIdentity": {
        "type": "IAMUser",
        "principalId": "ABCDEF0123456789ABCDE",
        "arn": "arn:aws:iam::012345678901:user/Joe",
        "accountId": "012345678901",
        "accessKeyId": "AIDACKCEVSQ6C2EXAMPLE",
        "userName": "Joe"
      },
      "eventTime": "2018-12-12T02:29:40Z",
      "eventSource": "kafka.amazonaws.com",
      "eventName": "DeleteCluster",
      "awsRegion": "us-east-1",
      "sourceIPAddress": "192.0.2.0",
      "userAgent": "aws-cli/1.14.67 Python/3.6.0 Windows/10 botocore/1.9.20",
      "requestParameters": {
        "clusterArn": "arn%3Aaws%3Akafka%3Aus-east-1%3A012345678901%3Acluster%2Fexamplecluster%2F01234567-abcd-0123-abcd-abcd0123efa-2"
      },
      "responseElements": {
        "clusterArn": "arn:aws:kafka:us-east-1:012345678901:cluster/examplecluster/01234567-abcd-0123-abcd-abcd0123efa-2",
        "state": "DELETING"
      },
      "requestID": "c6bfb3f7-abcd-0123-afa5-293519897703",
      "eventID": "8a7f1fcf-0123-abcd-9bdb-1ebf0663a75c",
      "readOnly": false,
      "eventType": "AwsApiCall",
      "recipientAccountId": "012345678901"
    }
  ]
}
```

The following example shows a CloudTrail log entry that demonstrates the `kafka-cluster:CreateTopic` action\.

```
{
  "eventVersion": "1.08",
  "userIdentity": {
    "type": "IAMUser",
    "principalId": "ABCDEFGH1IJKLMN2P34Q5",
    "arn": "arn:aws:iam::111122223333:user/Admin",
    "accountId": "111122223333",
    "accessKeyId": "CDEFAB1C2UUUUU3AB4TT",
    "userName": "Admin"
  },
  "eventTime": "2021-03-01T12:51:19Z",
  "eventSource": "kafka-cluster.amazonaws.com",
  "eventName": "CreateTopic",
  "awsRegion": "us-east-1",
  "sourceIPAddress": "198.51.100.0/24",
  "userAgent": "aws-msk-iam-auth/unknown-version/aws-internal/3 aws-sdk-java/1.11.970 Linux/4.14.214-160.339.amzn2.x86_64 OpenJDK_64-Bit_Server_VM/25.272-b10 java/1.8.0_272 scala/2.12.8 vendor/Red_Hat,_Inc.",
  "requestParameters": {
    "kafkaAPI": "CreateTopics",
    "resourceARN": "arn:aws:kafka:us-east-1:111122223333:topic/IamAuthCluster/3ebafd8e-dae9-440d-85db-4ef52679674d-1/Topic9"
  },
  "responseElements": null,
  "requestID": "e7c5e49f-6aac-4c9a-a1d1-c2c46599f5e4",
  "eventID": "be1f93fd-4f14-4634-ab02-b5a79cb833d2",
  "readOnly": false,
  "eventType": "AwsApiCall",
  "managementEvent": true,
  "eventCategory": "Management",
  "recipientAccountId": "111122223333"
}
```