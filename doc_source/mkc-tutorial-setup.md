# Step 1: Set up required resources<a name="mkc-tutorial-setup"></a>

In this step you create the following resources that you need for this getting\-started scenario:
+ An S3 bucket to serve as the destination that receives data from the connector\.
+ An MSK cluster to which you will send data\. The connector will then read the data from this cluster and send it to the destination S3 bucket\.
+ An IAM role that allows the connector to write to the destination S3 bucket\.
+ An Amazon VPC endpoint to make it possible to send data from the Amazon VPC that has the cluster and the connector to Amazon S3\.

**To create the S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

1. Enter `mkc-tutorial-destination-bucket` for the name of the bucket\.

1. Scroll down and choose **Create bucket**\.

1. In the list of bucket, choose the newly created `mkc-tutorial-destination-bucket`\.

1. Choose **Create folder**\.

1. Enter `tutorial` for the name of the folder, then scroll down and choose **Create folder**\.

**To create the cluster**

1. Open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home?region=us\-east\-1\#/home/](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/)\.

1. In the left pane, under **MSK Clusters**, choose **Clusters**\.

1. Choose **Create cluster**\.

1. Choose **Custom create**\.

1. For the cluster name enter `mkc-tutorial-cluster`\.

1. Under **Networking**, choose an Amazon VPC, then set the **Number of Zones** to 2, then select the Availability Zones and subnets that you want to use\. Remember the IDs the of the Amazon VPC and subnets you selected because we need them later in this tutorial\.

1. Under **Access control methods** ensure that only **Unauthenticated access** is selected\.

1. Under **Encryption** ensure that only **Plaintext** is selected\.

1. Scroll down and choose **Create cluster**\. This takes you to the cluster's details page\. On that page, look for the security group ID under **Security groups applied**\. Remember that ID because we need it later in this tutorial\.

**To create the IAM role that can write to the destination bucket**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left pane, under **Access management**, choose **Roles**\.

1. Choose **Create role**\.

1. Under **Or select a service to view its use cases**, choose **S3**\.

1. Scroll down and under **Select your use case**, again choose **S3**\.

1. Choose **Next: Permissions**\.

1. Choose **Create policy**\. This opens a new tab in your browser where you will create the policy\. Leave the original role\-creation tab open because we'll get back to it later\.

1. Choose the **JSON** tab, then replace the text in the window with the following policy\.

   ```
   {
    "Version":"2012-10-17",
    "Statement":[
       {
       "Effect":"Allow",
       "Action":[
           "s3:ListAllMyBuckets"
           ],
       "Resource":"arn:aws:s3:::*"
       },
       {
       "Effect":"Allow",
       "Action":[
           "s3:ListBucket",
           "s3:GetBucketLocation"
           ],
       "Resource":"arn:aws:s3:::mkc-tutorial-destination-bucket"
       },
       {
       "Effect":"Allow",
       "Action":[
           "s3:PutObject",
           "s3:GetObject",
           "s3:AbortMultipartUpload",
           "s3:ListMultipartUploadParts",
           "s3:ListBucketMultipartUploads"
           ],
       "Resource":"*"
       }
    ]
   }
   ```

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. Enter `mkc-tutorial-policy` for the policy name, then scroll down and choose **Create policy**\.

1. Back in the browser tab where you were creating the role, choose the refresh button\.

1. Find the `mkc-tutorial-policy` and select it by choosing the button to its left\.

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. Enter `mkc-tutorial-role` for the role name, and delete the text in the description box\.

1. Choose **Create role**\.

**To allow MSK Connect to assume the role**

1. In the IAM console, in the left pane, under **Access management**, choose **Roles**\.

1. Find the `mkc-tutorial-role` and choose it\.

1. Under the role's **Summary**, choose the **Trust relationships** tab\.

1. Choose **Edit trust relationship**\.

1. Replace the existing trust policy with the following JSON\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": "kafkaconnect.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

1. Choose **Update Trust Policy**\.

**To create an Amazon VPC endpoint from the cluster's VPC to Amazon S3**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left pane, choose **Endpoints**\.

1. Choose **Create endpoint**\.

1. Under **Service Name** choose the **com\.amazonaws\.us\-east\-1\.s3** service and the **Gateway** type\.

1. Choose the cluster's VPC and then select the box to the left of the route table that is associated with the cluster's subnets\.

1. Choose **Create endpoint**\.

**Next Step**

[Step 2: Create custom plugin](mkc-create-plugin.md)