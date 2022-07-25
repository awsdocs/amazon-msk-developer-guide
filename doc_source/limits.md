# Amazon MSK quota<a name="limits"></a>

## Amazon MSK quota<a name="msk-provisioned-quota"></a>
+ Up to 90 brokers per account and 30 brokers per cluster\. To request higher quota, [create a support case](https://console.aws.amazon.com/support/home#/)\.
+ A minimum of 1 GiB of storage per broker\.
+ A maximum of 16384 GiB of storage per broker\.
+ A cluster that uses [IAM access control](iam-access-control.md) can have up to 3000 TCP connections per broker at any given time\. To increase this limit, you can adjust the `listener.name.client_iam.max.connections` or the `listener.name.client_iam_public.max.connections` configuration property using the Kafka AlterConfig API or the `kafka-configs.sh` tool\. It's important to note that increasing either property to a high value can result in unavailability\.
+ Limits on TCP connections\. A cluster that uses [IAM access control](iam-access-control.md) can accept new connections at a rate of up to 20 TCP connections per broker per second for all broker types, except for the type kafka\.t3\.small\. Brokers of type kafka\.t3\.small are limited to 4 TCP connections per broker per second\. If you created your cluster after May 25, 2022, it also supports connection rate bursts\. If you want an older cluster to support connection rate bursts, you can [create a support case](https://console.aws.amazon.com/support/home#/)\.

  To handle retries on failed connections, you can set the `reconnect.backoff.ms` configuration parameter on the client side\. For example, if you want a client to retry connections after 1 second, set `reconnect.backoff.ms` to 1000\. For more information, see [https://kafka.apache.org/documentation/#producerconfigs_reconnect.backoff.ms](https://kafka.apache.org/documentation/#producerconfigs_reconnect.backoff.ms) in the Apache Kafka documentation\.
+ Up to 100 configurations per account\. To request higher quota, [create a support case](https://console.aws.amazon.com/support/home#/)\.
+ A maximum of 50 revisions per configuration\.
+ To update the configuration or the Apache Kafka version of an MSK cluster, first ensure the number of partitions per broker is under the limits described in [ Right\-size your cluster: Number of partitions per broker](bestpractices.md#partitions-per-broker)\.

## MSK Serverless quota<a name="serverless-quota"></a>


****  

| Dimension | Quota | 
| --- | --- | 
|  Maximum ingress throughput  |  200 MBps  | 
| Maximum egress throughput | 400 MBps | 
| Maximum retention duration | 24 hours\. To request a quota adjustment, [create a support case](https://console.aws.amazon.com/support/home#/)\. | 
|  Maximum number of client connections  | 1000 | 
| Maximum connection attempts | 100 per second | 
|  Maximum message size  | 8 MB | 
|  Maximum request size  | 100 MB | 
| Maximum request rate | 15,000 per second | 
|  Maximum fetch bytes per request  | 55 MB | 
|  Maximum number of consumer groups  | 500 | 
|  Maximum number of partitions  | 120 | 
| Maximum rate of partition creation and deletion | 120 in 5 minutes | 
|  Maximum ingress throughput per partition  | 5 MBps | 
|  Maximum egress throughput per partition  | 10 MBps | 
|  Maximum partition size  | 250 GB | 
|  Maximum number of client VPCs per serverless cluster  | 5 | 
| Maximum number of serverless clusters per account | 3 | 

## MSK Connect quota<a name="mkc-quota"></a>
+ Up to 100 custom plugins\.
+ Up to 100 worker configurations\.
+ Up to 60 connect workers\. If a connector is set up to have auto scaled capacity, then the maximum number of workers that the connector is set up to have is the number MSK Connect uses to calculate the quota for the account\.

To request higher quota for MSK Connect, [create a support case](https://console.aws.amazon.com/support/home#/)\.