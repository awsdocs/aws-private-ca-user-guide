# Quotas<a name="PcaLimits"></a>

AWS Private CA assigns quotas to your allowed number of certificates and certificate authorities\. Request rates for API actions are also subject to quotas\. AWS Private CA quotas are specific to an AWS account and Region\. 

AWS Private CA throttles API requests at different rates depending on the API operation\. Throttling means that AWS Private CA rejects an otherwise valid request because the request exceeds the operation's quota for the number of requests per second\. When a request is throttled, AWS Private CA returns a [ThrottlingException](https://docs.aws.amazon.com/privateca/latest/APIReference/CommonErrors.html) error\. AWS Private CA does not guarantee a minimum request rate for APIs\. 

To see what quotas can be adjusted, see the [AWS Private CA quotas table](https://docs.aws.amazon.com/general/latest/gr/acm-pca.html#limits_pca) in the *AWS General Reference Guide*\.

You can view your current quotas and request quota increases using AWS Service Quotas\.

**To see an up\-to\-date list of your AWS Private CA quotas**

1. Log into your AWS account\.

1. Open the Service Quotas console at [https://console\.aws\.amazon\.com/servicequotas/](https://console.aws.amazon.com/servicequotas/)\.

1. In the **Services** list, choose **AWS Certificate Manager Private Certificate Authority \(ACM PCA\)**\. Each quota in the** Service quotas** list shows your currently applied quota value, the default quota value, and whether or not the quota is adjustable\. Choose the name of a quota for more information about it\.

**To request a quota increase**

1. In the **Service quotas** list, choose the radio button for an adjustable quota\. 

1. Choose the **Request quota increase** button\.

1. Complete and submit the **Request quota increase** form\.

AWS Private CA is integrated with AWS Certificate Manager\. You can use the ACM console, AWS CLI, or ACM API to request private certificates from an existing private CA\. These private PKI certificates, which are managed by ACM, are subject both to PCA quotas and to the quotas that ACM places on public and imported certificates\. For more information about ACM requirements, see [Request a Private Certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-private.html) and [Quotas](https://docs.aws.amazon.com/acm/latest/userguide/acm-limits.html) in the AWS Certificate Manager User Guide\. 