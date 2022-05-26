# Logging for MSK Connect<a name="msk-connect-logging"></a>

MSK Connect can write log events that you can use to debug your connector\. When you create a connector, you can specify zero or more of the following log destinations:
+ Amazon CloudWatch Logs: You specify the log group to which you want MSK Connect to send your connector's log events\. For information on how to create a log group, see [Create a log group](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#Create-Log-Group) in the *CloudWatch Logs User Guide*\.
+ Amazon S3: You specify the S3 bucket to which you want MSK Connect to send your connector's log events\. For information on how to create an S3 bucket, see [Creating a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html) in the *Amazon S3 User Guide*\.
+ Amazon Kinesis Data Firehose: You specify the delivery stream to which you want MSK Connect to send your connector's log events\. For information on how to create a delivery stream, see [Creating an Amazon Kinesis Data Firehose delivery stream](https://docs.aws.amazon.com/firehose/latest/dev/basic-create.html) in the *Kinesis Data Firehose User Guide*\.

To learn more about setting up logging, see [Enabling logging from certain AWS services](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AWS-logs-and-resource-policy.html) in the *Amazon CloudWatch Logs User Guide*\.

MSK Connect emits the following types of log events:


****  

| Level | Description | 
| --- | --- | 
| INFO | Runtime events of interest at startup and shutdown\. | 
| WARN | Runtime situations that aren't errors but are undesirable or unexpected\. | 
| FATAL | Severe errors that cause premature termination\. | 
| ERROR | Unexpected conditions and runtime errors that aren't fatal\. | 

The following is an example of a log event sent to CloudWatch Logs:

```
[Worker-0bb8afa0b01391c41] [2021-09-06 16:02:54,151] WARN [Producer clientId=producer-1] Connection to node 1 (b-1.my-test-cluster.twwhtj.c2.kafka.us-east-1.amazonaws.com/INTERNAL_IP) could not be established. Broker may not be available. (org.apache.kafka.clients.NetworkClient:782)
```

## Preventing secrets from appearing in connector logs<a name="msk-connect-logging-secrets"></a>

**Note**  
Sensitive configuration values can appear in connector logs if a plugin does not define those values as secret\. Kafka Connect treats undefined configuration values the same as any other plaintext value\.

If your plugin defines a property as secret, Kafka Connect redacts the property's value from connector logs\. For example, the following connector logs demonstrate that if a plugin defines `aws.secret.key` as a `PASSWORD` type, then its value is replaced with `[hidden]`\.

```
    2022-01-11T15:18:55.000+00:00    [Worker-05e6586a48b5f331b] [2022-01-11 15:18:55,150] INFO SecretsManagerConfigProviderConfig values:
    2022-01-11T15:18:55.000+00:00    [Worker-05e6586a48b5f331b] aws.access.key = my_access_key
    2022-01-11T15:18:55.000+00:00    [Worker-05e6586a48b5f331b] aws.region = us-east-1
    2022-01-11T15:18:55.000+00:00    [Worker-05e6586a48b5f331b] aws.secret.key = [hidden]
    2022-01-11T15:18:55.000+00:00    [Worker-05e6586a48b5f331b] secret.prefix =
    2022-01-11T15:18:55.000+00:00    [Worker-05e6586a48b5f331b] secret.ttl.ms = 300000
    2022-01-11T15:18:55.000+00:00    [Worker-05e6586a48b5f331b] (com.github.jcustenborder.kafka.config.aws.SecretsManagerConfigProviderConfig:361)
```

To prevent secrets from appearing in connector log files, a plugin developer must use the Kafka Connect enum constant [https://kafka.apache.org/27/javadoc/org/apache/kafka/common/config/ConfigDef.Type.html#PASSWORD](https://kafka.apache.org/27/javadoc/org/apache/kafka/common/config/ConfigDef.Type.html#PASSWORD) to define sensitive properties\. When a property is type `ConfigDef.Type.PASSWORD`, Kafka Connect excludes its value from connector logs even if the value is sent as plaintext\. 