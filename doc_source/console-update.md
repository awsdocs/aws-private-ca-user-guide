# Updating a CA \(console\)<a name="console-update"></a>

The following procedures show how to update existing CA configurations using the AWS Management Console\.

## Update CA status \(console\)<a name="console-update-status-steps"></a>

In this example, the status of an enabled CA is changed to disabled\.

**To update the status of a CA**

1. Sign in to your AWS account and open the AWS Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)

1. On the **Private certificate authorities** page, choose your private CA from the list\.

1. On the **Actions** menu, choose **Disable** to disable a private CA that is currently active or choose **Enable** to set the CA status to active\. 

## Updating a CA's revocation configuration \(console\)<a name="update-revocation"></a>

You can update the [revocation configuration](revocation-setup.md) for your private CA, for example, by adding or removing either OCSP or CRL support, or by modifying their settings\.

**Note**  
Changes to the revocation configuration of a CA do not affect certificates that were already issued\. For managed revocation to work, older certificates must be re\-issued\.

For OCSP, you change the following settings:
+ Enable or disable OCSP\.
+ Enable or disable a custom OCSP fully qualified domain name \(FQDN\)\.
+ Change the FQDN\.

For a CRL, you can change any of the following settings:
+ Whether the private CA generates a certificate revocation list \(CRL\)
+ The number of days before a CRL expires\. Note that AWS Private CA begins trying to regenerate the CRL at ½ the number of days you specify\. 
+ The name of the Amazon S3 bucket where your CRL is saved\.
+ An alias to hide the name of your S3 bucket from public view\.

**Important**  
Changing any of the preceding parameters can have negative effects\. Examples include disabling CRL generation, changing the validity period, or changing the S3 bucket after you have placed your private CA in production\. Such changes can break existing certificates that depend on the CRL and the current CRL configuration\. Changing the alias can be done safely as long as the old alias remains linked to the correct bucket\. 

**To update the revocation settings**

1. Sign in to your AWS account and open the AWS Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. On the **Private certificate authorities** page, choose your private CA from the list\.

1. On the **Actions** menu, choose **Update CA revocation**\.

1. The **Update CA revocation configuration** page presents two options for sharing revocation status with clients that use your certificates using Online Certificate Status Protocol \(OCSP\), or configuring a certificate revocation list \(CRL\), displayed as follows:
   + **CRL distribution**
   + **OCSP**

   You can configure either, neither, or both of these revocation mechanisms for your CA\. Although optional, managed revocation is recommended as a [best practice](ca-best-practices.md)\. Before completing this step, see [Setting up a certificate revocation method](revocation-setup.md) for information about the advantages of each method, the preliminary setup that may be required, and additional revocation features\.

### To configure a CRL<a name="collapsible-section-4"></a>

1. Select **CRL distribution**\.

1. To create an Amazon S3 bucket for your CRL entries, choose **Yes** for the **Create a new S3 bucket** option and type a unique bucket name\. \(You do not need to include the path to the bucket\.\) Otherwise, choose **No** and choose an existing bucket from the list\. 

   If you choose **Yes**, AWS Private CA creates and attaches the [required access policy](crl-planning.md#s3-policies) to your bucket\. If you choose **No**, you must attach a policy to your bucket before you can begin generating CRLs\. Use one of the policy patterns described in [Access policies for CRLs in Amazon S3 ](crl-planning.md#s3-policies)\. For information about attaching a policy, see [How Do I Add an S3 Bucket Policy?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)
**Note**  
When you are using the AWS Private CA console, an attempt to create a CA fails if both of the following conditions apply:  
You are enforcing Block Public Access settings on your S3 bucket or account\.
You asked AWS Private CA to create an S3 bucket automatically\.
In this situation, the console attempts, by default, to create a publicly accessible bucket, and S3 rejects this action\. Check your Amazon S3 settings if this occurs\. For more information, see [Blocking public access to your Amazon S3 storage](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html)\.

1. Expand **Advanced** for additional configuration options\.
   + Add a **Custom CRL Name** to create an alias for your Amazon S3 bucket\. This name is contained in certificates issued by the CA in the “CRL Distribution Points" extension that is defined by RFC 5280\.
   + Type the number of days your CRL will remain valid\. The default value is 7 days\. For online CRLs, a validity period of 2\-7 days is common\. AWS Private CA tries to regenerate the CRL at the midpoint of the specified period\. 

1. Choose **Update** when done\.

### To configure OCSP<a name="collapsible-section-3"></a>

1. On the** Certificate revocation** page, choose **OCSP**\.

1. \(Optional\) In the **Custom OCSP name** field, provide a fully qualified domain name \(FQDN\) for your OCSP endpoint\.

When you provide an FQDN in this field, AWS Private CA inserts the FQDN into the *Authority Information Access* extension of each issued certificate in place of the default URL for the AWS OCSP responder\. When an endpoint receives a certificate containing the custom FQDN, it queries that address for an OCSP response\. For this mechanism to work, you need to take two additional actions:
   + Use a proxy server to forward traffic that arrives at your custom FQDN to the AWS OCSP responder\.
   + Add a corresponding CNAME record to your DNS database\. 
**Tip**  
For more information about implementing a complete OCSP solution using a custom CNAME, see [Configuring a Custom URL for AWS Private CA OCSP](ocsp-customize.md)\.

   For example, here is a CNAME record for customized OCSP as it would appear in Amazon Route 53\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/privateca/latest/userguide/console-update.html)
**Note**  
The value of the CNAME must not include a protocol prefix such as "http://" or "https://"\.

1. Choose **Update** when done\.