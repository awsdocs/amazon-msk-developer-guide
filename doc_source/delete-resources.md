# Step 6: Delete resources<a name="delete-resources"></a>

In this step, you delete the resources that you created in this tutorial\.

**To delete the cluster**

1. Open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home](https://console.aws.amazon.com/msk/home)\.

1. In the list of clusters, choose `msk-serverless-tutorial`\.

1. For **Actions**, choose **Delete cluster**\.

1. Enter `delete` in the field, then choose **Delete**\.

**To stop the client machine**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the list of Amazon EC2 instances, choose the client machine, which is the instance named **msk\-serverless\-tutorial**\.

1. Choose **Instance state**, then choose **Terminate instance**\.

1. Choose **Terminate**\.

**To delete the IAM policy and role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. On the navigation pane, choose **Roles**\.

1. In the search box, enter `msk-serverless-tutorial`\.

1. Choose the **msk\-serverless\-tutorial** role\.

1. Choose **Delete role**\.

1. Choose **Yes, delete**\.

1. On the navigation pane, choose **Policies**\.

1. In the search box, enter `msk-serverless-tutorial` followed by a return\.

1. Choose the **msk\-serverless\-tutorial** policy\.

1. On the policy's **Summary** page, choose **Delete policy**\.

1. Choose **Delete**\.