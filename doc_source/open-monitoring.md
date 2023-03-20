# Open monitoring with Prometheus<a name="open-monitoring"></a>

You can monitor your MSK cluster with Prometheus, an open\-source monitoring system for time\-series metric data\. You can publish this data to Amazon Managed Service for Prometheus using Prometheus's remote write feature\. You can also use tools that are compatible with Prometheus\-formatted metrics or tools that integrate with Amazon MSK Open Monitoring, like [Datadog](https://docs.datadoghq.com/integrations/amazon_msk/), [Lenses](https://docs.lenses.io/install_setup/deployment-options/aws-deployment.html#msk-prometheus-metrics), [New Relic](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-managed-kafka-msk-integration), and [Sumo logic](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Amazon_MSK_Prometheus_metrics_collection)\. Open monitoring is available for free but charges apply for the transfer of data across Availability Zones\. For information about Prometheus, see the [Prometheus documentation](https://prometheus.io/docs)\.

## Creating an Amazon MSK cluster with open monitoring enabled<a name="enable-open-monitoring-at-creation"></a>

**Using the AWS Management Console**

1. Sign in to the AWS Management Console, and open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home?region=us\-east\-1\#/home/](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/)\.

1. In the **Monitoring** section, select the check box next to **Enable open monitoring with Prometheus**\.

1. Provide the required information in all the sections of the page, and review all the available options\.

1. Choose **Create cluster**\.

**Using the AWS CLI**
+ Invoke the [create\-cluster](https://docs.aws.amazon.com/cli/latest/reference/kafka/create-cluster.html) command and specify its `open-monitoring` option\. Enable the `JmxExporter`, the `NodeExporter`, or both\. If you specify `open-monitoring`, the two exporters can't be disabled at the same time\.

**Using the API**
+ Invoke the [CreateCluster](https://docs.aws.amazon.com/msk/1.0/apireference/clusters.html#CreateCluster) operation and specify `OpenMonitoring`\. Enable the `jmxExporter`, the `nodeExporter`, or both\. If you specify `OpenMonitoring`, the two exporters can't be disabled at the same time\.

## Enabling open monitoring for an existing Amazon MSK cluster<a name="enable-open-monitoring-after-creation"></a>

To enable open monitoring, make sure that the cluster is in the `ACTIVE` state\.

**Using the AWS Management Console**

1. Sign in to the AWS Management Console, and open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home?region=us\-east\-1\#/home/](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/)\.

1. Choose the name of the cluster that you want to update\. This takes you to a page the contains details for the cluster\.

1. On the **Properties** tab, scroll down to find the **Monitoring** section\.

1. Choose **Edit**\.

1. Select the check box next to **Enable open monitoring with Prometheus**\.

1. Choose **Save changes**\.

**Using the AWS CLI**
+ Invoke the [update\-monitoring](https://docs.aws.amazon.com/cli/latest/reference/kafka/update-monitoring.html) command and specify its `open-monitoring` option\. Enable the `JmxExporter`, the `NodeExporter`, or both\. If you specify `open-monitoring`, the two exporters can't be disabled at the same time\.

**Using the API**
+ Invoke the [UpdateMonitoring](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn-monitoring.html#UpdateMonitoring) operation and specify `OpenMonitoring`\. Enable the `jmxExporter`, the `nodeExporter`, or both\. If you specify `OpenMonitoring`, the two exporters can't be disabled at the same time\.

## Setting up a Prometheus host on an Amazon EC2 instance<a name="set-up-prometheus-host"></a>

1. Download the Prometheus server from [https://prometheus.io/download/#prometheus](https://prometheus.io/download/#prometheus) to your Amazon EC2 instance\.

1. Extract the downloaded file to a directory and go to that directory\.

1. Create a file with the following contents and name it `prometheus.yml`\.

   ```
   # file: prometheus.yml
   # my global config
   global:
     scrape_interval:     60s
   
   # A scrape configuration containing exactly one endpoint to scrape:
   # Here it's Prometheus itself.
   scrape_configs:
     # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
     - job_name: 'prometheus'
       static_configs:
       # 9090 is the prometheus server port
       - targets: ['localhost:9090']
     - job_name: 'broker'
       file_sd_configs:
       - files:
         - 'targets.json'
   ```

1. Use the [ListNodes](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn-nodes.html#ListNodes) operation to get a list of your cluster's brokers\.

1. Create a file named `targets.json` with the following JSON\. Replace *broker\_dns\_1*, *broker\_dns\_2*, and the rest of the broker DNS names with the DNS names you obtained for your brokers in the previous step\. Include all of the brokers you obtained in the previous step\. Amazon MSK uses port 11001 for the JMX Exporter and port 11002 for the Node Exporter\.

   ```
   [
     {
       "labels": {
         "job": "jmx"
       },
       "targets": [
         "broker_dns_1:11001",
         "broker_dns_2:11001",
         .
         .
         .
         "broker_dns_N:11001"
       ]
     },
     {
       "labels": {
         "job": "node"
       },
       "targets": [
         "broker_dns_1:11002",
         "broker_dns_2:11002",
         .
         .
         .
         "broker_dns_N:11002"
       ]
     }
   ]
   ```

1. To start the Prometheus server on your Amazon EC2 instance, run the following command in the directory where you extracted the Prometheus files and saved `prometheus.yml` and `targets.json`\.

   ```
   ./prometheus
   ```

1. Find the IPv4 public IP address of the Amazon EC2 instance where you ran Prometheus in the previous step\. You need this public IP address in the following step\.

1. To access the Prometheus web UI, open a browser that can access your Amazon EC2 instance, and go to `Prometheus-Instance-Public-IP:9090`, where *Prometheus\-Instance\-Public\-IP* is the public IP address you got in the previous step\.

## Prometheus metrics<a name="prometheus-metrics"></a>

All metrics emitted by Apache Kafka to JMX are accessible using open monitoring with Prometheus\. For information about Apache Kafka metrics, see [Monitoring](https://kafka.apache.org/documentation/#monitoring) in the Apache Kafka documentation\. Along with Apache Kafka metrics, consumer\-lag metrics are also available at port 11001 under the JMX MBean name `kafka.consumer.group:type=ConsumerLagMetrics`\. You can also use the Prometheus Node Exporter to get CPU and disk metrics for your brokers at port 11002\.

## Storing Prometheus metrics in Amazon Managed Service for Prometheus<a name="managed-service-prometheus"></a>

Amazon Managed Service for Prometheus is a Prometheus\-compatible monitoring and alerting service that you can use to monitor Amazon MSK clusters\. It is a fully\-managed service that automatically scales the ingestion, storage, querying, and alerting of your metrics\. It also integrates with AWS security services to give you fast and secure access to your data\. You can use the open\-source PromQL query language to query your metrics and alert on them\.

For more information, see [Getting started with Amazon Managed Service for Prometheus](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-getting-started.html)\.
