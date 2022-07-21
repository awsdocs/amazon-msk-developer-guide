# Amazon MSK configuration operations<a name="msk-configuration-operations"></a>

This topic describes how to create custom MSK configurations and how to perform operations on them\. For information about how to use MSK configurations to create or update clusters, see [Amazon MSK: How it works](operations.md)\.

**Topics**
+ [To create an MSK configuration](#msk-configuration-operations-create)
+ [To update an MSK configuration](#msk-configuration-operations-update)
+ [To delete an MSK configuration](#msk-configuration-operations-delete)
+ [To describe an MSK configuration](#msk-configuration-operations-describe)
+ [To describe an MSK configuration revision](#msk-configuration-operations-describe)
+ [To list all MSK configurations in your account for the current Region](#msk-configuration-operations-list)

## To create an MSK configuration<a name="msk-configuration-operations-create"></a>

1. Create a file where you specify the configuration properties that you want to set and the values that you want to assign to them\. The following are the contents of an example configuration file\.

   ```
   auto.create.topics.enable = true
   
   zookeeper.connection.timeout.ms = 1000
   
   log.roll.ms = 604800000
   ```

1. Run the following AWS CLI command, replacing *config\-file\-path* with the path to the file where you saved your configuration in the previous step\.
**Note**  
The name that you choose for your configuration must match the following regex: "^\[0\-9A\-Za\-z\]\[0\-9A\-Za\-z\-\]\{0,\}$"\.

   ```
   aws kafka create-configuration --name "ExampleConfigurationName" --description "Example configuration description." --kafka-versions "1.1.1" --server-properties fileb://config-file-path
   ```

   The following is an example of a successful response after running this command\.

   ```
   {
       "Arn": "arn:aws:kafka:us-east-1:123456789012:configuration/SomeTest/abcdabcd-1234-abcd-1234-abcd123e8e8e-1",
       "CreationTime": "2019-05-21T19:37:40.626Z",
       "LatestRevision": {
           "CreationTime": "2019-05-21T19:37:40.626Z",
           "Description": "Example configuration description.",
           "Revision": 1
       },
       "Name": "ExampleConfigurationName"
   }
   ```

1. The previous command returns an Amazon Resource Name \(ARN\) for the newly created configuration\. Save this ARN because you need it to refer to this configuration in other commands\. If you lose your configuration ARN, you can find it again by listing all the configurations in your account\.

## To update an MSK configuration<a name="msk-configuration-operations-update"></a>

1. Create a file where you specify the configuration properties that you want to update and the values that you want to assign to them\. The following are the contents of an example configuration file\.

   ```
   auto.create.topics.enable = true
   
   zookeeper.connection.timeout.ms = 1000
   
   min.insync.replicas = 2
   ```

1. Run the following AWS CLI command, replacing *config\-file\-path* with the path to the file where you saved your configuration in the previous step\.

   Replace *configuration\-arn* with the ARN you obtained when you created the configuration\. If you didn't save the ARN when you created the configuration, you can use the `list-configurations` command to list all configuration in your account, and find the configuration that you want in the list that appears in the response\. The ARN of the configuration also appears in that list\.

   ```
   aws kafka update-configuration --arn configuration-arn --description "Example configuration revision description." --server-properties fileb://config-file-path
   ```

1. The following is an example of a successful response after running this command\.

   ```
   {
       "Arn": "arn:aws:kafka:us-east-1:123456789012:configuration/SomeTest/abcdabcd-1234-abcd-1234-abcd123e8e8e-1",
       "LatestRevision": {
           "CreationTime": "2020-08-27T19:37:40.626Z",
           "Description": "Example configuration revision description.",
           "Revision": 2
       }
   }
   ```

## To delete an MSK configuration<a name="msk-configuration-operations-delete"></a>

The following procedure shows how to delete a configuration that isn't attached to a cluster\. You can't delete a configuration that's attached to a cluster\.

1. To run this example, replace *configuration\-arn* with the ARN you obtained when you created the configuration\. If you didn't save the ARN when you created the configuration, you can use the `list-configurations` command to list all configuration in your account, and find the configuration that you want in the list that appears in the response\. The ARN of the configuration also appears in that list\.

   ```
   aws kafka delete-configuration --arn configuration-arn
   ```

1. The following is an example of a successful response after running this command\.

   ```
   {
       "arn": " arn:aws:kafka:us-east-1:123456789012:configuration/SomeTest/abcdabcd-1234-abcd-1234-abcd123e8e8e-1",
       "state": "DELETING"
   }
   ```

## To describe an MSK configuration<a name="msk-configuration-operations-describe"></a>

1. This command returns metadata about the configuration\. To get a detailed description of the configuration, run the `describe-configuration-revision`\.

   To run this example, replace *configuration\-arn* with the ARN you obtained when you created the configuration\. If you didn't save the ARN when you created the configuration, you can use the `list-configurations` command to list all configuration in your account, and find the configuration that you want in the list that appears in the response\. The ARN of the configuration also appears in that list\.

   ```
   aws kafka describe-configuration --arn configuration-arn
   ```

1. The following is an example of a successful response after running this command\.

   ```
   {
       "Arn": "arn:aws:kafka:us-east-1:123456789012:configuration/SomeTest/abcdabcd-abcd-1234-abcd-abcd123e8e8e-1",
       "CreationTime": "2019-05-21T00:54:23.591Z",
       "Description": "Example configuration description.",
       "KafkaVersions": [
           "1.1.1"
       ],
       "LatestRevision": {
           "CreationTime": "2019-05-21T00:54:23.591Z",
           "Description": "Example configuration description.",
           "Revision": 1
       },
       "Name": "SomeTest"
   }
   ```

## To describe an MSK configuration revision<a name="msk-configuration-operations-describe"></a>

Describing an MSK configuration using the `describe-configuration` command, gives you the metadata of the configuration\. To see a description of the configuration, use this command, `describe-configuration-revision`, instead\.
+ Run the following command, replacing *configuration\-arn* with the ARN you obtained when you created the configuration\. If you didn't save the ARN when you created the configuration, you can use the `list-configurations` command to list all configuration in your account, and find the configuration that you want in the list that appears in the response\. The ARN of the configuration also appears in that list\.

  ```
  aws kafka describe-configuration-revision --arn configuration-arn --revision 1
  ```

  The following is an example of a successful response after running this command\.

  ```
  {
      "Arn": "arn:aws:kafka:us-east-1:123456789012:configuration/SomeTest/abcdabcd-abcd-1234-abcd-abcd123e8e8e-1",
      "CreationTime": "2019-05-21T00:54:23.591Z",
      "Description": "Example configuration description.",
      "Revision": 1,
      "ServerProperties": "YXV0by5jcmVhdGUudG9waWNzLmVuYWJsZSA9IHRydWUKCgp6b29rZWVwZXIuY29ubmVjdGlvbi50aW1lb3V0Lm1zID0gMTAwMAoKCmxvZy5yb2xsLm1zID0gNjA0ODAwMDAw"
  }
  ```

  The value of `ServerProperties` is encoded using base64\. If you use a base64 decoder \(for example, https://www\.base64decode\.org/\) to manually decode it, you get the contents of the original configuration file that you used to create the custom configuration\. In this case, you get the following:

  ```
  auto.create.topics.enable = true
  
  zookeeper.connection.timeout.ms = 1000
  
  log.roll.ms = 604800000
  ```

## To list all MSK configurations in your account for the current Region<a name="msk-configuration-operations-list"></a>
+ Run the following command\.

  ```
  aws kafka list-configurations
  ```

  The following is an example of a successful response after running this command\.

  ```
  {
      "Configurations": [
          {
              "Arn": "arn:aws:kafka:us-east-1:123456789012:configuration/SomeTest/abcdabcd-abcd-1234-abcd-abcd123e8e8e-1",
              "CreationTime": "2019-05-21T00:54:23.591Z",
              "Description": "Example configuration description.",
              "KafkaVersions": [
                  "1.1.1"
              ],
              "LatestRevision": {
                  "CreationTime": "2019-05-21T00:54:23.591Z",
                  "Description": "Example configuration description.",
                  "Revision": 1
              },
              "Name": "SomeTest"
          },
          {
              "Arn": "arn:aws:kafka:us-east-1:123456789012:configuration/SomeTest/abcdabcd-1234-abcd-1234-abcd123e8e8e-1",
              "CreationTime": "2019-05-03T23:08:29.446Z",
              "Description": "Example configuration description.",
              "KafkaVersions": [
                  "1.1.1"
              ],
              "LatestRevision": {
                  "CreationTime": "2019-05-03T23:08:29.446Z",
                  "Description": "Example configuration description.",
                  "Revision": 1
              },
              "Name": "ExampleConfigurationName"
          }
      ]
  }
  ```