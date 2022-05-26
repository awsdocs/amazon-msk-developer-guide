# Port information<a name="port-info"></a>

The following list provides the numbers of the ports that Amazon MSK uses to communicate with client machines\.
+ To communicate with brokers in plaintext, use port 9092\.
+ To communicate with brokers by using TLS encryption, use port 9094 for access from within AWS and port 9194 for public access\.
+ To communicate with brokers by using SASL/SCRAM, use port is 9096 for access from within AWS and port 9196 for public access\.
+ To communicate with brokers in a cluster that is set up to use [IAM access control](iam-access-control.md), use port 9098 for access from within AWS and port 9198 for public access\.
+ Apache ZooKeeper nodes use port 2181 by default\. To communicate with Apache ZooKeeper by using TLS encryption, use port 2182\.