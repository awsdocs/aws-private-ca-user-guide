# Add Tags to your Private Certificate Authority<a name="PcaCaTagging"></a>

A *tag* is a label that you can assign to a private CA\. Each tag consists of a *key* and a *value*\. You can use the ACM PCA console, AWS Command Line Interface \(AWS CLI\), or the PCA API to add, view, or remove tags for private CAs\. 

You can create custom tags that suit your needs\. For example, you could tag private CAs with an `Environment=Prod` or `Environment=Beta` tag to identify which environment the CA is intended for\. 

Other AWS resources also support tagging\. You can, therefore, assign the same tag to different resources to indicate whether those resources are related\. For example, you can assign a tag such as `Website=example.com` to your CA, the Elastic Load Balancing load balancer, and other related resources\. 

The following basic restrictions apply to ACM PCA tags:
+ The maximum number of tags per private CA is 50\.
+ The maximum length of a tag key is 127 characters\.
+ The maximum length of a tag value is 255 characters\.
+ Tag keys and values are case sensitive\.
+ The `aws:` prefix is reserved for AWS use; you cannot add, edit, or delete tags whose key begins with `aws:`\. Tags that begin with `aws:` do not count against your tags\-per\-resource limit\.
+ If you plan to use your tagging schema across multiple services and resources, remember that other services may have other restrictions for allowed characters\. Refer to the documentation for that service\. 
+ ACM PCA tags are not available for use in the AWS Management Console's [Resource Groups and Tag Editor](https://aws.amazon.com/blogs/aws/resource-groups-and-tagging/)\. 

You can tag a private CA from the AWS Management Console or AWS CLI:
+ [Tagging a private CA \(console\)](#TagConsole)
+ [Tagging a private CA \(AWS CLI\)](#TagCli)

**To tag a private CA \(console\)**

1. Sign in to your AWS account and open the ACM PCA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Choose **Private CAs**\.

1. Choose your private CA from the list\.

1. Choose the **Tags** tab\.

1. Choose **Edit**\.

1. Type a key and value pair\.

1. Choose **Add Tag**\.

**To tag a private CA \(AWS CLI\)**  
Use the [tag\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/tag-certificate-authority.html) command to add tags to your private CA\. 

```
aws acm-pca tag-certificate-authority \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--tags Key=Admin,Value=Alice
```

Use the [list\-tags](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/list-tags.html) command to list the tags for a private CA\. 

```
aws acm-pca list-tags \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/123455678-1234-1234-1234-123456789012 \
--max-results 10
```

Use the [untag\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/untag-certificate-authority.html) command to remove tags from a private CA\. 

```
aws acm-pca untag-certificate-authority \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--tags Key=Purpose,Value=Website
```