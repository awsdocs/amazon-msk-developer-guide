# Troubleshooting Amazon MSK Identity and Access<a name="security_iam_troubleshoot"></a>

Use the following information to help you diagnose and fix common issues that you might encounter when working with Amazon MSK and IAM\.

**Topics**
+ [I Am Not Authorized to Perform an Action in Amazon MSK](#security_iam_troubleshoot-no-permissions)

## I Am Not Authorized to Perform an Action in Amazon MSK<a name="security_iam_troubleshoot-no-permissions"></a>

If the AWS Management Console tells you that you're not authorized to perform an action, then you must contact your administrator for assistance\. Your administrator is the person that provided you with your user name and password\.

The following example error occurs when the `mateojackson` IAM user tries to use the console to delete a cluster but does not have `kafka:DeleteCluster` permissions\.

```
User: arn:aws:iam::123456789012:user/mateojackson is not authorized to perform: kafka:DeleteCluster on resource: purchaseQueriesCluster
```

In this case, Mateo asks his administrator to update his policies to allow him to access the `purchaseQueriesCluster` resource using the `kafka:DeleteCluster` action\.