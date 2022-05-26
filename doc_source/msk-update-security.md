# Updating a cluster's security settings<a name="msk-update-security"></a>

Use this Amazon MSK operation to update the authentication and client\-broker encryption settings of your MSK cluster\. You can also update the Private Security Authority used to sign certificates for mutual TLS authentication\. You can't change the in\-cluster \(broker\-to\-broker\) encryption setting\.

The cluster must be in the `ACTIVE` state for you to update its security settings\. 

If you turn on authentication using IAM, SASL, or TLS, you must also turn on encryption between clients and brokers\. The following table shows the possible combinations\.


****  

| Authentication | Client\-Broker Encryption Options | Broker\-Broker Encryption | 
| --- | --- | --- | 
| Unauthenticated | TLS, PLAINTEXT, TLS\_PLAINTEXT | Can be on or off\. | 
| mTLS | TLS, TLS\_PLAINTEXT | Must be on\. | 
| SASL/SCRAM | TLS | Must be on\. | 
| SASL/IAM | TLS | Must be on\. | 

When client\-broker encryption is set to `TLS_PLAINTEXT` and client\-authentication is set to `mTLS`, Amazon MSK creates two types of listeners for clients to connect to: one listener for clients to connect using mTLS authentication with TLS Encryption, and another for clients to connect without authentication or encryption \(plaintext\)\.

For more information about security settings, see [Security in Amazon Managed Streaming for Apache Kafka](security.md)\. 

## Updating a cluster's security settings using the AWS Management Console<a name="update-security-console"></a>

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose the MSK cluster that you want to update\.

1. In the **Security settings** section, choose **Edit**\.

1. Choose the authentication and encryption settings that you want for the cluster, then choose **Save changes**\.

## Updating a cluster's security settings using the AWS CLI<a name="update-security-cli"></a>

1. Create a JSON file that contains the encryption settings that you want the cluster to have\. The following is an example\. 
**Note**  
You can only update the client\-broker encryption setting\. You can't update the in\-cluster \(broker\-to\-broker\) encryption setting\.

   ```
   {"EncryptionInTransit":{"ClientBroker": "TLS"}}
   ```

1. Create a JSON file that contains the authentication settings that you want the cluster to have\. The following is an example\.

   ```
   {"Sasl":{"Scram":{"Enabled":true}}}
   ```

1. Run the following AWS CLI command:

   ```
   aws kafka update-security --cluster-arn ClusterArn --current-version Current-Cluster-Version --client-authentication file://Path-to-Authentication-Settings-JSON-File --encryption-info file://Path-to-Encryption-Settings-JSON-File
   ```

   The output of this `update-security` operation looks like the following JSON\.

   ```
   {
       
       "ClusterArn": "arn:aws:kafka:us-east-1:012345678012:cluster/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2",
       "ClusterOperationArn": "arn:aws:kafka:us-east-1:012345678012:cluster-operation/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2/0123abcd-abcd-4f7f-1234-9876543210ef"
   }
   ```

1. To see the status of the `update-security` operation, run the following command, replacing *ClusterOperationArn* with the ARN that you obtained in the output of the `update-security` command\.

   ```
   aws kafka describe-cluster-operation --cluster-operation-arn ClusterOperationArn
   ```

   The output of this `describe-cluster-operation` command looks like the following JSON example\.

   ```
   {
       "ClusterOperationInfo": {
           "ClientRequestId": "c0b7af47-8591-45b5-9c0c-909a1a2c99ea",
           "ClusterArn": "arn:aws:kafka:us-east-1:012345678012:cluster/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2",
           "CreationTime": "2021-09-17T02:35:47.753000+00:00",
           "OperationArn": "arn:aws:kafka:us-east-1:012345678012:cluster-operation/exampleClusterName/abcdefab-1234-abcd-5678-cdef0123ab01-2/0123abcd-abcd-4f7f-1234-9876543210ef",
           "OperationState": "PENDING",
           "OperationType": "UPDATE_SECURITY",
           "SourceClusterInfo": {},
           "TargetClusterInfo": {}
       }
   }
   ```

   If `OperationState` has the value `PENDING` or `UPDATE_IN_PROGRESS`, wait a while, then run the `describe-cluster-operation` command again\. 

## Updating a cluster's security settings using the API<a name="update-security-api"></a>

To update the security settings for a cluster using the API, see [UpdateSecurity](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn-security.html#UpdateSecurity)\.

**Note**  
The AWS CLI and API operations for updating the security settings of a cluster are idempotent\. This means that if you invoke the security update operation and specify an authentication or encryption setting that is the same setting that the cluster currently has, that setting won't change\.