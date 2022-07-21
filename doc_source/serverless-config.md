# Configuration for serverless clusters<a name="serverless-config"></a>

Amazon MSK sets broker configuration properties for serverless clusters\. You can't change these broker configuration property settings\. However, you can set the following topic configuration properties\.


****  

| Configuration property | Default | Editable | Maximum allowed value | 
| --- | --- | --- | --- | 
| [cleanup\.policy](https://kafka.apache.org/documentation/#topicconfigs_cleanup.policy) | Delete | Yes, but only at topic creation time |  | 
|  [compression\.type](https://kafka.apache.org/documentation/#topicconfigs_compression.type)  | Producer | Yes |  | 
|  [max\.message\.bytes](https://kafka.apache.org/documentation/#topicconfigs_max.message.bytes)  | 1048588 | Yes | 8 MiB | 
|  [message\.timestamp\.difference\.max\.ms](https://kafka.apache.org/documentation/#topicconfigs_message.timestamp.difference.max.ms)  | long\.max | Yes |  | 
|  [message\.timestamp\.type](https://kafka.apache.org/documentation/#topicconfigs_message.timestamp.type)  | CreateTime | Yes |  | 
|  [retention\.bytes](https://kafka.apache.org/documentation/#topicconfigs_retention.bytes)  | 250 GiB | Yes | 250 GiB | 
|  [retention\.ms](https://kafka.apache.org/documentation/#topicconfigs_retention.ms)  | 1 day | Yes | 1 day | 

You can also use Apache Kafka commands to set or modify topic\-level configuration properties for new or existing topics\. For more information about topic\-level configuration properties and examples of how to set them, see [Topic\-Level Configs](https://kafka.apache.org/documentation/#topicconfigs) in the official Apache Kafka documentation\.