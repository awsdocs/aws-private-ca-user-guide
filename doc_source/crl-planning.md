# Planning a certificate revocation list \(CRL\)<a name="crl-planning"></a>

Before you can configure a CRL as part of the [CA creation process](create-CA.md), some prior setup may be necessary\. This section explains the prerequisites and options that you should understand before creating a CA with a CRL attached\. 

For information about using Online Certificate Status Protocol \(OCSP\) as an alternative or a supplement to a CRL, see [Certificate revocation options](Create-CA-console.md#PcaCreateRevocation) and [Configuring a Custom URL for AWS Private CA OCSP](ocsp-customize.md)\.

**Topics**
+ [CRL structure](#crl-structure)
+ [Access policies for CRLs in Amazon S3](#s3-policies)
+ [Enabling the S3 Block Public Access \(BPA\) feature](#s3-bpa)
+ [Encrypting Your CRLs](#crl-encryption)

## CRL structure<a name="crl-structure"></a>

Each CRL is a DER encoded file\. To download the file and use [OpenSSL](https://www.openssl.org/) to view it, use a command similar to the following:

```
openssl crl -inform DER -in path-to-crl-file -text -noout
```

CRLs have the following format:

```
Certificate Revocation List (CRL):
		        Version 2 (0x1)
		    Signature Algorithm: sha256WithRSAEncryption
		        Issuer: /C=US/ST=WA/L=Seattle/O=Example Company CA/OU=Corporate/CN=www.example.com
		        Last Update: Feb 26 19:28:25 2018 GMT
		        Next Update: Feb 26 20:28:25 2019 GMT
		        CRL extensions:
		            X509v3 Authority Key Identifier:
		                keyid:AA:6E:C1:8A:EC:2F:8F:21:BC:BE:80:3D:C5:65:93:79:99:E7:71:65
		
		            X509v3 CRL Number:
		                1519676905984
		Revoked Certificates:
		    Serial Number: E8CBD2BEDB122329F97706BCFEC990F8
		        Revocation Date: Feb 26 20:00:36 2018 GMT
		        CRL entry extensions:
		            X509v3 CRL Reason Code:
		                Key Compromise
		    Serial Number: F7D7A3FD88B82C6776483467BBF0B38C
		        Revocation Date: Jan 30 21:21:31 2018 GMT
		        CRL entry extensions:
		            X509v3 CRL Reason Code:
		                Key Compromise
		    Signature Algorithm: sha256WithRSAEncryption
		         82:9a:40:76:86:a5:f5:4e:1e:43:e2:ea:83:ac:89:07:49:bf:
		         c2:fd:45:7d:15:d0:76:fe:64:ce:7b:3d:bb:4c:a0:6c:4b:4f:
		         9e:1d:27:f8:69:5e:d1:93:5b:95:da:78:50:6d:a8:59:bb:6f:
		         49:9b:04:fa:38:f2:fc:4c:0d:97:ac:02:51:26:7d:3e:fe:a6:
		         c6:83:34:b4:84:0b:5d:b1:c4:25:2f:66:0a:2e:30:f6:52:88:
		         e8:d2:05:78:84:09:01:e8:9d:c2:9e:b5:83:bd:8a:3a:e4:94:
		         62:ed:92:e0:be:ea:d2:59:5b:c7:c3:61:35:dc:a9:98:9d:80:
		         1c:2a:f7:23:9b:fe:ad:6f:16:7e:22:09:9a:79:8f:44:69:89:
		         2a:78:ae:92:a4:32:46:8d:76:ee:68:25:63:5c:bd:41:a5:5a:
		         57:18:d7:71:35:85:5c:cd:20:28:c6:d5:59:88:47:c9:36:44:
		         53:55:28:4d:6b:f8:6a:00:eb:b4:62:de:15:56:c8:9c:45:d7:
		         83:83:07:21:84:b4:eb:0b:23:f2:61:dd:95:03:02:df:0d:0f:
		         97:32:e0:9d:38:de:7c:15:e4:36:66:7a:18:da:ce:a3:34:94:
		         58:a6:5d:5c:04:90:35:f1:8b:55:a9:3c:dd:72:a2:d7:5f:73:
		         5a:2c:88:85
```

**Note**  
The CRL will only be deposited in Amazon S3 after a certificate has been issued that refers to it\. Prior to that, there will only be an `acm-pca-permission-test-key` file visible in the Amazon S3 bucket\.

## Access policies for CRLs in Amazon S3<a name="s3-policies"></a>

If you plan to create a CRL, you need to prepare an Amazon S3 bucket to store it in\. AWS Private CA automatically deposits the CRL in the Amazon S3 bucket you designate and updates it periodically\. For more information, see [Creating a bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) 

Your S3 bucket must be secured by an attached permissions policy\. Authorized users and service principals require `Put` permission to allow AWS Private CA to place objects in the bucket, and `Get` permission to retrieve them\. During the console procedure for [creating](Create-CA-console.md) a CA, you can choose to let AWS Private CA create a new bucket and apply a default permissions policy\. 

The default policy applies no `SourceArn` restriction on the CA\. We recommend that you manually apply the less permissive policy shown below, which restricts access to both a specific AWS account and a specific private CA\. For more information, see [Adding a bucket policy using the Amazon S3 console](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
       	 "Effect":"Allow",
         "Principal":{
            "Service":"acm-pca.amazonaws.com"
         },
         "Action":[
            "s3:PutObject",
            "s3:PutObjectAcl",
            "s3:GetBucketAcl",
            "s3:GetBucketLocation"
         ],
         "Resource":[
            "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
            "arn:aws:s3:::DOC-EXAMPLE-BUCKET"
         ],
         "Condition":{
            "StringEquals":{
               "aws:SourceAccount":"account",
               "aws:SourceArn":"arn:partition:acm-pca:region:account:certificate-authority/CA_ID"
            }
         }
      }
   ]
}
```

If you choose to allow the default policy, you can always [modify](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html) it later\.

## Enabling the S3 Block Public Access \(BPA\) feature<a name="s3-bpa"></a>

New Amazon S3 buckets are configured by default with the Block Public Access \(BPA\) feature activated\. Included in the Amazon S3 [security best practices](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html), BPA is a set of access controls that customers can use to fine\-tune access to objects in their S3 buckets and to the buckets as a whole\. When BPA is active and correctly configured, only authorized and authenticated AWS users have access to a bucket and its contents\. 

AWS recommends the use of BPA on all S3 buckets to avoid exposure of sensitive information to potential adversaries\. However, additional planning is required if your PKI clients retrieve CRLs across the public internet \(that is, while not logged into an AWS account\)\. This section describes how to configure a private PKI solution using Amazon CloudFront, a content delivery network \(CDN\), to serve CRLs without requiring authenticated client access to an S3 bucket\.

**Note**  
Using CloudFront incurs additional costs on your AWS account\. For more information, see [Amazon CloudFront Pricing](https://aws.amazon.com/cloudfront/pricing/)\.  
If you choose to store your CRL in an S3 bucket with BPA enabled, and you do not use CloudFront, you must build another CDN solution to ensure that your PKI client has access to your CRL\.

### Set up Amazon S3 with BPA<a name="set-up-s3"></a>

In S3, create a new bucket for your CRL, as usual, then enable BPA on it\.

**To configure an Amazon S3 bucket that blocks public access to your CRL**

1. Create a new S3 bucket using the procedure in [Creating a bucket](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket-overview.html)\. During the procedure, select the **Block *all* public access** option\. 

   For more information, see [Blocking public access to your Amazon S3 storage](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/access-control-block-public-access.html)\.

1. When the bucket has been created, choose its name from the list, navigate to the **Permissions** tab, choose **Edit** in the **Object ownership** section, and select **Bucket owner preferred**\.

1. Also on the **Permissions** tab, add an IAM policy to the bucket as described in [Access policies for CRLs in Amazon S3 ](#s3-policies)\.

### Set up CloudFront for BPA<a name="set-up-cloudfront"></a>

Create a CloudFront distribution that will have access to your private S3 bucket, and can serve CRLs to unauthenticated clients\.

**To configure a CloudFront distribution for the CRL**

1. Create a new CloudFront distribution using the procedure in [Creating a Distribution](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-creating-console.html) in the *Amazon CloudFront Developer Guide*\.

   While completing the procedure, apply the following settings:
   + In **Origin Domain Name**, choose your S3 bucket\.
   + Choose **Yes **for **Restrict Bucket Access**\.
   + Choose **Create a New Identity** for **Origin Access Identity**\.
   + Choose **Yes, Update Bucket Policy** under **Grant Read Permissions on Bucket**\.
**Note**  
In this procedure, CloudFront modifies your bucket policy to allow it to access bucket objects\. Consider [editing](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html) this policy to allow access only to objects under the `crl` folder\. 

1. After the distribution has initialized, locate its domain name in the CloudFront console and save it for the next procedure\.
**Note**  
If your S3 bucket was newly created in a Region other than us\-east\-1, you might get an HTTP 307 temporary redirect error when you access your published application through CloudFront\. It might take several hours for the address of the bucket to propagate\.

### Set up your CA for BPA<a name="set-up-CA"></a>

While configuring your new CA, include the alias to your CloudFront distribution\. 

**To configure your CA with a CNAME for CloudFront**
+ Create your CA using [Procedure for creating a CA \(CLI\) ](Create-CA-CLI.md)\.

  When you perform the procedure, the revocation file `revoke_config.txt` should include the following lines to specify a non\-public CRL object and to provide a URL to the distribution endpoint in CloudFront:

  ```
  "S3ObjectAcl":"BUCKET_OWNER_FULL_CONTROL",
  	"CustomCname":"abcdef012345.cloudfront.net"
  ```

  Afterward, when you issue certificates with this CA, they will contain a block like the following:

  ```
  X509v3 CRL Distribution Points: 
  	Full Name:
  	URI:http://abcdef012345.cloudfront.net/crl/01234567-89ab-cdef-0123-456789abcdef.crl
  ```

**Note**  
If you have older certificates that were issued by this CA, they will be unable to access the CRL\.

## Encrypting Your CRLs<a name="crl-encryption"></a>

You can optionally configure encryption on the Amazon S3 bucket containing your CRLs\. AWS Private CA supports two encryption modes for assets in Amazon S3:
+ Automatic server\-side encryption with Amazon S3\-managed AES\-256 keys\.
+ Customer managed encryption using AWS Key Management Service and an AWS KMS key configured to your specifications\.

**Note**  
AWS Private CA does not support using default KMS keys generated automatically by S3\.

The following procedures describe how to set up each of the encryption options\.

**To configure automatic encryption**

Complete the following steps to enable S3 server\-side encryption\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** table, choose the bucket that will hold your AWS Private CA assets\.

1. On the page for your bucket, choose the **Properties** tab\.

1. Choose the **Default encryption** card\.

1. Choose **Enable**\.

1. Choose **Amazon S3 key \(SSE\-S3\)**\.

1. Choose **Save Changes**\.

**To configure custom encryption**

Complete the following steps to enable encryption using a custom key\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** table, choose the bucket that will hold your AWS Private CA assets\.

1. On the page for your bucket, choose the **Properties** tab\.

1. Choose the **Default encryption** card\.

1. Choose **Enable**\.

1. Choose **AWS Key Management Service key \(SSE\-KMS\)**\.

1. Choose either **Choose from your AWS KMS keys** or **Enter AWS KMS key ARN**\.

1. Choose **Save Changes**\.

1. \(Optional\) If you do not have an KMS key already, create one using the following AWS CLI [create\-key](https://docs.aws.amazon.com/cli/latest/reference/kms/create-key.html) command:

   ```
   $ aws kms create-key
   ```

   The output contains the key ID and Amazon Resource Name \(ARN\) of the KMS key\. The following is an example output:

   ```
   {
       "KeyMetadata": {
           "KeyId": "01234567-89ab-cdef-0123-456789abcdef",
           "Description": "",
           "Enabled": true,
           "KeyUsage": "ENCRYPT_DECRYPT",
           "KeyState": "Enabled",
           "CreationDate": 1478910250.94,
           "Arn": "arn:aws:kms:us-west-2:123456789012:key/01234567-89ab-cdef-0123-456789abcdef",
           "AWSAccountId": "123456789012"
       }
   }
   ```

1. Using the following steps, you give the AWS Private CA service principal permission to use the KMS key\. By default, all KMS keys are private; only the resource owner can use a KMS key to encrypt and decrypt data\. However, the resource owner can grant permissions to access the KMS key to other users and resources\. The service principal must be in the same Region as where the KMS key is stored\.

   1. First, save the default policy for your KMS key as `policy.json` using the following [get\-key\-policy](https://docs.aws.amazon.com/cli/latest/reference/kms/get-key-policy.html) command:

      ```
      $ aws kms get-key-policy --key-id key-id --policy-name default --output text > ./policy.json
      ```

   1. Open the `policy.json` file in a text editor\. Select one of the following policy statements and add it to the existing policy\.

      If your Amazon S3 bucket key is *enabled*, use the following statement:

      ```
      {
         "Sid":"Allow ACM-PCA use of the key",
         "Effect":"Allow",
         "Principal":{
            "Service":"acm-pca.amazonaws.com"
         },
         "Action":[
            "kms:GenerateDataKey",
            "kms:Decrypt"
         ],
         "Resource":"*",
         "Condition":{
            "StringLike":{
               "kms:EncryptionContext:aws:s3:arn":"arn:aws:s3:::bucket-name"
            }
         }
      }
      ```

      If your Amazon S3 bucket key is *disabled*, use the following statement:

      ```
      {
         "Sid":"Allow ACM-PCA use of the key",
         "Effect":"Allow",
         "Principal":{
            "Service":"acm-pca.amazonaws.com"
         },
         "Action":[
            "kms:GenerateDataKey",
            "kms:Decrypt"
         ],
         "Resource":"*",
         "Condition":{
            "StringLike":{
               "kms:EncryptionContext:aws:s3:arn":[
                  "arn:aws:s3:::bucket-name/acm-pca-permission-test-key",
                  "arn:aws:s3:::bucket-name/acm-pca-permission-test-key-private",
                  "arn:aws:s3:::bucket-name/audit-report/*",
                  "arn:aws:s3:::bucket-name/crl/*"
               ]
            }
         }
      }
      ```

   1. Finally, apply the updated policy using the following [put\-key\-policy](https://docs.aws.amazon.com/cli/latest/reference/kms/put-key-policy.html) command:

      ```
      $ aws kms put-key-policy --key-id key_id --policy-name default --policy file://policy.json
      ```