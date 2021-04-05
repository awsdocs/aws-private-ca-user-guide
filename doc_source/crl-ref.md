# Certificate Revocation List \(CRL\) Reference<a name="crl-ref"></a>

To do

## CRL Structure<a name="crl-structure"></a>

Each CRL is a DER encoded file\. To download the file and use OpenSSL to view it, use a command similar to the following:

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
The CRL will only be deposited in Amazon S3 once a certificate has been issued that refers to it\. Prior to that, there will only be an `acm-pca-permission-test-key` file visible in the Amazon S3 bucket\.

## Access Policies for CRLs in Amazon S3<a name="s3-policies"></a>

If you plan to create a CRL, you need to prepare an Amazon S3 bucket to store it in\. ACM Private CA automatically deposits the CRL in the Amazon S3 bucket you designate and updates it periodically\. For more information, see [How Do I Create an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) 

Your S3 bucket must be secured by an attached permissions policy\. The policy protects the contents of the bucket from being accessed by unauthorized users or service principals\. During the console procedure for creating a CA, you can let ACM Private CA create a new bucket and apply a policy automatically, or you can use a bucket that you have previously set up yourself\. In that case, or when you create a CA using the AWS CLI, you must attach a policy manually\. For more information, see [How Do I Add an S3 Bucket Policy?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)

We offer two example policies for securing Amazon S3 buckets\.
<a name="policy1"></a>
**Policy 1 \(Restrictive\)**  
This policy grants restricted permissions on the S3 bucket to the ACM Private CA service principal\. This example restricts by both AWS account and by the ARN of a private CA, but it could also be configured to restrict just one or the other\. The `Put` permission allows ACM Private CA to place objects in the bucket\. The `Get` permission allows objects in the bucket to be retrieved\. 

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
            "arn:aws:s3:::bucket-name/*",
            "arn:aws:s3:::bucket-name"
         ],
         "Condition":{
            "StringEquals":{
               "aws:SourceAccount":"account",
               "aws:SourceArn":"arn:partition:acm-pca:region:account:certificate-authority/11111111-2222-3333-4444-555555555555"
            }
         }
      }
   ]
}
```
<a name="policy2"></a>
**Policy 2 \(Permissive\)**  
This policy grants `Put` and `Get` permissions on the S3 bucket to the ACM Private CA service principal without restricting access by CA or user\. The `Put` permission allows ACM Private CA to place objects in the bucket\. The `Get` permission allows objects in the bucket to be retrieved\. A permissive policy is appropriate if you want the bucket to be available for multiple CAs or multiple users\.

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
            "arn:aws:s3:::bucket-name/*",
            "arn:aws:s3:::bucket-name"
         ]
      }
   ]
}
```

## Encrypting Your CRLs<a name="crl-encryption"></a>

You can optionally configure encryption on the Amazon S3 bucket containing your CRLs\. ACM Private CA supports two encryption modes for assets in S3:
+ Automatic server\-side encryption with Amazon S3\-managed AES\-256 keys\.
+ Customer managed encryption using AWS Key Management Service and customer master keys \(CMKs\) configured to your specifications\.

**Note**  
ACM Private CA does not support using default CMKs generated automatically by S3\.

The following procedures describe how to set up each of the encryption options\.

**To configure automatic encryption**  
Complete the following steps to enable S3 server\-side encryption\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** table, choose the bucket that will hold your ACM Private CA assets\.

1. On the page for your bucket, choose the **Properties** tab\.

1. Choose the **Default encryption** card\.

1. Choose **AES\-256**\.

1. Optionally view the bucket permissions policy, then choose **Save**\.



**To configure custom encryption**  
Complete the following steps to enable encryption using a custom CMK\.

1. \(Optional\) If you do not have an AWS KMS CMK already, create one using the following AWS CLI [create\-key](https://docs.aws.amazon.com/cli/latest/reference/kms/create-key.html) command:

   ```
   aws kms create-key
   ```

   The output contains the key ID and Amazon Resource Name \(ARN\) of the CMK\. The following is example output:

   ```
   {
       "KeyMetadata": {
           "KeyId": "6f815f63-e628-448c-8251-e40cb0d29f59",
           "Description": "",
           "Enabled": true,
           "KeyUsage": "ENCRYPT_DECRYPT",
           "KeyState": "Enabled",
           "CreationDate": 1478910250.94,
           "Arn": "arn:aws:kms:us-west-2:123456789012:key/6f815f63-e628-448c-8251-e40cb0d29f59",
           "AWSAccountId": "123456789012"
       }
   }
   ```

1. Using the following steps, you give the ACM Private CA service principal permission to use the CMK\. By default, all AWS KMS CMKs are private; only the resource owner can use a CMK to encrypt and decrypt data\. However, the resource owner can grant permissions to access the CMK to other users and resources\. The service principal must be in the same Region as where the CMK is stored\.

   1. First, save the default policy for your CMK as `policy.json` using the following [get\-key\-policy](https://docs.aws.amazon.com/cli/latest/reference/kms/get-key-policy.html) command:

      ```
      aws kms get-key-policy --key-id key-id --policy-name default --output text > ./policy.json
      ```

   1. Open the `policy.json` file in a text editor and add the following statement:

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
                  "arn:aws:s3:::bucket_name/acm-pca-permission-test-key",
                  "arn:aws:s3:::bucket_name/acm-pca-permission-test-key-private",
                  "arn:aws:s3:::bucket_name/audit-report/*",
                  "arn:aws:s3:::bucket_name/crl/*"
               ]
            }
         }
      }
      ```

   1. Finally, add the updated policy using the following [put\-key\-policy](https://docs.aws.amazon.com/cli/latest/reference/kms/put-key-policy.html) command:

      ```
      aws kms put-key-policy --key-id key-id --policy-name default --policy file://policy.json
      ```