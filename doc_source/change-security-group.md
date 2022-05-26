# Changing an Amazon MSK cluster's security group<a name="change-security-group"></a>

This page explains how to change the security group of an existing MSK cluster\. You might need to change a cluster's security group in order to provide access to a certain set of users or to limit access to the cluster\. For information about security groups, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the Amazon VPC user guide\.

1. Use the [ListNodes](https://docs.amazonaws.cn/en_us/msk/1.0/apireference/clusters-clusterarn-nodes.html#ListNodes) API or the [list\-nodes](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/kafka/list-nodes.html) command in the AWS CLI to get a list of the brokers in your cluster\. The results of this operation include the IDs of the elastic network interfaces \(ENIs\) that are associated with the brokers\.

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Using the dropdown list near the top\-right corner of the screen, select the Region in which the cluster is deployed\.

1. In the left pane, under **Network & Security**, choose **Network Interfaces**\.

1. Select the first ENI that you obtained in the first step\. Choose the **Actions** menu at the top of the screen, then choose **Change Security Groups**\. Assign the new security group to this ENI\. Repeat this step for each of the ENIs that you obtained in the first step\.
**Note**  
Changes that you make to a cluster's security group using the Amazon EC2 console aren't reflected in the MSK console under **Network settings**\.

1. Configure the new security group's rules to ensure that your clients have access to the brokers\. For information about setting security group rules, see [Adding, Removing, and Updating Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html?shortFooter=true#AddRemoveRules) in the Amazon VPC user guide\.

**Important**  
If you change the security group that is associated with the brokers of a cluster, and then add new brokers to that cluster, Amazon MSK associates the new brokers with the original security group that was associated with the cluster when the cluster was created\. However, for a cluster to work correctly, all of its brokers must be associated with the same security group\. Therefore, if you add new brokers after changing the security group, you must follow the previous procedure again and update the ENIs of the new brokers\.