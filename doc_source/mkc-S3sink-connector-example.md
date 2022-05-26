# Amazon S3 sink connector<a name="mkc-S3sink-connector-example"></a>

This example shows how to use the Confluent [Amazon S3 sink connector](https://www.confluent.io/hub/confluentinc/kafka-connect-s3) and the AWS CLI to create an Amazon S3 sink connector in MSK Connect\.

1. Copy the following JSON and paste it in a new file\. Replace the placeholder strings with values that correspond to your Amazon MSK cluster's bootstrap servers connection string and the cluster's subnet and security group IDs\. For information about how to set up a service execution role, see [IAM roles and policies for MSK Connect](msk-connect-iam.md)\.

   ```
   {
       "connectorConfiguration": {
           "connector.class": "io.confluent.connect.s3.S3SinkConnector",
           "s3.region": "us-east-1",
           "format.class": "io.confluent.connect.s3.format.json.JsonFormat",
           "flush.size": "1",
           "schema.compatibility": "NONE",
           "topics": "my-test-topic",
           "tasks.max": "2",
           "partitioner.class": "io.confluent.connect.storage.partitioner.DefaultPartitioner",
           "storage.class": "io.confluent.connect.s3.storage.S3Storage",
           "s3.bucket.name": "my-test-bucket"
       },
       "connectorName": "example-S3-sink-connector",
       "kafkaCluster": {
           "apacheKafkaCluster": {
               "bootstrapServers": "<cluster-bootstrap-servers-string>",
               "vpc": {
                   "subnets": [
                       "<cluster-subnet-1>",
                       "<cluster-subnet-2>",
                       "<cluster-subnet-3>"
                   ],
                   "securityGroups": ["<cluster-security-group-id>"]
               }
           }
       },
       "capacity": {
           "provisionedCapacity": {
               "mcuCount": 2,
               "workerCount": 4
           }
       },
       "kafkaConnectVersion": "2.7.1",
       "serviceExecutionRoleArn": "<arn-of-a-role-that-msk-connect-can-assume>",
       "plugins": [
           {
               "customPlugin": {
                   "customPluginArn": "<arn-of-custom-plugin-that-contains-connector-code>",
                   "revision": 1
               }
           }
       ],
       "kafkaClusterEncryptionInTransit": {"encryptionType": "PLAINTEXT"},
       "kafkaClusterClientAuthentication": {"authenticationType": "NONE"}
   }
   ```

1. Run the following AWS CLI command in the folder where you saved the JSON file in the previous step\.

   ```
   aws kafkaconnect create-connector --cli-input-json file://connector-info.json
   ```

   The following is an example of the output that you get when you run the command successfully\.

   ```
   {
       "ConnectorArn": "arn:aws:kafkaconnect:us-east-1:123450006789:connector/example-S3-sink-connector/abc12345-abcd-4444-a8b9-123456f513ed-2", 
       "ConnectorState": "CREATING", 
       "ConnectorName": "example-S3-sink-connector"
   }
   ```