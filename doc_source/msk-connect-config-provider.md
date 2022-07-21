# Using a configuration provider to externalize secrets<a name="msk-connect-config-provider"></a>

To externalize sensitive configuration values with a service like AWS Secrets Manager, you can set up a configuration provider that implements the [ConfigProvider](https://kafka.apache.org/27/javadoc/org/apache/kafka/common/config/provider/ConfigProvider.html) class interface\. A configuration provider lets you specify variables instead of plaintext in a connector or worker configuration, and workers running in your connector resolve these variables at runtime\. This prevents credentials and other secrets from appearing as plaintext in a connector configuration\. 

You can develop your own configuration provider plugin or use a third\-party plugin\. For an example of setting up a third\-party configuration provider that integrates with AWS Secrets Manager, see the instructions for configuring an [Amazon Redshift sink connector with configuration provider](mkc-redshiftsink-connector-example.md)\.

To develop your own configuration provider plugin, use the following guidelines:
+ Implement the [ConfigProvider](https://kafka.apache.org/27/javadoc/org/apache/kafka/common/config/provider/ConfigProvider.html) interface, which is discovered using the Java ServiceLoader facility\.
+ Create a file named `META-INF/services/org.apache.kafka.common.config.provider.ConfigProvider` that contains the fully qualified name of your ConfigProvider implementation class\. Package this file into a JAR with your implementation classes\. For example, see `[org\.apache\.kafka\.common\.config\.provider\.ConfigProvider](https://github.com/jcustenborder/kafka-config-provider-aws/blob/master/src/main/resources/META-INF/services/org.apache.kafka.common.config.provider.ConfigProvider)` in the open\-source AWS Secrets Manager Config Provider plugin\.
+ Use the Kafka Connect enum constant [https://kafka.apache.org/27/javadoc/org/apache/kafka/common/config/ConfigDef.Type.html#PASSWORD](https://kafka.apache.org/27/javadoc/org/apache/kafka/common/config/ConfigDef.Type.html#PASSWORD) to define sensitive configuration values\. For more information, see [Preventing secrets from appearing in connector logs](msk-connect-logging.md#msk-connect-logging-secrets)\.

## Considerations<a name="msk-connect-config-providers-considerations"></a>

Consider the following when you use a configuration provider with Amazon MSK Connect\.
+ Sensitive configuration values can appear in connector logs if a plugin does not define those values as secret\. Kafka Connect treats undefined configuration values the same as any other plaintext value\. To learn more, see [Preventing secrets from appearing in connector logs](msk-connect-logging.md#msk-connect-logging-secrets)\.
+ By default, MSK Connect frequently restarts a connector when the connector uses a configuration provider\. To turn off this restart behavior, you can set the `config.action.reload` value to `none` in your connector configuration\.