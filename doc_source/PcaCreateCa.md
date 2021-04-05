# Creating a Private CA and Revocation<a name="PcaCreateCa"></a>

This section describes how to use ACM Private CA to create a private certificate authority \(CA\)\. It also explains the option to add a revocation support through Online Certificate Status Protocol \(OCSP\) or a certificate revocation list \(CRL\)\. You can use these procedures to create both root CAs and subordinate CAs, resulting in an auditable hierarchy of trust relationships that matches your organizational needs\.

**Note**  
You have the option of encrypting your CRL\. For more information, see [Encrypting Your CRLs ](crl-ref.md#crl-encryption)\.

You can create a CA using the AWS Management Console, the PCA portion of the AWS CLI, or AWS CloudFormation\.

For information about using a CA to sign end\-entity certificates for your users, devices, and applications, see [Issuing a Private End\-Entity Certificate](PcaIssueCert.md)\.

**Note**  
Your account is charged a monthly price for each private CA starting from the time that you create it\.

**For information about updating the configuration of a CA that you have already created, see [Updating Your Private CA](PCAUpdateCA.md)\.**
+ [Creating a CA \(Console\)](#CA-procedures)
+ [Using the AWS CLI to Create a CA](#Create-CA-CLI)
+ [Using AWS CloudFormation to Create a CA](#Create-CA-CFN)

## Creating a CA \(Console\)<a name="CA-procedures"></a>

To get started using the console, sign in to your AWS account and open the ACM Private CA console at **[https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)**\. The introductory page appears if your console opens to a Region where you have no CA\. Choose **Get started** beneath **Private certificate authority**\. Choose **Get started** again\. If the console opens to a Region in which you already have one or more CAs, the introductory page does not appear\. Choose **Private CAs** and then choose **Create CA**\. 

**To create a private CA \(console\)**

1. <a name="PcaCreateCaType"></a>

**Select CA type**

   On the **Select the certificate authority \(CA\) type** page, choose the type of the private certificate authority that you want to create\. 
   + Choosing **Root CA** establishes a new CA hierarchy\. This CA is backed by a self\-signed certificate\. It serves as the ultimate signing authority for other CAs and end\-entity certificates in the hierarchy\.
   + Choosing **Subordinate CA** creates a CA that must be signed by a parent CA above it in the hierarchy\. Subordinate CAs are typically be used to create other subordinate CAs or to issue end\-entity certificates to users, computers, and applications\. 
**Note**  
ACM Private CA provides an automated signing process when your subordinate CA's parent CA is also hosted by ACM Private CA\. All you do is choose the parent CA to use\.  
Your subordinate CA might need to be signed by an external signing authority\. If so, ACM Private CA provides you with a certificate signing request \(CSR\) that you must download and use to obtain a signed CA certificate\. For more information, see [Installing a Subordinate CA Certificate Signed by an External Parent CA](PCACertInstall.md#InstallSubordinateExternal)\.

   After choosing a CA type, choose **Next**\.

1. <a name="PcaCreateCaName"></a>

**Configure CA subject name**

   On the **Configure the certificate authority \(CA\) subject name**, configure the subject name of your private CA\. You must enter at least one of the following values:
   + **Organization \(O\)**
   + **Organization Unit \(OU\)**
   + **Country name \(C\)**
   + **State or province name**
   + **Locality name**
   + **Common Name \(CN\)**

   Because the backing certificate is self\-signed, the subject information that you provide for a private CA is probably sparser than what a public CA would contain\. For more information about each of the values that make up a subject distinguished name, see [X\.500 Distinguished Name](PcaTerms.md#terms-x500dn)\.

    When done, choose **Next**\.

1. <a name="PcaCreateKeyAlg"></a>

**Configure CA key algorithm**

   On the **Configure the certificate authority \(CA\) key algorithm** page, choose the key algorithm and the bit\-size of the key\. The default value is an RSA algorithm with a 2048\-bit key length\. If you expand the **Advanced** options, you can choose from the following algorithms: 
   + RSA 2048
   + RSA 4096
   + ECDSA P256
   + ECDSA P384

   Make a selection and then choose **Next**\. 

1. <a name="PcaCreateRevocation"></a>

**\(Optional\) Configure revocation**
**Important**  
You might need to revoke certificates that your CA has signed\. For example, revocation might be necessary when a certificate has been issued improperly or if its secret key has been compromised\. As a best practice, you should configure your PKI to validate certificate revocation status when your clients attempt a secure connection\. 

    The **Configure certificate revocation** page presents two mechanisms for sharing revocation status with clients that use your certificates: 
   + **Online Certificate Status Protocol \(OCSP\)**
**Note**  
OCSP is not currently supported in the ACM Private CA console\. Beta OCSP users can find AWS CLI examples at [Using the AWS CLI to Create a CA](#Create-CA-CLI) and [Updating a CA \(AWS CLI\)](PCAUpdateCA.md#ca-update-cli)\.

     ACM Private CA provides an OCSP responder that clients can query before initiating a secure connection\. 

     When you enable OCSP for a CA, ACM Private CA includes the *Authority Information Access* extension in each new certificate issued\. This extension provides a URL for your OCSP responder\. The extension allows clients such as web browsers to query the responder and determine whether an end\-entity or subordinate CA certificate can be trusted\.

     The provided OCSP responder is compliant with [RFC 5019](https://tools.ietf.org/html/rfc5019)\. You can also use this responder as a proxy for your own OCSP infrastructure\.

     OCSP is well suited for use by clients with limited storage and computing capacity, such as IoT devices\.
   + **Certificate Revocation Lists \(CRLs\)**

     A CRL contains a list of revoked certificates\. When you associate a CRL with a CA, ACM Private CA includes the *CRL Distribution Points* extension in each new certificate issued\. This extension provides a URL for the CRL\. The extension allows clients such as web browsers to query the CRL and determine whether an end\-entity or subordinate CA certificate can be trusted\. 

     Because a client must download CRLs and process them locally, their use is more resource\-intensive than OCSP\. 

   You can configure either, neither, or both of these mechanisms for your CA\. Though optional, managed revocation is recommended as a best practice\.
**Note**  
OCSP is not currently supported in the ACM Private CA console\. Beta OCSP users can find AWS CLI examples at [Using the AWS CLI to Create a CA](#Create-CA-CLI) and [Updating a CA \(AWS CLI\)](PCAUpdateCA.md#ca-update-cli)\.

   To configure CRL, complete the following steps: <a name="PcaCreateCRL"></a>

   1. Select **Enable CRL distribution**\.

   1. To create a new Amazon S3 bucket for your CRL entries, choose **Yes** for the **Create a new S3 bucket** option and type a unique bucket name\. \(You do not need to include the path to the bucket\.\) Otherwise, choose **No** and choose an existing bucket from the list\. 

      If you choose **Yes**, ACM Private CA creates and attaches the necessary policy to your bucket\. If you choose **No**, you must attach a policy to your bucket before you can begin generating CRLs\. Use one of the policy patterns described in [Access Policies for CRLs in Amazon S3 ](crl-ref.md#s3-policies)\. For information about attaching a policy, see [How Do I Add an S3 Bucket Policy?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)
**Note**  
Your private CA might fail to create a CRL bucket if Amazon S3 **block public access** settings are enforced on your account\. Check your Amazon S3 settings if this occurs\. For more information, see [Blocking public access to your Amazon S3 storage](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html)\.

   1. Expand **Advanced** for additional configuration options\.
      + Add a **Custom CRL Name** to create an alias for your Amazon S3 bucket\. This name is contained in certificates issued by the CA in the “CRL Distribution Points" extension that is defined by RFC 5280\.
      + Type the number of days your CRL will remain valid\. The default value is 7 days\. For online CRLs, a validity period of two to seven days is common\. ACM Private CA tries to regenerate the CRL at the midpoint of the specified period\. 

   Choose **Next**\.

1. <a name="PcaCreateAddTags"></a>

**\(Optional\) Add tags**

   On the **Add tags** page, you can optionally tag your CA\. Tags are key\-value pairs that serve as metadata for identifying and organizing AWS resources\. For a list of ACM Private CA tag parameters and for instructions on how to add tags to CAs after creation, see [Add Tags to your Private Certificate Authority](PCAUpdateCA.md#PcaCaTagging)\.

   Choose **Next**\.

1. <a name="PcaCreateAcmPerms"></a>

**\(Optional\) Configure CA permissions**

   Optionally delegate automatic renewal permissions to the ACM service principal\. ACM can only automatically renew private end\-entity certificates generated by this CA if this permission is granted\. You can assign renewal permissions at any time with the ACM Private CA [CreatePermission](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_CreatePermission.html) API or [create\-permission](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/create-permission.html) CLI command\.

   The default is to enable these permissions\.

   Choose **Next**\.

1. <a name="PcaCreateReview"></a>

**Review**

   On the **Review and create** page, confirm that your configuration is correct, select the check box to acknowledge pricing information, and choose **Confirm and create**\. 

   If you want to continue on to creating and installing a CA certificate, choose **Get started** in the **Success** dialog box\. Then follow the instructions at [Creating and Installing the Certificate for a Private CA](PCACertInstall.md)\. Otherwise choose **You can also finish this later**, which takes you to a list of your **Private CAs**\. 

## Using the AWS CLI to Create a CA<a name="Create-CA-CLI"></a>

Use the [create\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/create-certificate-authority.html) command to create a private CA\. You must specify the CA configuration, the CA type\. If you plan to enable revocation status checks, you must also specify a configuration for a CRL, OCSP functionality, both, or neither\. Optionally, you can supply tags and an idempotency token\.

If you are configuring a CRL, you must create an Amazon S3 bucket and attach an access policy to it *before* you issue the create\-certificate\-authority command\. Use one of the policy patterns described in [Access Policies for CRLs in Amazon S3 ](crl-ref.md#s3-policies)\. For information about attaching a policy, see [How Do I Add an S3 Bucket Policy?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)

The CA configuration specifies the following information:
+ The name of the algorithm
+ The key size to be used to create the CA private key
+ The type of signing algorithm that the CA uses to sign
+ X\.500 subject information

The revocation configuration for a CRL defines a `CrlConfiguration` object with the following information:
+ The `Enabled` flag set to "true"
+ The CRL expiration period in days \(the validity period of the CRL\)
+ The Amazon S3 bucket that will contain the CRL 
+ A CNAME alias for the S3 bucket that is included in certificates issued by the CA

The revocation configuration for OCSP defines an `OcspConfiguration` object with the following information:
+ The `Enabled` flag set to "true"
+ \(Optional\) A custom CNAME declared as a value for `OcspCustomCname`\.

**Note**  
You can enable both revocation mechanisms on the same CA by defining both an `OcspConfiguration` object and a `CrlConfiguration` object\. If you supply no \-\-revocation\-configuration parameter, both mechanisms are disabled by default\. If you need revocation validation support later, see [Updating a CA \(AWS CLI\)](PCAUpdateCA.md#ca-update-cli)\.

The following examples assume that you have set up your `.aws` configuration directory with a valid default Region, endpoint, and credentials\. For information about configuring your AWS CLI environment, see [Configuration and credential file settings](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)\. For readability, we supply the CA configuration and revocation input as JSON files in the example commands\. Modify the example files as needed for your use\. 

All of the examples use the same `ca_config.txt`:

**File: ca\_config\.txt**

```
{
   "KeyAlgorithm":"RSA_2048",
   "SigningAlgorithm":"SHA256WITHRSA",
   "Subject":{
      "Country":"US",
      "Organization":"Example Corp",
      "OrganizationalUnit":"Sales",
      "State":"WA",
      "Locality":"Seattle",
      "CommonName":"www.example.com"
   }
}
```

**Example 1: Create a CA with an attached CRL**  
In this example, the revocation configuration defines CRL parameters\.  
**File: revoke\_config\.txt**  

```
{
   "CrlConfiguration":{
      "Enabled":true,
      "ExpirationInDays":7,
      "S3BucketName":"your-bucket-name"
   }
}
```
**Command**  

```
aws acm-pca create-certificate-authority \
      --certificate-authority-configuration file://ca_config.txt \
      --revocation-configuration file://revoke_config.txt \
      --certificate-authority-type "ROOT" \
      --idempotency-token 01234567 \
      --tags Key=Name,Value=MyPCA-1
```
If successful, this command outputs the ARN \(Amazon Resource Name\) of the CA\.  

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123456789ab"
}
```
Use the following command to inspect the configuration of your CA:  

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123456789ab" \
      --output json
```
This description should contain the following section:  

```
"RevocationConfiguration": {
   ...
   "CrlConfiguration": {
      "Enabled": true,
      "ExpirationInDays": 7,
      "S3BucketName": "your-bucket-name"
   },
   ...
}
```

**Example 2: Create a CA with an attached CRL and a custom CNAME enabled**  
In this example, the revocation configuration defines CRL parameters that include a custom CNAME\.  
**File: revoke\_config\.txt**  

```
{
   "CrlConfiguration":{
      "Enabled":true,
      "ExpirationInDays":7,
      "CustomCname": "alternative.example.com",
      "S3BucketName":"your-bucket-name"
   }
}
```
**Command**  

```
aws acm-pca create-certificate-authority \
      --certificate-authority-configuration file://ca_config.txt \
      --revocation-configuration file://revoke_config.txt \
      --certificate-authority-type "ROOT" \
      --idempotency-token 01234567 \
      --tags Key=Name,Value=MyPCA-1
```
If successful, this command outputs the ARN \(Amazon Resource Name\) of the CA\.  

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123456789ab"
}
```
Use the following command to inspect the configuration of your CA:  

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123456789ab" \
      --output json
```
This description should contain the following section:  

```
"RevocationConfiguration": {
   ...
   "CrlConfiguration": {
      "Enabled": true,
      "ExpirationInDays": 7,
      "CustomCname": "alternative.example.com",
      "S3BucketName": "your-bucket-name",
   ...
   }
}
```

**Example 3: Create a CA with OCSP enabled**  
In this example, the revocation file enables default OCSP support, which uses the ACM Private CA responder to check certificate status\.  
**File: revoke\_config\.txt for OCSP**  

```
{
   "OcspConfiguration":{
      "Enabled":true
   }
}
```
**Command**  

```
aws acm-pca create-certificate-authority \
	--certificate-authority-configuration file://ca_config.txt \
	--revocation-configuration file://revoke_config.txt \
	--certificate-authority-type "ROOT" \
	--idempotency-token 01234567 \
	--tags Key=Name,Value=MyPCA-2
```
If successful, this command outputs the ARN \(Amazon Resource Name\) of the CA\.  

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123456789ab"
}
```
Use the following command to inspect the configuration of your CA:  

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123456789ab" \
      --output json
```
This description should contain the following section:  

```
"RevocationConfiguration": {
   ...
   "OcspConfiguration": {
      "Enabled": true
   }
   ...
}
```

**Example 4: Create a CA with OCSP and a custom CNAME enabled**  
In this example, the revocation file enables customized OCSP support\. The `OcspCustomCname` parameter takes a fully qualified domain name \(FQDN\) as its value\.   
When this parameter is present, ACM Private CA inserts the FQDN into issued certificates in place of the default URL for the AWS OCSP responder\. When a TLS endpoint, for example, receives a certificate containing the custom FQDN, it queries this address for an OCSP response\. This requires you to take two additional actions:
+ Use a proxy server to forward traffic that arrives at your custom FQDN to the AWS OCSP responder\.
+ Add a corresponding CNAME record to your DNS database\. 
For more information about implementing a complete OCSP solution using a custom CNAME, see [Customizing Online Certificate Status Protocol \(OCSP\)](ocsp-customize.md)\.
For example, here is a CNAME record for customized OCSP as it would appear in Amazon Route 53:   


****  

| Record name | Type | Routing policy | Differentiator | Value/Route traffic to | 
| --- | --- | --- | --- | --- | 
|  alternative\. example\.com  | CNAME | Simple | \- | proxy\.example\.com | 
The value of the CNAME must not include a protocol prefix such as "http://" or "https://"\.
**File: revoke\_config\.txt for OCSP**  

```
{
   "OcspConfiguration":{
      "Enabled":true,
      "OcspCustomCname":"alternative.example.com"
   }
}
```
**Command**  

```
aws acm-pca create-certificate-authority \
	--certificate-authority-configuration file://ca_config.txt \
	--revocation-configuration file://revoke_config.txt \
	--certificate-authority-type "ROOT" \
	--idempotency-token 01234567 \
	--tags Key=Name,Value=MyPCA-3
```
If successful, this command outputs the ARN \(Amazon Resource Name\) of the CA\.  

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123456789ab"
}
```
Use the following command to inspect the configuration of your CA:  

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123456789ab" \
      --output json
```
This description should contain the following section:  

```
"RevocationConfiguration": {
   ...
   "OcspConfiguration": {
      "Enabled": true,
      "OcspCustomCname": "alternative.example.com"
   }
   ...
}
```

## Using AWS CloudFormation to Create a CA<a name="Create-CA-CFN"></a>

For information about creating a private CA using AWS CloudFormation, see [ACM PCA Resource Type Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_ACMPCA.html) in the *AWS CloudFormation User Guide*\.