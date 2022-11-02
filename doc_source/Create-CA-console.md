# Procedure for creating a CA \(console\)<a name="Create-CA-console"></a>

Complete the following steps to create a private CA using the AWS Management Console\.

**To get started using the console**

Sign in to your AWS account and open the AWS Private CA console at **[https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)**\. 
+ If you are opening the console in a Region where you have no private CAs, the introductory page appears\. Choose **Create a private CA**\. 
+ If you are opening the console in a Region where you have already created a CA, the **Private certificate authorities** page opens with a list of your CAs\. Choose **Create CA**\.

## CA type options<a name="PcaCreateCaType"></a>

On the **Type options** section of the console, choose the type of private certificate authority that you want to create\. 
+ Choosing **Root** establishes a new CA hierarchy\. This CA is backed by a self\-signed certificate\. It serves as the ultimate signing authority for other CAs and end\-entity certificates in the hierarchy\.
+ Choosing **Subordinate** creates a CA that must be signed by a parent CA above it in the hierarchy\. Subordinate CAs are typically used to create other subordinate CAs or to issue end\-entity certificates to users, computers, and applications\. 
**Note**  
AWS Private CA provides an automated signing process when your subordinate CA's parent CA is also hosted by AWS Private CA\. All you do is choose the parent CA to use\.  
Your subordinate CA might need to be signed by an external trust services provider\. If so, AWS Private CA provides you with a certificate signing request \(CSR\) that you must download and use to obtain a signed CA certificate\. For more information, see [Installing a subordinate CA certificate signed by an external parent CA](PCACertInstall.md#InstallSubordinateExternal)\.

## Subject distinguished name options<a name="PcaCreateCaName"></a>

Under **Subject distinguished name options**, configure the subject name of your private CA\. You must enter a value for at least one of the following options:
+ **Organization \(O\)** – For example, a company name
+ **Organization Unit \(OU\)** – For example, a division within a company
+ **Country name \(C\)** – A two\-letter country code
+ **State or province name** – Full name of a state or province
+ **Locality name** – The name of a city
+ **Common Name \(CN\)** – A fully qualified domain name \(FQDN\)

Because the backing certificate is self\-signed, the subject information that you provide for a private CA is probably more sparse than what a public CA would contain\. For more information about each of the values that make up a subject distinguished name, see [RFC 5280](https://datatracker.ietf.org/doc/html/rfc5280#section-4.1.2.4)\.

## Key algorithm options<a name="PcaCreateKeyAlg"></a>

Under **Key algorithm options**, choose the key algorithm and the bit\-size of the key\. The default value is an RSA algorithm with a 2048\-bit key length\. You can choose from the following algorithms: 
+ RSA 2048
+ RSA 4096
+ ECDSA P256
+ ECDSA P384

## Certificate revocation options<a name="PcaCreateRevocation"></a>

Under **Certificate revocation options**, you can select from two methods of sharing revocation status with clients that use your certificates:
+ **CRL distribution**
+ **OCSP**

You can configure either, neither, or both of these revocation options for your CA\. Although optional, managed revocation is recommended as a [best practice](ca-best-practices.md)\. Before completing this step, see [Setting up a certificate revocation method](revocation-setup.md) for information about the advantages of each method, the preliminary setup that might be required, and additional revocation features\.

**Note**  
If you create your CA without configuring revocation, you can always configure it later\. For more information, see [Updating your private CA](PCAUpdateCA.md)\. 

### To configure a CRL<a name="collapsible-section-2"></a>

1. On the **Configure revocation * \- optional*** page, choose **CRL distribution**\.

1. To create an Amazon S3 bucket for your CRL entries, choose **Yes** for the **Create a new S3 bucket** option and type a unique bucket name\. \(You do not need to include the path to the bucket\.\) Otherwise, choose **No** and choose an existing bucket from the list\. 

   If you choose **Yes**, AWS Private CA creates and attaches the [required access policy](crl-planning.md#s3-policies) to your bucket\. If you choose **No**, you must attach a policy to your bucket before you can begin generating CRLs\. Use one of the policy patterns described in [Access policies for CRLs in Amazon S3 ](crl-planning.md#s3-policies)\. For more information, see [Adding a bucket policy using the Amazon S3 console\.](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)
**Note**  
When you are using the AWS Private CA console, an attempt to create a CA fails if both the following conditions apply:  
You are enforcing Block Public Access settings on your S3 bucket or account\.
You asked AWS Private CA to create an S3 bucket automatically\.
In this situation, the console attempts, by default, to create a publicly accessible bucket, and Amazon S3 rejects this action\. Check your Amazon S3 settings if this occurs\. For more information, see [Blocking public access to your Amazon S3 storage](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html)\.

1. Expand **Advanced** for additional configuration options\.
   + Add a **Custom CRL Name** to create an alias for your Amazon S3 bucket\. This name is contained in certificates issued by the CA in the “CRL Distribution Points" extension that is defined by RFC 5280\.
   + Type the number of days your CRL will remain valid\. The default value is 7 days\. For online CRLs, a validity period of 2\-7 days is common\. AWS Private CA tries to regenerate the CRL at the midpoint of the specified period\. 

### To configure OCSP<a name="collapsible-section-1"></a>

1. On the **Configure revocation * \- optional*** page, choose **OCSP**\.

1. In the **Custom OCSP endpoint * \- optional*** field, you can provide a fully qualified domain name \(FQDN\) for a non\-Amazon OCSP endpoint\.

When you provide an FQDN in this field, AWS Private CA inserts the FQDN into the *Authority Information Access* extension of each issued certificate in place of the default URL for the AWS OCSP responder\. When an endpoint receives a certificate containing the custom FQDN, it queries that address for an OCSP response\. For this mechanism to work, you need to take two additional actions:
   + Use a proxy server to forward traffic that arrives at your custom FQDN to the AWS OCSP responder\.
   + Add a corresponding CNAME record to your DNS database\. 
**Tip**  
For more information about implementing a complete OCSP solution using a custom CNAME, see [Configuring a Custom URL for AWS Private CA OCSP](ocsp-customize.md)\.

   For example, here is a CNAME record for customized OCSP as it would appear in Amazon Route 53\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/privateca/latest/userguide/Create-CA-console.html)
**Note**  
The value of the CNAME must not include a protocol prefix such as "http://" or "https://"\.

## Tags<a name="PcaCreateAddTags"></a>

Under **Tags**, you can optionally tag your CA\. Tags are key\-value pairs that serve as metadata for identifying and organizing AWS resources\. For a list of AWS Private CA tag parameters and for instructions on how to add tags to CAs after creation, see [Managing tags for your private CA](PcaCaTagging.md)\.

**Note**  
To attach tags to a private CA during the creation procedure, a CA administrator must first associate an inline IAM policy with the `CreateCertificateAuthority` action and explicitly allow tagging\. For more information, see [Attaching tags to a CA at the time of creation](auth-InlinePolicies.md#policy-tag-ca)\.

## CA permissions options<a name="PcaCreateAcmPerms"></a>

Under **CA permissions options**, you can optionally delegate automatic renewal permissions to the ACM service principal\. ACM can only automatically renew private end\-entity certificates generated by this CA if this permission is granted\. You can assign renewal permissions at any time with the AWS Private CA [CreatePermission](https://docs.aws.amazon.com/privateca/latest/APIReference/API_CreatePermission.html) API or [create\-permission](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/create-permission.html) CLI command\.

The default is to enable these permissions\.

## Pricing<a name="PcaCreatePricing"></a>

Under **Pricing**, confirm that you understand the pricing for a private CA\. 

**Note**  
For the latest AWS Private CA pricing information, see [AWS Private Certificate Authority Pricing](https://aws.amazon.com/private-ca/pricing/)\. You can also use the [AWS pricing calculator](https://calculator.aws/#/createCalculator/certificateManager) to estimate costs\. 

## Create CA<a name="complete-create-ca"></a>

Choose **Confirm and create** after you have checked all of the entered information for accuracy\. The details page for the CA opens and displays its status as **Pending certificate**\. 

**Note**  
While on the details page, you can finish configuring your CA by choosing **Actions**, **Install CA certificate**, or you can return later to the **Private certificate authorities** list and complete the installation procedure that applies in your case:  
[Installing a root CA certificate](PCACertInstall.md#InstallRoot)
[Installing a subordinate CA certificate hosted by AWS Private CA](PCACertInstall.md#InstallSubordinateInternal)
[Installing a subordinate CA certificate signed by an external parent CA](PCACertInstall.md#InstallSubordinateExternal)