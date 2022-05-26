# Amazon MSK Encryption<a name="msk-encryption"></a>

Amazon MSK provides data encryption options that you can use to meet strict data management requirements\. The certificates that Amazon MSK uses for encryption must be renewed every 13 months\. Amazon MSK automatically renews these certificates for all clusters\. It sets the state of the cluster to `MAINTENANCE` when it starts the certificate\-update operation\. It sets it back to `ACTIVE` when the update is done\. While a cluster is in the `MAINTENANCE` state, you can continue to produce and consume data, but you can't perform any update operations on it\. 

## Encryption at Rest<a name="msk-encryption-at-rest"></a>

Amazon MSK integrates with [AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/) \(KMS\) to offer transparent server\-side encryption\. Amazon MSK always encrypts your data at rest\. When you create an MSK cluster, you can specify the AWS KMS key that you want Amazon MSK to use to encrypt your data at rest\. If you don't specify a KMS key, Amazon MSK creates an [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) for you and uses it on your behalf\. For more information about KMS keys, see [AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#kms_keys) in the *AWS Key Management Service Developer Guide*\.

## Encryption in Transit<a name="msk-encryption-in-transit"></a>

Amazon MSK uses TLS 1\.2\. By default, it encrypts data in transit between the brokers of your MSK cluster\. You can override this default at the time you create the cluster\. 

For communication between clients and brokers, you must specify one of the following three settings:
+ Only allow TLS encrypted data\. This is the default setting\.
+ Allow both plaintext, as well as TLS encrypted data\.
+ Only allow plaintext data\.

Amazon MSK brokers use public AWS Certificate Manager certificates\. Therefore, any truststore that trusts Amazon Trust Services also trusts the certificates of Amazon MSK brokers\.

While we highly recommend enabling in\-transit encryption, it can add additional CPU overhead and a few milliseconds of latency\. Most use cases aren't sensitive to these differences, however, and the magnitude of impact depends on the configuration of your cluster, clients, and usage profile\. 