# AWS managed policies for MSK Connect<a name="mkc-security-iam-awsmanpol"></a>

To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the `ViewOnlyAccess` AWS managed policy provides read\-only access to many AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.

## AWS managed policy: AmazonMSKConnectReadOnlyAccess<a name="security-iam-awsmanpol-AmazonMSKConnectReadOnlyAccess"></a>

This policy grants the user the permissions that are needed to list and describe MSK Connect resources\.

You can attach the `AmazonMSKConnectReadOnlyAccess` policy to your IAM identities\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kafkaconnect:ListConnectors",
                "kafkaconnect:ListCustomPlugins",
                "kafkaconnect:ListWorkerConfigurations"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "kafkaconnect:DescribeConnector"
            ],
            "Resource": [
                "arn:aws:kafkaconnect:*:*:connector/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "kafkaconnect:DescribeCustomPlugin"
            ],
            "Resource": [
                "arn:aws:kafkaconnect:*:*:custom-plugin/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "kafkaconnect:DescribeWorkerConfiguration"
            ],
            "Resource": [
                "arn:aws:kafkaconnect:*:*:worker-configuration/*"
            ]
        }
    ]
}
```

## AWS managed policy: KafkaConnectServiceRolePolicy<a name="security-iam-awsmanpol-KafkaConnectServiceRolePolicy"></a>

This policy grants the MSK Connect service the permissions that are needed to create and manage network interfaces that have the tag `AmazonMSKConnectManaged:true`\. These network interfaces give MSK Connect network access to resources in your Amazon VPC, such as an Apache Kafka cluster or a source or a sink\.

You can't attach KafkaConnectServiceRolePolicy to your IAM entities\. This policy is attached to a service\-linked role that allows MSK Connect to perform actions on your behalf\.

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"ec2:CreateNetworkInterface"
			],
			"Resource": "arn:aws:ec2:*:*:network-interface/*",
			"Condition": {
				"StringEquals": {
					"aws:RequestTag/AmazonMSKConnectManaged": "true"
				},
				"ForAllValues:StringEquals": {
					"aws:TagKeys": "AmazonMSKConnectManaged"
				}
			}
		},
		{
			"Effect": "Allow",
			"Action": [
				"ec2:CreateNetworkInterface"
			],
			"Resource": [
				"arn:aws:ec2:*:*:subnet/*",
				"arn:aws:ec2:*:*:security-group/*"
			]
		},
		{
			"Effect": "Allow",
			"Action": [
				"ec2:CreateTags"
			],
			"Resource": "arn:aws:ec2:*:*:network-interface/*",
			"Condition": {
				"StringEquals": {
					"ec2:CreateAction": "CreateNetworkInterface"
				}
			}
		},
		{
			"Effect": "Allow",
			"Action": [
				"ec2:DescribeNetworkInterfaces",
				"ec2:CreateNetworkInterfacePermission",
				"ec2:AttachNetworkInterface",
				"ec2:DetachNetworkInterface",
				"ec2:DeleteNetworkInterface"
			],
			"Resource": "arn:aws:ec2:*:*:network-interface/*",
			"Condition": {
				"StringEquals": {
					"ec2:ResourceTag/AmazonMSKConnectManaged": "true"
				}
			}
		}
	]
}
```

## MSK Connect updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>

View details about updates to AWS managed policies for MSK Connect since this service began tracking these changes\.


| Change | Description | Date | 
| --- | --- | --- | 
|  MSK Connect updated read\-only policy  |  MSK Connect updated the AmazonMSKConnectReadOnlyAccess policy to remove the restrictions on listing operations\.  | October 13, 2021 | 
|  MSK Connect started tracking changes  |  MSK Connect started tracking changes for its AWS managed policies\.  | September 14, 2021 | 