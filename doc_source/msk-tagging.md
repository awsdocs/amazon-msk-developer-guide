# Tagging an Amazon MSK cluster<a name="msk-tagging"></a>

You can assign your own metadata in the form of *tags* to an Amazon MSK resource, such as an MSK cluster\. A tag is a key\-value pair that you define for the resource\. Using tags is a simple yet powerful way to manage AWS resources and organize data, including billing data\. 

**Topics**
+ [Tag basics](#msk-tagging-basics)
+ [Tracking costs using tagging](#msk-tagging-billing)
+ [Tag restrictions](#msk-tagging-restrictions)
+ [Tagging resources using the Amazon MSK API](#msk-tagging-howto)

## Tag basics<a name="msk-tagging-basics"></a>

You can use the Amazon MSK API to complete the following tasks:
+ Add tags to an Amazon MSK resource\.
+ List the tags for an Amazon MSK resource\.
+ Remove tags from an Amazon MSK resource\.

You can use tags to categorize your Amazon MSK resources\. For example, you can categorize your Amazon MSK clusters by purpose, owner, or environment\. Because you define the key and value for each tag, you can create a custom set of categories to meet your specific needs\. For example, you might define a set of tags that help you track clusters by owner and associated application\. 

The following are several examples of tags:
+ `Project: Project name`
+ `Owner: Name`
+ `Purpose: Load testing` 
+ `Environment: Production` 

## Tracking costs using tagging<a name="msk-tagging-billing"></a>

You can use tags to categorize and track your AWS costs\. When you apply tags to your AWS resources, including Amazon MSK clusters, your AWS cost allocation report includes usage and costs aggregated by tags\. You can organize your costs across multiple services by applying tags that represent business categories \(such as cost centers, application names, or owners\)\. For more information, see [Use Cost Allocation Tags for Custom Billing Reports](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing User Guide*\.

## Tag restrictions<a name="msk-tagging-restrictions"></a>

The following restrictions apply to tags in Amazon MSK\.

**Basic restrictions**
+ The maximum number of tags per resource is 50\.
+ Tag keys and values are case\-sensitive\.
+ You can't change or edit tags for a deleted resource\.

**Tag key restrictions**
+ Each tag key must be unique\. If you add a tag with a key that's already in use, your new tag overwrites the existing key\-value pair\. 
+ You can't start a tag key with `aws:` because this prefix is reserved for use by AWS\. AWS creates tags that begin with this prefix on your behalf, but you can't edit or delete them\.
+ Tag keys must be between 1 and 128 Unicode characters in length\.
+ Tag keys must consist of the following characters: Unicode letters, digits, white space, and the following special characters: `_ . / = + - @`\.

**Tag value restrictions**
+ Tag values must be between 0 and 255 Unicode characters in length\.
+ Tag values can be blank\. Otherwise, they must consist of the following characters: Unicode letters, digits, white space, and any of the following special characters: `_ . / = + - @`\.

## Tagging resources using the Amazon MSK API<a name="msk-tagging-howto"></a>

You can use the following operations to tag or untag an Amazon MSK resource or to list the current set of tags for a resource:
+ [ListTagsForResource](https://docs.aws.amazon.com//msk/1.0/apireference/tags-resourcearn.html#ListTagsForResource)
+ [TagResource](https://docs.aws.amazon.com//msk/1.0/apireference/tags-resourcearn.html#TagResource)
+ [UntagResource](https://docs.aws.amazon.com//msk/1.0/apireference/tags-resourcearn.html#UntagResource)