# Security in Amazon Managed Streaming for Apache Kafka<a name="security"></a>

Cloud security at AWS is the highest priority\. As an AWS customer, you benefit from a data center and network architecture that is built to meet the requirements of the most security\-sensitive organizations\.

Security is a shared responsibility between AWS and you\. The [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) describes this as security *of* the cloud and security *in* the cloud:
+ **Security of the cloud** – AWS is responsible for protecting the infrastructure that runs AWS services in the AWS Cloud\. AWS also provides you with services that you can use securely\. Third\-party auditors regularly test and verify the effectiveness of our security as part of the [AWS Compliance Programs](http://aws.amazon.com/compliance/programs/)\. To learn about the compliance programs that apply to Amazon Managed Streaming for Apache Kafka, see [Amazon Web Services in Scope by Compliance Program](http://aws.amazon.com/compliance/services-in-scope/)\.
+ **Security in the cloud** – Your responsibility is determined by the AWS service that you use\. You are also responsible for other factors including the sensitivity of your data, your company's requirements, and applicable laws and regulations\. 

This documentation helps you understand how to apply the shared responsibility model when using Amazon MSK\. The following topics show you how to configure Amazon MSK to meet your security and compliance objectives\. You also learn how to use other Amazon Web Services that help you to monitor and secure your Amazon MSK resources\. 

**Topics**
+ [Data protection in Amazon Managed Streaming for Apache Kafka](data-protection.md)
+ [Authentication and authorization for Amazon MSK APIs](security-iam.md)
+ [Authentication and authorization for Apache Kafka APIs](kafka_apis_iam.md)
+ [Changing an Amazon MSK cluster's security group](change-security-group.md)
+ [Controlling access to Apache ZooKeeper](zookeeper-security.md)
+ [Logging](msk-logging.md)
+ [Compliance validation for Amazon Managed Streaming for Apache Kafka](MSK-compliance.md)
+ [Resilience in Amazon Managed Streaming for Apache Kafka](disaster-recovery-resiliency.md)
+ [Infrastructure security in Amazon Managed Streaming for Apache Kafka](infrastructure-security.md)