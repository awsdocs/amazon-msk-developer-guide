# Step 2: Create an IAM role<a name="create-iam-role"></a>

In this step, you perform two tasks\. The first task is to create an IAM policy that grants access to create topics on the cluster and to send data to those topics\. The second task is to create an IAM role and associate this policy with it\. In a later step, we create a client machine that assumes this role and uses it to create a topic on the cluster and to send data to that topic\.

**To create an IAM policy that makes it possible to create topics and write to them**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. On the navigation pane, choose **Policies**\.

1. Choose **Create Policy**\.

1. Choose the **JSON** tab, then replace the JSON in the editor window with the following JSON\. 

   Replace *region* with the code of the AWS Region where you created your cluster\. Replace *Account\-ID* with your account IDReplace *msk\-serverless\-tutorial\-cluster* with the name of your serverless cluster\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "kafka-cluster:Connect",
                   "kafka-cluster:AlterCluster",
                   "kafka-cluster:DescribeCluster"
               ],
               "Resource": [
                   "arn:aws:kafka:region:Account-ID:cluster/msk-serverless-tutorial-cluster/*"
               ]
           },
           {
               "Effect": "Allow",
               "Action": [
                   "kafka-cluster:*Topic*",
                   "kafka-cluster:WriteData",
                   "kafka-cluster:ReadData"
               ],
               "Resource": [
                   "arn:aws:kafka:region:Account-ID:topic/msk-serverless-tutorial-cluster/*"
               ]
           },
           {
               "Effect": "Allow",
               "Action": [
                   "kafka-cluster:AlterGroup",
                   "kafka-cluster:DescribeGroup"
               ],
               "Resource": [
                   "arn:aws:kafka:region:Account-ID:group/msk-serverless-tutorial-cluster/*"
               ]
           }
       ]
   }
   ```

   For instructions on how to write secure policies, see [IAM access control](iam-access-control.md)\.

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. For the policy name, enter a descriptive name, such as **msk\-serverless\-tutorial\-policy**\.

1. Choose **Create policy**\.

**To create an IAM role and attach the policy to it**

1. On the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Under **Common use cases**, choose **EC2**, then choose **Next: Permissions**\.

1. In the search box, enter the name of the policy that you previously created for this tutorialThen select the box to the left of the policy\.

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. For the role name, enter a descriptive name, such as **msk\-serverless\-tutorial\-role**\.

1. Choose **Create role**\.

**Next Step**

[Step 3: Create a client machine](create-serverless-cluster-client.md)