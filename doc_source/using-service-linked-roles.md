# Using Service\-Linked Roles for Amazon MSK<a name="using-service-linked-roles"></a>

Amazon MSK uses AWS Identity and Access Management \(IAM\) [ service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to Amazon MSK\. Service\-linked roles are predefined by Amazon MSK and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up Amazon MSK easier because you do not have to manually add the necessary permissions\. Amazon MSK defines the permissions of its service\-linked roles\. Unless defined otherwise, only Amazon MSK can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy cannot be attached to any other IAM entity\.

For information about other services that support service\-linked roles, see [Amazon Web Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html), and look for the services that have **Yes **in the **Service\-Linked Role** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

**Topics**
+ [Service\-Linked Role Permissions](#slr-permissions)
+ [Creating a Service\-Linked Role](#create-slr)
+ [Editing a Service\-Linked Role](#edit-slr)
+ [Supported Regions for Service\-Linked Roles](#slr-regions)

## Service\-Linked Role Permissions for Amazon MSK<a name="slr-permissions"></a>

Amazon MSK uses the service\-linked role named **AWSServiceRoleForKafka** – Allows Amazon MSK to access AWS resources on your behalf\.

The AWSServiceRoleForKafka service\-linked role trusts the following services to assume the role:
+ `kafka.amazonaws.com`

The role permissions policy allows Amazon MSK to complete the following actions on the specified resources:
+ Action: `ec2:CreateNetworkInterface` on `*`
+ Action: `ec2:DescribeNetworkInterfaces` on `*`
+ Action: `ec2:CreateNetworkInterfacePermission` on `*`
+ Action: `ec2:AttachNetworkInterface` on `*`
+ Action: `ec2:DeleteNetworkInterface` on `*`
+ Action: `ec2:DetachNetworkInterface` on `*`
+ Action: `acm-pca:GetCertificateAuthorityCertificate` on `*`
+ Action: `secretsmanager:ListSecrets` on `*`
+ Action: `secretsmanager:GetResourcePolicy` on secrets with the prefix `AmazonMSK_` that you create for Amazon MSK
+ Action: `secretsmanager:PutResourcePolicy` on secrets with the prefix `AmazonMSK_` that you create for Amazon MSK
+ Action: `secretsmanager:DeleteResourcePolicy` on secrets with the prefix `AmazonMSK_` that you create for Amazon MSK
+ Action: `secretsmanager:DescribeSecret` on secrets with the prefix `AmazonMSK_` that you create for Amazon MSK

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. For more information, see [Service\-Linked Role Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

## Creating a Service\-Linked Role for Amazon MSK<a name="create-slr"></a>

You don't need to create a service\-linked role manually\. When you create an Amazon MSK cluster in the AWS Management Console, the AWS CLI, or the AWS API, Amazon MSK creates the service\-linked role for you\. 

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\. When you create an Amazon MSK cluster, Amazon MSK creates the service\-linked role for you again\. 

## Editing a Service\-Linked Role for Amazon MSK<a name="edit-slr"></a>

Amazon MSK does not allow you to edit the AWSServiceRoleForKafka service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Supported Regions for Amazon MSK Service\-Linked Roles<a name="slr-regions"></a>

Amazon MSK supports using service\-linked roles in all of the Regions where the service is available\. For more information, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.