# Enabling internet access for Amazon MSK Connect<a name="msk-connect-internet-access"></a>

If your connector for Amazon MSK Connect needs access to the internet, we recommend that you use the following Amazon Virtual Private Cloud \(VPC\) settings to enable that access\.
+ Configure your connector with private subnets\.
+ Create a public [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) or [NAT instance](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html) for your VPC in a public subnet\. For more information, see the [Connect subnets to the internet or other VPCs using NAT devices](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) page in the *Amazon Virtual Private Cloud* *User Guide*\. 
+ Allow outbound traffic from your private subnets to your NAT gateway or instance\.

**Workaround for public subnets**

If you configure your cluster to use public subnets, you can attach an [Elastic IP address \(EIP\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html) to the elastic network interface \(ENI\) that Amazon MSK Connect creates in your VPC\. We don't recommend this method, because ENI attachments are not permanent, and the service may create or remove ENIs during maintenance or scaling operations\.

## Setting up a NAT gateway for Amazon MSK Connect<a name="msk-connect-internet-access-private-subnets-example"></a>

The following steps show you how to set up a NAT gateway to enable internet access for a connector\. You must complete these steps before you create a connector in a private subnet\.

**Prerequisites**

Make sure you have the following items\.
+ The ID of the Amazon Virtual Private Cloud \(VPC\) associated with your cluster\. For example, *vpc\-123456ab*\.
+ The IDs of the private subnets in your VPC\. For example, *subnet\-a1b2c3de*, *subnet\-f4g5h6ij*, etc\. You must configure your connector with private subnets\.

**To enable internet access for your connector**

1. Open the Amazon Virtual Private Cloud console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Create a public subnet for your NAT gateway with a descriptive name, and note the subnet ID\. For detailed instructions, see [Create a subnet in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#AddaSubnet)\.

1. Create an internet gateway so that your VPC can communicate with the internet, and note the gateway ID\. Attach the internet gateway to your VPC\. For instructions, see [Create and attach an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Attach_Gateway)\.

1. Provision a public NAT gateway so that hosts in your private subnets can reach your public subnet\. When you create the NAT gateway, select the public subnet that you created earlier\. For instructions, see [Create a NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-creating)\.

1. Configure your route tables\. You must have two route tables in total to complete this setup\. You should already have a main route table that was automatically created at the same time as your VPC\. In this step you create an additional route table for your public subnet\.

   1. Use the following settings to modify your VPC's main route table so that your private subnets route traffic to your NAT gateway\. For instructions, see [Work with route tables](https://docs.aws.amazon.com/vpc/latest/userguide/WorkWithRouteTables.html) in the *Amazon Virtual Private Cloud* *User Guide*\.  
**Private MSKC route table**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/msk/latest/developerguide/msk-connect-internet-access.html)

   1. Follow the instructions in [Create a custom route table](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Routing) to create a route table for your public subnet\. When you create the table, enter a descriptive name in the **Name tag** field to help you identify which subnet the table is associated with\. For example, **Public MSKC**\.

   1. Configure your **Public MSKC** route table using the following settings\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/msk/latest/developerguide/msk-connect-internet-access.html)