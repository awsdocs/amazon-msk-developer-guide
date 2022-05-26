# Step 4: Create connector<a name="mkc-create-connector"></a>

**To create the connector**

1. Sign in to the AWS Management Console, and open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home?region=us\-east\-1\#/home/](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/)\.

1. In the left pane, expand **MSK Connect**, then choose **Connectors**\.

1. Choose **Create connector**\.

1. In the list of plugins, choose `mkc-tutorial-plugin`, then choose **Next**\.

1. For the connector name enter `mkc-tutorial-connector`\.

1. In the list of clusters, choose `mkc-tutorial-cluster`\.

1. Copy the following configuration and paste it into the connector configuration field\.

   ```
   connector.class=io.lenses.streamreactor.connect.aws.s3.sink.S3SinkConnector
   key.converter.schemas.enable=false
   connect.s3.kcql=INSERT INTO mkc-tutorial-destination-bucket:tutorial SELECT * FROM mkc-tutorial-topic
   aws.region=us-east-1
   tasks.max=2
   topics=mkc-tutorial-topic
   schema.enable=false
   value.converter=org.apache.kafka.connect.storage.StringConverter
   errors.log.enable=true
   key.converter=org.apache.kafka.connect.storage.StringConverter
   ```

1. Under **Access permissions** choose `mkc-tutorial-role`\.

1. Choose **Next**\. On the **Security** page, choose **Next** again\.

1. On the **Logs** page choose **Next**\.

1. Under **Review and create** choose **Create connector**\.

**Next Step**

[Step 5: Send data](mkc-send-data.md)