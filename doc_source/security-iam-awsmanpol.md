# AWS managed policies for Amazon MSK<a name="security-iam-awsmanpol"></a>

To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the `ViewOnlyAccess` AWS managed policy provides read\-only access to many AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.

## AWS managed policy: AmazonMSKFullAccess<a name="security-iam-awsmanpol-AmazonMSKFullAccess"></a>

This policy grants administrative permissions that allow a principal full access to all Amazon MSK actions\. The permissions in this policy are grouped as follows:
+ The Amazon MSK permissions allow all Amazon MSK actions\.
+ Some of the Amazon EC2 permissions in this policy are required to validate the passed resources in an API request\. This is to make sure Amazon MSK is able to successfully use the resources with a cluster\. The rest of the Amazon EC2 permissions in this policy allow Amazon MSK to create AWS resources that are needed to make it possible for you to connect to your clusters\.
+ The AWS KMS permissions are used during API calls to validate the passed resources in a request\. They are required for Amazon MSK to be able to use the passed key with the Amazon MSK cluster\.
+ The CloudWatch Logs, Amazon S3, and Amazon Kinesis Data Firehose permissions are required for Amazon MSK to be able to ensure that the log delivery destinations are reachable, and that they are valid for broker log use\.

```
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "kafka:*",
                    "ec2:DescribeSubnets",
                    "ec2:DescribeVpcs",
                    "ec2:DescribeSecurityGroups",
                    "ec2:DescribeRouteTables",
                    "ec2:DescribeVpcEndpoints",
                    "ec2:DescribeVpcAttribute",
                    "kms:DescribeKey",
                    "kms:CreateGrant",
                    "logs:CreateLogDelivery",
                    "logs:GetLogDelivery",
                    "logs:UpdateLogDelivery",
                    "logs:DeleteLogDelivery",
                    "logs:ListLogDeliveries",
                    "logs:PutResourcePolicy",
                    "logs:DescribeResourcePolicies",
                    "logs:DescribeLogGroups",
                    "S3:GetBucketPolicy",
                    "firehose:TagDeliveryStream"
                ],
                "Resource": "*"
            },
            {
                "Effect": "Allow",
                "Action": [
                    "ec2:CreateVpcEndpoint"
                ],
                "Resource": [
                    "arn:*:ec2:*:*:vpc/*",
                    "arn:*:ec2:*:*:subnet/*",
                    "arn:*:ec2:*:*:security-group/*"
                ]
            },
            {
                "Effect": "Allow",
                "Action": [
                    "ec2:CreateVpcEndpoint"
                ],
                "Resource": [
                    "arn:*:ec2:*:*:vpc-endpoint/*"
                ],
                "Condition": {
                    "StringEquals": {
                        "aws:RequestTag/AWSMSKManaged": "true"
                    },
                    "StringLike": {
                        "aws:RequestTag/ClusterArn": "*"
                    }
                }
            },
            {
                "Effect": "Allow",
                "Action": [
                    "ec2:CreateTags"
                ],
                "Resource": "arn:*:ec2:*:*:vpc-endpoint/*",
                "Condition": {
                    "StringEquals": {
                        "ec2:CreateAction": "CreateVpcEndpoint"
                    }
                }
            },
            {
                "Effect": "Allow",
                "Action": [
                    "ec2:DeleteVpcEndpoints"
                ],
                "Resource": "arn:*:ec2:*:*:vpc-endpoint/*",
                "Condition": {
                    "StringEquals": {
                        "ec2:ResourceTag/AWSMSKManaged": "true"
                    },
                    "StringLike": {
                        "ec2:ResourceTag/ClusterArn": "*"
                    }
                }
            },
            {
                "Effect": "Allow",
                "Action": "iam:CreateServiceLinkedRole",
                "Resource": "arn:aws:iam::*:role/aws-service-role/kafka.amazonaws.com/AWSServiceRoleForKafka*",
                "Condition": {
                    "StringLike": {
                        "iam:AWSServiceName": "kafka.amazonaws.com"
                    }
                }
            },
            {
                "Effect": "Allow",
                "Action": [
                    "iam:AttachRolePolicy",
                    "iam:PutRolePolicy"
                ],
                "Resource": "arn:aws:iam::*:role/aws-service-role/kafka.amazonaws.com/AWSServiceRoleForKafka*"
            },
            {
                "Effect": "Allow",
                "Action": "iam:CreateServiceLinkedRole",
                "Resource": "arn:aws:iam::*:role/aws-service-role/delivery.logs.amazonaws.com/AWSServiceRoleForLogDelivery*",
                "Condition": {
                    "StringLike": {
                        "iam:AWSServiceName": "delivery.logs.amazonaws.com"
                    }
                }
            }

        ]
    }
```

## AWS managed policy: AmazonMSKReadOnlyAccess<a name="security-iam-awsmanpol-AmazonMSKReadOnlyAccess"></a>

This policy grants read\-only permissions that allow users to view information in Amazon MSK\. Principals with this policy attached can't make any updates or delete exiting resources, nor can they create new Amazon MSK resources\. For example, principals with these permissions can view the list of clusters and configurations associated with their account, but cannot change the configuration or settings of any clusters\. The permissions in this policy are grouped as follows:
+ The Amazon MSK permissions allow you to list Amazon MSK resources, describe them, and get information about them\.
+ The Amazon EC2 permissions are used to describe the Amazon VPC, subnets, security groups, and ENIs that are associated with a cluster\.
+ The AWS KMS permission is used to describe the key that is associated with the cluster\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "kafka:Describe*",
                "kafka:List*",
                "kafka:Get*",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcs",
                "kms:DescribeKey"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: KafkaServiceRolePolicy<a name="security-iam-awsmanpol-KafkaServiceRolePolicy"></a>

You can't attach KafkaServiceRolePolicy to your IAM entities\. This policy is attached to a service\-linked role that allows Amazon MSK to perform actions on your behalf\. For more information, see [Using Service\-Linked Roles for Amazon MSK](using-service-linked-roles.md)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateNetworkInterface",
                "ec2:DescribeNetworkInterfaces",
                "ec2:CreateNetworkInterfacePermission",
                "ec2:AttachNetworkInterface",
                "ec2:DeleteNetworkInterface",
                "ec2:DetachNetworkInterface", 
                "acm-pca:GetCertificateAuthorityCertificate",
                "secretsmanager:ListSecrets"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetResourcePolicy",
                "secretsmanager:PutResourcePolicy",
                "secretsmanager:DeleteResourcePolicy",
                "secretsmanager:DescribeSecret"
            ],
            "Resource": "*",
            "Condition": {
                "ArnLike": {
                    "secretsmanager:SecretId": "arn:*:secretsmanager:*:*:secret:AmazonMSK_*"
                }
            }
        }
    ]
}
```

## Amazon MSK updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>

View details about updates to AWS managed policies for Amazon MSK since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the Amazon MSK [Document History for Amazon MSK Developer Guide](doc-history.md) page\.


| Change | Description | Date | 
| --- | --- | --- | 
|  [AmazonMSKFullAccess](#security-iam-awsmanpol-AmazonMSKFullAccess) – Update to an existing policy  |  Amazon MSK added new Amazon EC2 permissions to make it possible to connect to a cluster\.  | November 30, 2021 | 
|  [AmazonMSKFullAccess](#security-iam-awsmanpol-AmazonMSKFullAccess) – Update to an existing policy  |  Amazon MSK added a new permission to allow it to describe Amazon EC2 route tables\.  | November 19, 2021 | 
|  Amazon MSK started tracking changes  |  Amazon MSK started tracking changes for its AWS managed policies\.  | November 19, 2021 | 