# Procedure for creating a CA \(console\)<a name="Create-CA-console"></a>

Complete the following steps to create a private CA using the AWS Management Console\.

**To get started using the console**

Sign in to your AWS account and open the ACM Private CA console at **[https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)**\. 
+ If you are opening the console in a Region where you have no private CAs, the introductory page appears\. Choose **Create a private CA**\. 
+ If you are opening the console in a Region where you have already created a CA, the **Private certificate authorities** page opens with a list of your CAs\. Choose **Create CA**\.

## 1\. Select CA type<a name="PcaCreateCaType"></a>

On the **Select the certificate authority \(CA\) type** page, choose the type of private certificate authority that you want to create\. 
+ Choosing **Root CA** establishes a new CA hierarchy\. This CA is backed by a self\-signed certificate\. It serves as the ultimate signing authority for other CAs and end\-entity certificates in the hierarchy\.
+ Choosing **Subordinate CA** creates a CA that must be signed by a parent CA above it in the hierarchy\. Subordinate CAs are typically be used to create other subordinate CAs or to issue end\-entity certificates to users, computers, and applications\. 
**Note**  
ACM Private CA provides an automated signing process when your subordinate CA's parent CA is also hosted by ACM Private CA\. All you do is choose the parent CA to use\.  
Your subordinate CA might need to be signed by an external signing authority\. If so, ACM Private CA provides you with a certificate signing request \(CSR\) that you must download and use to obtain a signed CA certificate\. For more information, see [Installing a subordinate CA certificate signed by an external parent CA](PCACertInstall.md#InstallSubordinateExternal)\.

After choosing a CA type, choose **Next**\.

## 2\. Configure CA subject name<a name="PcaCreateCaName"></a>

Under **Subject distinguished name options**, configure the subject name of your private CA\. You must enter at least one of the following values:
+ **Organization \(O\)**
+ **Organization Unit \(OU\)**
+ **Country name \(C\)**
+ **State or province name**
+ **Locality name**
+ **Common Name \(CN\)**

Because the backing certificate is self\-signed, the subject information that you provide for a private CA is probably more sparse than what a public CA would contain\. For more information about each of the values that make up a subject distinguished name, see [X\.500 Distinguished Name](PcaTerms.md#terms-x500dn)\.

 When done, choose **Next**\.

## 3\. Configure CA key algorithm<a name="PcaCreateKeyAlg"></a>

Under **Key algorithm options**, choose the key algorithm and the bit\-size of the key\. The default value is an RSA algorithm with a 2048\-bit key length\. If you expand the **Advanced** options, you can choose from the following algorithms: 
+ RSA 2048
+ RSA 4096
+ ECDSA P256
+ ECDSA P384

Make a selection and then choose **Next**\. 

## 4\. Configure revocation<a name="PcaCreateRevocation"></a>

On the **Configure revocation * \- optional*** page, under **Configure revocation options** there are two methods for sharing revocation status with clients that use your certificates: configuring a certificate revocation list \(CRL\), or using Online Certificate Status Protocol \(OCSP\)\. These are displayed as follows:
+ **CRL distribution**
+ **OCSP**

You can configure either, neither, or both of these revocation options for your CA\. Although optional, managed revocation is recommended as a [best practice](ca-best-practices.md)\. Before completing this step, see [Setting up a certificate revocation method](revocation-setup.md) for information about the advantages of each method, the preliminary setup that might be required, and additional revocation features\.

**Note**  
If you create your CA without configuring revocation, you can always configure it later\. For more information, see [Updating your private CA](PCAUpdateCA.md)\. 

### To configure a CRL<a name="collapsible-section-2"></a>

1. On the **Configure revocation * \- optional*** page, choose **CRL distribution**\.

1. To create a new Amazon S3 bucket for your CRL entries, choose **Yes** for the **Create a new S3 bucket** option and type a unique bucket name\. \(You do not need to include the path to the bucket\.\) Otherwise, choose **No** and choose an existing bucket from the list\. 

   If you choose **Yes**, ACM Private CA creates and attaches the [required access policy](crl-planning.md#s3-policies) to your bucket\. If you choose **No**, you must attach a policy to your bucket before you can begin generating CRLs\. Use one of the policy patterns described in [Access policies for CRLs in Amazon S3 ](crl-planning.md#s3-policies)\. For information about attaching a policy, see [How Do I Add an S3 Bucket Policy?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)
**Note**  
When you are using the ACM Private CA console, an attempt to create a CA fails if both of the following conditions apply:  
You are enforcing Block Public Access settings on your S3 bucket or account\.
You asked ACM Private CA to create an S3 bucket automatically\.
In this situation, the console attempts, by default, to create a publicly accessible bucket, and Amazon S3 rejects this action\. Check your Amazon S3 settings if this occurs\. For more information, see [Blocking public access to your Amazon S3 storage](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html)\.

1. Expand **Advanced** for additional configuration options\.
   + Add a **Custom CRL Name** to create an alias for your Amazon S3 bucket\. This name is contained in certificates issued by the CA in the “CRL Distribution Points" extension that is defined by RFC 5280\.
   + Type the number of days your CRL will remain valid\. The default value is 7 days\. For online CRLs, a validity period of 2\-7 days is common\. ACM Private CA tries to regenerate the CRL at the midpoint of the specified period\. 

### To configure OCSP<a name="collapsible-section-1"></a>

1. On the **Configure revocation * \- optional*** page, choose **OCSP**\.

1. In the **Custom OCSP endpoint * \- optional*** field, you can provide a non\-default fully qualified domain name \(FQDN\) for your OCSP endpoint\.

When you supply an FQDN in this field, ACM Private CA inserts the FQDN into the *Authority Information Access* extension of each issued certificate in place of the default URL for the AWS OCSP responder\. When an endpoint receives a certificate containing the custom FQDN, it queries that address for an OCSP response\. For this mechanism to work, you need to take two additional actions:
   + Use a proxy server to forward traffic that arrives at your custom FQDN to the AWS OCSP responder\.
   + Add a corresponding CNAME record to your DNS database\. 
**Tip**  
For more information about implementing a complete OCSP solution using a custom CNAME, see [Configuring a Custom URL for ACM Private CA OCSP](ocsp-customize.md)\.

   For example, here is a CNAME record for customized OCSP as it would appear in Amazon Route 53:   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/Create-CA-console.html)
**Note**  
The value of the CNAME must not include a protocol prefix such as "http://" or "https://"\.

When you are done configuring revocation methods, choose **Next**\.

## 5\. Add tags<a name="PcaCreateAddTags"></a>

On the **Add tags * \- optional*** page, you can optionally tag your CA\. Tags are key\-value pairs that serve as metadata for identifying and organizing AWS resources\. For a list of ACM Private CA tag parameters and for instructions on how to add tags to CAs after creation, see [Adding tags to your private CA](PcaCaTagging.md)\.

Choose **Next**\.

## 6\. Configure CA permissions<a name="PcaCreateAcmPerms"></a>

Optionally delegate automatic renewal permissions to the ACM service principal\. ACM can only automatically renew private end\-entity certificates generated by this CA if this permission is granted\. You can assign renewal permissions at any time with the ACM Private CA [CreatePermission](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_CreatePermission.html) API or [create\-permission](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/create-permission.html) CLI command\.

The default is to enable these permissions\.

Choose **Next**\.

## 7\. Review and create<a name="PcaCreateReview"></a>

On the **Review and create** page, confirm that your configuration is correct\. You can choose **Edit ** next to each step to return to the step and change its settings\. If you are creating your CA in the ap\-northeast\-3 Region, check the box to acknowledge that the key storage security standard is FIPS 140\-2 Level 2 or higher\. In all cases, check the box to acknowledge pricing information\. Finally, choose **Confirm and create**\.

If you want to continue on to creating and installing a CA certificate, choose **Install CA certificate** in the **Success\!** window\. Otherwise choose **Cancel**, which takes you to a list of your **Private CAs**\. You can finish setting up the CA by following the instructions at [Creating and installing the CA certificate](PCACertInstall.md)\. 