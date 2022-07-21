# Using LinkedIn's Cruise Control for Apache Kafka with Amazon MSK<a name="cruise-control"></a>

You can use LinkedIn's Cruise Control to rebalance your Amazon MSK cluster, detect and fix anomalies, and monitor the state and health of the cluster\.

**To download and build Cruise Control**

1. Create an Amazon EC2 instance in the same Amazon VPC as the Amazon MSK cluster\.

1. Install Prometheus on the Amazon EC2 instance that you created in the previous step\. Note the private IP and the port\. The default port number is 9090\. For information on how to configure Prometheus to aggregate metrics for your cluster, see [Open monitoring with Prometheus](open-monitoring.md)\.

1. Download [Cruise Control](https://github.com/linkedin/cruise-control/releases) on the Amazon EC2 instance\. \(Alternatively, you can use a separate Amazon EC2 instance for Cruise Control if you prefer\.\) For a cluster that has Apache Kafka version 2\.4\.\*, use the latest 2\.4\.\* Cruise Control release\. If your cluster has an Apache Kafka version that is older than 2\.4\.\*, use the latest 2\.0\.\* Cruise Control release\.

1. Decompress the Cruise Control file, then go to the decompressed folder\.

1. Run the following command to install git\.

   ```
   sudo yum -y install git
   ```

1. Run the following command to initialize the local repo\. Replace *Your\-Cruise\-Control\-Folder* with the name of your current folder \(the folder that you obtained when you decompressed the Cruise Control download\)\.

   ```
   git init && git add . && git commit -m "Init local repo." && git tag -a Your-Cruise-Control-Folder -m "Init local version."
   ```

1. Run the following command to build the source code\.

   ```
   ./gradlew jar copyDependantLibs
   ```

**To configure and run Cruise Control**

1. Make the following updates to the `config/cruisecontrol.properties` file\. Replace the example bootstrap servers and Apache ZooKeeper connection string with the values for your cluster\. To get these strings for your cluster, you can see the cluster details in the console\. Alternatively, you can use the [GetBootstrapBrokers](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn-bootstrap-brokers.html#GetBootstrapBrokers) and [DescribeCluster](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster) API operations or their CLI equivalents\.

   ```
   # If using TLS encryption, use 9094; use 9092 if using plaintext
   bootstrap.servers=b-1.test-cluster.2skv42.c1.kafka.us-east-1.amazonaws.com:9094,b-2.test-cluster.2skv42.c1.kafka.us-east-1.amazonaws.com:9094,b-3.test-cluster.2skv42.c1.kafka.us-east-1.amazonaws.com:9094
   zookeeper.connect=z-1.test-cluster.2skv42.c1.kafka.us-east-1.amazonaws.com:2181,z-2.test-cluster.2skv42.c1.kafka.us-east-1.amazonaws.com:2181,z-3.test-cluster.2skv42.c1.kafka.us-east-1.amazonaws.com:2181
       
   # SSL properties, needed if cluster is using TLS encryption
   security.protocol=SSL
   ssl.truststore.location=/home/ec2-user/kafka.client.truststore.jks
       
   # Use the Prometheus Metric Sampler
   metric.sampler.class=com.linkedin.kafka.cruisecontrol.monitor.sampling.prometheus.PrometheusMetricSampler
       
   # Prometheus Metric Sampler specific configuration
   prometheus.server.endpoint=1.2.3.4:9090 # Replace with your Prometheus IP and port
       
   # Change the capacity config file and specify its path; details below
   capacity.config.file=config/capacityCores.json
   ```

1. Edit the `config/capacityCores.json` file to specify the right disk size and CPU cores and network in/out limits\. You can use the [DescribeCluster](https://docs.aws.amazon.com//msk/1.0/apireference/clusters-clusterarn.html#DescribeCluster) API operation \(or its CLI equivalent\) to obtain the disk size\. For CPU cores and network in/out limits, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

   ```
   {
     "brokerCapacities": [
       {
         "brokerId": "-1",
         "capacity": {
           "DISK": "10000",
           "CPU": {
             "num.cores": "2"
           },
           "NW_IN": "5000000",
           "NW_OUT": "5000000"
         },
         "doc": "This is the default capacity. Capacity unit used for disk is in MB, cpu is in number of cores, network throughput is in KB."
       }
     ]
   }
   ```

1. You can optionally install the Cruise Control UI\. To download it, go to [Setting Up Cruise Control Frontend](https://github.com/linkedin/cruise-control-ui/wiki/Single-Kafka-Cluster#setting-up-cruise-control-frontend)\.

1. Run the following command to start Cruise Control\. Consider using a tool like `screen` or `tmux` to keep a long\-running session open\.

   ```
   <path-to-your-kafka-installation>/bin/kafka-cruise-control-start.sh config/cruisecontrol.properties 9091
   ```

1. Use the Cruise Control APIs or the UI to make sure that Cruise Control has the cluster load data and that it's making rebalancing suggestions\. It might take several minutes to get a valid window of metrics\.