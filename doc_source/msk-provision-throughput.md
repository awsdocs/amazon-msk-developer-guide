# Provisioning storage throughput<a name="msk-provision-throughput"></a>

Amazon MSK brokers persist data on storage volumes\. Storage I/O is consumed when producers write to the cluster, when data is replicated between brokers, and when consumers read data that isn't in memory\. The volume storage throughput is the rate at which data can be written into and read from a storage volume\. Provisioned storage throughput is the ability to specify that rate for the brokers in your cluster\. 

You can specify the provisioned throughput rate in MiB per second for clusters whose brokers are of type `kafka.m5.4xlarge` or larger and if the storage volume is 10 GiB or greater\. It is possible to specify provisioned throughput during cluster creation\. You can also enable or disable provisioned throughput for a cluster that is in the `ACTIVE` state\.

## Throughput bottlenecks<a name="throughput-bottlenecks"></a>

There are multiple causes of bottlenecks in broker throughput: volume throughput, EC2\-EBS network throughput, and EC2 egress throughput\. You can enable provisioned storage throughput to adjust volume throughput\. However, broker throughput limitations can be caused by EC2\-EBS network throughput and EC2 egress throughput\. 

EC2 egress throughput is impacted by the number of consumer groups and consumers per consumer groups\. Also, both EC2\-EBS network throughput and EC2 egress throughput are higher for larger broker types, as shown in the following table\.


****  

| Broker type | EC2\-EBS network throughput \(MBps\) | 
| --- | --- | 
| kafka\.m5\.4xlarge | 593\.75 | 
| kafka\.m5\.8xlarge | 850 | 
| kafka\.m5\.12xlarge | 1187\.5 | 
| kafka\.m5\.16xlarge | 1700 | 
| kafka\.m5\.24xlarge | 2375 | 

## Measuring storage throughput<a name="throughput-metrics"></a>

You can use the `VolumeReadBytes` and `VolumeWriteBytes` metrics to measure the average storage throughput of a cluster\. The sum of these two metrics gives the average storage throughput in bytes\. To get the average storage throughput for a cluster, set these two metrics to SUM and the period to 1 minute, then use the following formula\.

```
Average storage throughput in MiB/s = (Sum(VolumeReadBytes) + Sum(VolumeWriteBytes)) / (60 * 1024 * 1024)
```

For information about the `VolumeReadBytes` and `VolumeWriteBytes` metrics, see [`PER_BROKER` Level monitoring](metrics-details.md#broker-metrics)\.

## Configuration update<a name="provisioned-throughput-config"></a>

You can update your Amazon MSK configuration either before or after you turn on provisioned throughput\. However, you won't see the desired throughput until you perform both actions: update the `num.replica.fetchers` configuration parameter and turn on provisioned throughput\. 

In the default Amazon MSK configuration, `num.replica.fetchers` has a value of 2\. To update your `num.replica.fetchers`, you can use the suggested values from the following table\. These values are for guidance purposes\. We recommend that you adjust these values based on your use case\. 


****  

| Broker type | num\.replica\.fetchers | 
| --- | --- | 
| kafka\.m5\.4xlarge | 4 | 
| kafka\.m5\.8xlarge | 8 | 
| kafka\.m5\.12xlarge | 14 | 
| kafka\.m5\.16xlarge | 16 | 
| kafka\.m5\.24xlarge | 16 | 

Your updated configuration may not take effect for up to 24 hours, and may take longer when a source volume is not fully utilized\. However, transitional volume performance at least equals the performance of source storage volumes during the migration period\. A fully\-utilized 1 TiB volume typically takes about six hours to migrate to an updated configuration\. 

## Provisioning storage throughput using the AWS Management Console<a name="provisioned-throughput-console"></a>

1. Sign in to the AWS Management Console, and open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home?region=us\-east\-1\#/home/](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/)\.

1. Choose **Create cluster**\.

1. Choose **Custom create**\.

1. Specify a name for the cluster\.

1. In the **Storage** section, choose **Enable**\.

1. Choose a value for storage throughput per broker\.

1. Choose a VPC, zones and subnets, and a security group\.

1. Choose **Next**\.

1. At the bottom of the **Security** step, choose **Next**\.

1. At the bottom of the **Monitoring and tags** step, choose **Next**\.

1. Review the cluster settings, then choose **Create cluster**\.

## Provisioning storage throughput using the AWS CLI<a name="provisioned-throughput-cli"></a>

This section shows an example of how you can use the AWS CLI to create a cluster with provisioned throughput enabled\.

1. Copy the following JSON and paste it into a file\. Replace the subnet IDs and security group ID placeholders with values from your account\. Name the file `cluster-creation.json` and save it\.

   ```
   {
       "Provisioned": {
           "BrokerNodeGroupInfo":{
               "InstanceType":"kafka.m5.4xlarge",
               "ClientSubnets":[
                   "Subnet-1-ID",
                   "Subnet-2-ID"
               ],
               "SecurityGroups":[
                   "Security-Group-ID"
               ],
               "StorageInfo": {
                   "EbsStorageInfo": {
                       "VolumeSize": 10,
                       "ProvisionedThroughput": {
                           "Enabled": true,
                           "VolumeThroughput": 250
                       }
                   }
               }
           },
           "EncryptionInfo": {
               "EncryptionInTransit": {
                   "InCluster": false,
                   "ClientBroker": "PLAINTEXT"
               }
           },
           "KafkaVersion":"2.2.1",
           "NumberOfBrokerNodes": 2
       },
       "ClusterName": "provisioned-throughput-example"
   }
   ```

1. Run the following AWS CLI command from the directory where you saved the JSON file in the previous step\.

   ```
   aws kafka create-cluster-v2 --cli-input-json file://cluster-creation.json
   ```

## Provisioning storage throughput using the API<a name="provisioned-throughput-api"></a>

To configure provisioned storage throughput while creating a cluster, use [CreateClusterV2](https://docs.aws.amazon.com/MSK/2.0/APIReference/v2-clusters.html#CreateClusterV2)\.