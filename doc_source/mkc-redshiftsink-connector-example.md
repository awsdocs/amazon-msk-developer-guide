# Amazon Redshift sink connector with configuration provider<a name="mkc-redshiftsink-connector-example"></a>

This example shows how to use the Confluent [Amazon Redshift Sink Connector](https://www.confluent.io/hub/confluentinc/kafka-connect-aws-redshift) plugin and the open\-source [AWS Secrets Manager Config Provider](https://www.confluent.io/hub/jcustenborder/kafka-config-provider-aws) plugin to create a connector for MSK Connect\. Using a configuration provider lets you externalize secrets such as database credentials\. To learn more about configuration providers, see [Using a configuration provider to externalize secrets](msk-connect-config-provider.md)\.

**Note**  
You can test and evaluate the Confluent Amazon Redshift Sink Connector for free for 30 days\. After the 30\-day evaluation period, you must have an appropriate Confluent license\. For more information, see [Confluent Platform Licenses](https://docs.confluent.io/platform/current/installation/license.html)\.

## Before you begin<a name="mkc-redshiftsink-connector-example-prereqs"></a>

**Note**  
Amazon MSK Connect does not currently support connections to AWS Secrets Manager using VPC endpoints for AWS PrivateLink\.

Your connector must be able to access the internet so that it can interact with services such as AWS Secrets Manager that are outside of your Amazon Virtual Private Cloud\. The steps in this section help you complete the following tasks to enable internet access\.
+ Set up a public subnet that hosts a NAT gateway and routes traffic to an internet gateway in your VPC\.
+ Create a default route that directs your private subnet traffic to your NAT gateway\.

For more information, see [Enabling internet access for Amazon MSK Connect](msk-connect-internet-access.md)\.

**Prerequisites**

Before you can enable internet access, you need the following items:
+ The ID of the Amazon Virtual Private Cloud \(VPC\) associated with your cluster\. For example, *vpc\-123456ab*\.
+ The IDs of the private subnets in your VPC\. For example, *subnet\-a1b2c3de*, *subnet\-f4g5h6ij*, etc\. You must configure your connector with private subnets\.

**To enable internet access for your connector**

1. Open the Amazon Virtual Private Cloud console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Create a public subnet for your NAT gateway with a descriptive name, and note the subnet ID\. For detailed instructions, see [Create a subnet in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#AddaSubnet)\.

1. Create an internet gateway so that your VPC can communicate with the internet, and note the gateway ID\. Attach the internet gateway to your VPC\. For instructions, see [Create and attach an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Attach_Gateway)\.

1. Provision a public NAT gateway so that hosts in your private subnets can reach your public subnet\. When you create the NAT gateway, select the public subnet that you created earlier\. For instructions, see [Create a NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-creating)\.

1. Configure your route tables\. You must have two route tables in total to complete this setup\. You should already have a main route table that was automatically created at the same time as your VPC\. In this step you create an additional route table for your public subnet\.

   1. Use the following settings to modify your VPC's main route table so that your private subnets route traffic to your NAT gateway\. For instructions, see [Work with route tables](https://docs.aws.amazon.com/vpc/latest/userguide/WorkWithRouteTables.html) in the *Amazon Virtual Private Cloud* *User Guide*\.  
**Private MSKC route table**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/msk/latest/developerguide/mkc-redshiftsink-connector-example.html)

   1. Follow the instructions in [Create a custom route table](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Routing) to create a route table for your public subnet\. When you create the table, enter a descriptive name in the **Name tag** field to help you identify which subnet the table is associated with\. For example, **Public MSKC**\.

   1. Configure your **Public MSKC** route table using the following settings\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/msk/latest/developerguide/mkc-redshiftsink-connector-example.html)

Now that you have enabled internet access for Amazon MSK Connect you are ready to create a connector\.

## Creating an Amazon Redshift sink connector<a name="mkc-redshiftsink-connector-example-steps"></a>

1. 

**Prepare a custom plugin**

   1. Download and extract the [Amazon Redshift Sink Connector](https://www.confluent.io/hub/confluentinc/kafka-connect-aws-redshift) from the Confluent site\.

   1. Download and extract the [AWS Secrets Manager Config Provider](https://www.confluent.io/hub/jcustenborder/kafka-config-provider-aws)\.

   1. Place the following archives into the same directory:
      + The `confluentinc-kafka-connect-aws-redshift-1.2.0` folder
      + The `jcusten-border-kafka-config-provider-aws-0.1.1` folder

   1. Compress the directory that you created in the previous step into a ZIP file and then upload the ZIP file to an S3 bucket\. For instructions, see [Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html) in the *Amazon S3 User Guide\.*

   1. Copy the following JSON and paste it in a file\. For example, `redshift-sink-custom-plugin.json`\. Replace *<example\-custom\-plugin\-name>* with the name that you want the plugin to have, *<arn\-of\-your\-s3\-bucket>* with the ARN of the S3 bucket where you uploaded the ZIP file, and `<file-key-of-ZIP-object>` with the file key of the ZIP that you uploaded to S3\.

      ```
      {
          "name": "<example-custom-plugin-name>",
          "contentType": "ZIP",
          "location": {
              "s3Location": {
                  "bucketArn": "<arn-of-your-s3-bucket>",
                  "fileKey": "<file-key-of-ZIP-object>"
              }
          }
      }
      ```

   1. Run the following AWS CLI command from the folder where you saved the JSON file to create a plugin\.

      ```
      aws kafkaconnect create-custom-plugin --cli-input-json file://<redshift-sink-custom-plugin.json>
      ```

      You should see output similar to the following example\.

      ```
      {
          "CustomPluginArn": "arn:aws:kafkaconnect:us-east-1:012345678901:custom-plugin/example-custom-plugin-name/abcd1234-a0b0-1234-c1-12345678abcd-1",
          "CustomPluginState": "CREATING",
          "Name": "example-custom-plugin-name",
          "Revision": 1
      }
      ```

   1. Run the following command to check the plugin state\. The state should change from `CREATING` to `ACTIVE`\. Replace the ARN placeholder with the ARN that you got in the output of the previous command\.

      ```
      aws kafkaconnect describe-custom-plugin --custom-plugin-arn "<arn-of-your-custom-plugin>"
      ```

1. 

**Configure AWS Secrets Manager permissions and create a secret for your Amazon Redshift credentials**

   1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secrets-manager/)\.

   1. Create a new secret to store your Amazon Redshift user name and password\. For instructions, see [Create a secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) in the *AWS Secrets Manager* User Guide\.

   1. Copy your secret's ARN\.

   1. Add the Secrets Manager permissions from the following example policy to your [Service execution role](msk-connect-service-execution-role.md)\. Replace *<arn:aws:secretsmanager:us\-east\-1:123456789000:secret:MyRedshiftSecret\-1234>* with the ARN of your secret\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "secretsmanager:GetResourcePolicy",
              "secretsmanager:GetSecretValue",
              "secretsmanager:DescribeSecret",
              "secretsmanager:ListSecretVersionIds"
            ],
            "Resource": [
              "<arn:aws:secretsmanager:us-east-1:123456789000:secret:MyRedshiftSecret-1234>"
            ]
          }
        ]
      }
      ```

      For instructions on how to add IAM permissions, see [Adding and removing IAM identity permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the *IAM User Guide*\.

1. 

**Create a custom worker configuration with information about your configuration provider**

   1. Copy the following worker configuration properties into a file, replacing the `<placeholder>` strings with values that correspond to your scenario\. To learn more about the configuration properties for the AWS Secrets Manager Config Provider, see [SecretsManagerConfigProvider](https://jcustenborder.github.io/kafka-connect-documentation/projects/kafka-config-provider-aws/configProviders/SecretsManagerConfigProvider.html) in the plugin's documentation\.

      ```
      key.converter=<org.apache.kafka.connect.storage.StringConverter>
      value.converter=<org.apache.kafka.connect.storage.StringConverter>
      config.providers.secretManager.class=com.github.jcustenborder.kafka.config.aws.SecretsManagerConfigProvider
      config.providers=secretManager
      config.providers.secretManager.param.aws.region=<us-east-1>
      ```

   1. Run the following AWS CLI command to create your custom worker configuration\. 

      Replace the following values:
      + *<my\-worker\-config\-name>* \- a descriptive name for your custom worker configuration
      + *<encoded\-properties\-file\-content\-string>* \- a base64\-encoded version of the plaintext properties that you copied in the previous step

      ```
      aws kafkaconnect create-worker-configuration --name <my-worker-config-name> --properties-file-content <encoded-properties-file-content-string>
      ```

1. 

**Create a connector**

   1. Use the following example JSON to define properties for your connector\. Replace the *<placeholders>* with values that correspond to your scenario and resources\.

      Note that the configuration uses variables like `${secretManager:MyRedshiftSecret-1234:user name}` instead of plaintext to specify Redshift credentials\. Replace `MyRedshiftSecret-1234` with the name of your secret and then include the name of the key that you want to retrieve\. You must also replace `<arn-of-config-provider-worker-configuration>` with the ARN of your custom worker configuration\.

      ```
      {
          "connectorName": "example-redshift-sink-connector",
          "kafkaConnectVersion": "2.7.1",
          "serviceExecutionRoleArn": "<arn-of-msk-connect-service-execution-role>",
          "plugins": [
              {
                  "customPlugin": {
                      "customPluginArn": "<arn-of-custom-plugin>",
                      "revision": 1
                  }
              }
          ],
          "capacity": {
              "provisionedCapacity": {
                  "workerCount": 2,
                  "mcuCount": 1
              }
          },
          "kafkaCluster": {
              "apacheKafkaCluster": {
                  "bootstrapServers": "<my-cluster-bootstrap-servers-string>",
                  "vpc": {
                      "subnets": [
                          "<subnet-abcd1234>",
                          "<subnet-cdef0123>",
                          "<subnet-abcd0101>"
                      ]
                  }
              }
          },
          "kafkaClusterClientAuthentication": {"authenticationType": "NONE"},
          "kafkaClusterEncryptionInTransit": {"encryptionType": "PLAINTEXT"},
          "logDelivery": {
              "workerLogDelivery": {
                  "cloudWatchLogs": {
                      "logGroup": "<example-log-group-name>",
                      "enabled": true
                  }
              }
          },
          "connectorConfiguration": {
              "confluent.topic.bootstrap.servers": "<bootstrap-servers-string>",
              "confluent.topic.replication.factor": "1",
              "connector.class": "io.confluent.connect.aws.redshift.RedshiftSinkConnector",
              "tasks.max": "2",
              "topics": "<example-topic>",
              "aws.redshift.domain": "my-example-redshift-cluster.abcdefghijkl.us-east-1-integ.redshift-dev.amazonaws.com",
              "aws.redshift.port": "5439",
              "aws.redshift.database": "dev",
              "aws.redshift.user": "${secretManager:<MyRedshiftSecret-1234>:<username>}",
              "aws.redshift.password": "${secretManager:<MyRedshiftSecret-1234>:<password>}",
              "pk.mode": "kafka",
              "auto.create": "true",
              "key.converter": "org.apache.kafka.connect.json.JsonConverter",
              "value.converter": "org.apache.kafka.connect.json.JsonConverter",
              "key.converter.schemas.enable": "true",
              "value.converter.schemas.enable": "true"
          },
          "workerConfiguration": {
              "workerConfigurationArn": "<arn-of-config-provider-worker-configuration>",
              "revision": 1
          }
      }
      ```

   1. Run the following command to create the connector\.

      ```
      aws kafkaconnect create-connector --cli-input-json file://redshift-sink-connector.json
      ```

      The following is an example of the output that you get when you run the command successfully\.

      ```
      {
          "ConnectorArn": "arn:aws:kafkaconnect:us-east-1:123450006789:connector/example-redshift-sink-connector/abc12345-abcd-4444-a8b9-123456f513ed-2", 
          "ConnectorState": "CREATING", 
          "ConnectorName": "example-redshift-sink-connector"
      }
      ```