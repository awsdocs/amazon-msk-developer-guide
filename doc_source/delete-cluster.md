# Step 7: Delete the AWS resources created for this tutorial<a name="delete-cluster"></a>

In the final step of [Getting Started Using Amazon MSK](getting-started.md), you delete the MSK cluster and the client machine that you created for this tutorial\.

**To delete the resources using the AWS Management Console**

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. Choose the name of your cluster\. For example, **MSKTutorialCluster**\.

1. Choose **Actions**, then choose **Delete**\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose the instance that you created for your client machine, for example, **MSKTutorialClient**\.

1. Choose **Instance state**, then choose **Terminate instance**\.

**To delete the IAM policy and role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. On the navigation pane, choose **Roles**\.

1. In the search box, enter the name of the IAM role that you created for this tutorial\.

1. Choose the role\. Then choose **Delete role**, and confirm the deletion\.

1. On the navigation pane, choose **Policies**\.

1. In the search box, enter the name of the policy that you created for this tutorial\.

1. Choose the policy to open its summary page\. On the policy's **Summary** page, choose **Delete policy**\.

1. Choose **Delete**\.