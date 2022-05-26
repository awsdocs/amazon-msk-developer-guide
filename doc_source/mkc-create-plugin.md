# Step 2: Create custom plugin<a name="mkc-create-plugin"></a>

A plugin contains the code that defines the logic of the connector\. In this step you create a custom plugin that has the code for the Lenses Amazon S3 Sink Connector\. In a later step, when you create the MSK connector, you specify that its code is in this custom plugin\. You can use the same plugin to create multiple MSK connectors with different configurations\.

**To create the custom plugin**

1. Download the [Lenses Amazon S3 Sink Connector](https://github.com/lensesio/stream-reactor/releases/download/2.1.3/kafka-connect-aws-s3-2.1.3.1-2.5.0-all.jar) JAR\.

1. Compress the downloaded JAR file to turn it into a ZIP file\.

1. Upload the ZIP file to an S3 bucket to which you have access\. For information on how to upload files to Amazon S3, see [Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html) in the Amazon S3 user guide\.

1. Open the Amazon MSK console at [https://console.aws.amazon.com/msk/](https://console.aws.amazon.com/msk/)\.

1. In the left pane expand **MSK Connect**, then choose **Custom plugins**\.

1. Choose **Create custom plugin**\.

1. Choose **Browse S3**\.

1. In the list of buckets find the bucket where you uploaded the ZIP file, and choose that bucket\.

1. In the list of objects in the bucket, select the radio button to the left of the ZIP file, then choose the button labeled **Choose**\.

1. Enter `mkc-tutorial-plugin` for the custom plugin name, then choose **Create custom plugin**\.

It might take AWS a few minutes to finish creating the custom plugin\. When the creation process is complete, you see the following message in a banner at the top of the browser window\.

```
Custom plugin mkc-tutorial-plugin was successfully created
The custom plugin was created. You can now create a connector using this custom plugin.
```

**Next Step**

[Step 3: Create client machine and Apache Kafka topic](mkc-create-topic.md)