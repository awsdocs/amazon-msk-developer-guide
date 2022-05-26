# Connecting to an Amazon MSK Cluster<a name="client-access"></a>

By default, clients can access an MSK cluster only if they're in the same VPC as the cluster\. To connect to your MSK cluster from a client that's in the same VPC as the cluster, make sure the cluster's security group has an inbound rule that accepts traffic from the client's security group\. For information about setting up these rules, see [Security Group Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules)\. For an example of how to access a cluster from an Amazon EC2 instance that's in the same VPC as the cluster, see [Getting Started Using Amazon MSK](getting-started.md)\.

To connect to your MSK cluster from a client that's outside the cluster's VPC, see the following topics:

**Topics**
+ [Public access](public-access.md)
+ [Access from within AWS but outside cluster's VPC](aws-access.md)
+ [Port information](port-info.md)