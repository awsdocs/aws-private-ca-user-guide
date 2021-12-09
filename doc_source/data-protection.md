# Data protection in AWS Certificate Manager Private Certificate Authority<a name="data-protection"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in AWS Certificate Manager Private Certificate Authority\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\)\. That way each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put confidential or sensitive information, such as your customers' email addresses, into tags or free\-form fields such as a **Name** field\. This includes when you work with ACM Private CA or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into tags or free\-form fields used for names may be used for billing or diagnostic logs\. If you provide a URL to an external server, we strongly recommend that you do not include credentials information in the URL to validate your request to that server\.

## Storage and security compliance of ACM Private CA private keys<a name="private-keys"></a>

The private keys for private CAs are stored in AWS managed hardware security modules \(HSMs\)\. The HSMs comply with FIPS PUB 140\-2 Security Requirements for Cryptographic Modules\. The level of FIPS security compliance level varies by Region, as follows:


****  

| Region name | Region | FIPS PUB 140\-2 Level 2 \(or higher\) | FIPS PUB 140\-2 Level 3 \(or higher\) | 
| --- | --- | --- | --- | 
|  US East \(Ohio\)  |  us\-east\-2  |    | ✓ | 
|  US East \(N\. Virginia\)  |  us\-east\-1  |    |  ✓  | 
|  US West \(N\. California\)  |  us\-west\-1  |    |  ✓  | 
|  US West \(Oregon\)  |  us\-west\-2  |    |  ✓  | 
|  Africa \(Cape Town\)  |  af\-south\-1  |    |  ✓  | 
|  Asia Pacific \(Hong Kong\)  |  ap\-east\-1  |    | ✓ | 
|  Asia Pacific \(Mumbai\)  |  ap\-south\-1  |    |  ✓  | 
|  Asia Pacific \(Osaka\)  |  ap\-northeast\-3  |  ✓  |    | 
|  Asia Pacific \(Seoul\)  |  ap\-northeast\-2  |    |  ✓  | 
|  Asia Pacific \(Singapore\)  |  ap\-southeast\-1  |    |  ✓  | 
|  Asia Pacific \(Sydney\)  |  ap\-southeast\-2  |    | ✓ | 
|  Asia Pacific \(Tokyo\)  |  ap\-northeast\-1  |    |  ✓  | 
|  Canada \(Central\)  |  ca\-central\-1  |    |  ✓  | 
|  Europe \(Frankfurt\)  |  eu\-central\-1  |    |  ✓  | 
|  Europe \(Ireland\)  |  eu\-west\-1  |    |  ✓  | 
|  Europe \(London\)  |  eu\-west\-2  |    | ✓ | 
|  Europe \(Milan\)  |  eu\-south\-1  |    |  ✓  | 
|  Europe \(Paris\)  |  eu\-west\-3  |    |  ✓  | 
|  Europe \(Stockholm\)  |  eu\-north\-1  |    |  ✓  | 
|  Middle East \(Bahrain\)  |  me\-south\-1  |    |  ✓  | 
|  South America \(São Paulo\)  |  sa\-east\-1  |    | ✓ | 
|  AWS GovCloud \(US\-East\)  |  us\-gov\-east\-1  |    |  ✓  | 
|  AWS GovCloud \(US\-West\)  |  us\-gov\-west\-1  |    |  ✓  | 