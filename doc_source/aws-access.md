# Access from within AWS but outside cluster's VPC<a name="aws-access"></a>

To connect to an MSK cluster from inside AWS but outside the cluster's Amazon VPC, the following options exist\.

## Amazon VPC peering<a name="vpc-peering"></a>

To connect to your MSK cluster from a VPC that's different from the cluster's VPC, you can create a peering connection between the two VPCs\. For information about VPC peering, see the [Amazon VPC Peering Guide](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html)\.

## AWS Direct Connect<a name="direct-connect"></a>

AWS Direct Connect links your on\-premise network to AWS over a standard 1 gigabit or 10 gigabit Ethernet fiber\-optic cable\. One end of the cable is connected to your router, the other to an AWS Direct Connect router\. With this connection in place, you can create virtual interfaces directly to the AWS cloud and Amazon VPC, bypassing Internet service providers in your network path\. For more information, see [AWS Direct Connect](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html)\.

## AWS Transit Gateway<a name="transit-gateway"></a>

AWS Transit Gateway is a service that enables you to connect your VPCs and your on\-premises networks to a single gateway\. For information about how to use AWS Transit Gateway, see [AWS Transit Gateway](https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html)\.

## VPN connections<a name="vpn-connections"></a>

You can connect your MSK cluster's VPC to remote networks and users using the VPN connectivity options described in the following topic: [VPN Connections](https://docs.aws.amazon.com/vpc/latest/userguide/vpn-connections.html)\.

## REST proxies<a name="rest-proxies"></a>

You can install a REST proxy on an instance running within your cluster's Amazon VPC\. REST proxies enable your producers and consumers to communicate with the cluster through HTTP API requests\.

## Multiple Region multi\-VPC connectivity<a name="multi-vpc-multi-region"></a>

The following document describes connectivity options for multiple VPCs that reside in different Regions: [Multiple Region Multi\-VPC Connectivity](https://aws.amazon.com/answers/networking/aws-multiple-region-multi-vpc-connectivity/)\.

## EC2\-Classic<a name="accessing-cluster-from-ec2-classic"></a>

Use the following procedure to connect to your cluster from an EC2\-Classic instance\.

1. Follow the guidance described in [ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html) to connect your EC2\-Classic instance to your cluster's VPC\.

1. Find and copy the private IP associated with your EC2\-Classic instance\.

1. Using the AWS CLI, run the following command, replacing *ClusterArn* with the Amazon Resource Name \(ARN\) for your MSK cluster\.

   ```
   aws kafka describe-cluster --cluster-arn "ClusterArn"
   ```

1. In the output of the `describe-cluster` command, look for `SecurityGroups` and save the ID of the security group for your MSK cluster\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left pane, choose **Security Groups**\.

1. Choose the security group whose ID you saved after you ran the `describe-cluster` command\. Select the box at the beginning of the row corresponding to this security group\.

1. In the lower half of the page, choose **Inbound Rules**\.

1. Choose **Edit rules**, then choose **Add Rule**\.

1. For the **Type** field, choose **All traffic** in the drop\-down list\.

1. Leave the **Source** set to **Custom** and enter the private IP of your EC2\-Classic instance, followed immediately by **/32** with no intervening spaces\.

1. Choose **Save rules**\.