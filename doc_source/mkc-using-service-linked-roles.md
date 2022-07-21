# Using service\-linked roles for MSK Connect<a name="mkc-using-service-linked-roles"></a>

Amazon MSK Connect uses AWS Identity and Access Management \(IAM\)[ service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to MSK Connect\. Service\-linked roles are predefined by MSK Connect and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up MSK Connect easier because you don't have to manually add the necessary permissions\. MSK Connect defines the permissions of its service\-linked roles, and unless defined otherwise, only MSK Connect can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy cannot be attached to any other IAM entity\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) and look for the services that have **Yes **in the **Service\-Linked Role** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

## Service\-linked role permissions for MSK Connect<a name="slr-permissions"></a>

MSK Connect uses the service\-linked role named **AWSServiceRoleForKafkaConnect** – Allows Amazon MSK Connect to access Amazon resources on your behalf\.

The AWSServiceRoleForKafkaConnect service\-linked role trusts the `kafkaconnect.amazonaws.com` service to assume the role\.

For information about the permissions policy that the role uses, see [AWS managed policy: KafkaConnectServiceRolePolicy](mkc-security-iam-awsmanpol.md#security-iam-awsmanpol-KafkaConnectServiceRolePolicy)\.

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. For more information, see [Service\-Linked Role Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

## Creating a service\-linked role for MSK Connect<a name="create-slr"></a>

You don't need to manually create a service\-linked role\. When you create a connector in the AWS Management Console, the AWS CLI, or the AWS API, MSK Connect creates the service\-linked role for you\. 

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\. When you create a connector, MSK Connect creates the service\-linked role for you again\. 

## Editing a service\-linked role for MSK Connect<a name="edit-slr"></a>

MSK Connect does not allow you to edit the AWSServiceRoleForKafkaConnect service\-linked role\. After you create a service\-linked role, you can't change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Deleting a service\-linked role for MSK Connect<a name="delete-slr"></a>

You can use the IAM console, the AWS CLI or the AWS API to manually delete the service\-linked role\. To do this, you must first manually delete all of your MSK Connect connectors, and then you can manually delete the role\. For more information, see [Deleting a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.

## Supported Regions for MSK Connect service\-linked roles<a name="slr-regions"></a>

MSK Connect supports using service\-linked roles in all of the regions where the service is available\. For more information, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.