# Authentication and Authorization for Amazon MSK APIs<a name="security-iam"></a>

AWS Identity and Access Management \(IAM\) is an AWS service that helps an administrator securely control access to AWS resources\. IAM administrators control who can be *authenticated* \(signed in\) and *authorized* \(have permissions\) to use Amazon MSK resources\. IAM is an AWS service that you can use with no additional charge\.

This page describes how you can use IAM to control who can perform [Amazon MSK operations](https://docs.aws.amazon.com/msk/1.0/apireference/operations.html) on your cluster\. For information on how to control who can perform Apache Kafka operations on your cluster, see [Authentication and Authorization for Apache Kafka APIs](kafka_apis_iam.md)\.

**Topics**
+ [How Amazon MSK Works with IAM](security_iam_service-with-iam.md)
+ [Amazon MSK Identity\-Based Policy Examples](security_iam_id-based-policy-examples.md)
+ [Using Service\-Linked Roles for Amazon MSK](using-service-linked-roles.md)
+ [AWS managed policies for Amazon MSK](security-iam-awsmanpol.md)
+ [Troubleshooting Amazon MSK Identity and Access](security_iam_troubleshoot.md)