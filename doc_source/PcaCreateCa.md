# Creating a Private CA<a name="PcaCreateCa"></a>

This section describes how you create a private certificate authority \(CA\) using ACM Private CA\. You can use these procedures to create both root CAs and subordinate CAs, resulting in an auditable hierarchy of trust relationships that matches your organizational needs\.

For information about using a CA to sign end\-entity certificates for your users, devices, and applications, see [Issuing a Private End\-Entity Certificate](PcaIssueCert.md)\.

**Note**  
Your account is charged a monthly price for each private CA starting from the time that you create it\.

**To create a private CA using the AWS console**

1. Sign in to your AWS account and open the ACM Private CA console at **[https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)**\. The introductory page will appear if your console opens to a region in which you do not have a CA\. Choose **Get started** beneath **Private certificate authority**\. Choose **Get started** again\. If the console opens to a region in which you already have one or more CAs, the introductory page will not be shown\. Choose **Private CAs** and then choose **Create CA**\. 

1. <a name="PcaCreateCaType"></a>On the **Select the certificate authority \(CA\) type** page, select the type of the private certificate authority that you want to create\. 
   + Choosing **Root CA** establishes a new CA hierarchy\. This CA is backed by a self\-signed certificate\. It serves as the ultimate signing authority for other CAs and end\-entity certificates in the hierarchy\.
   + Choosing **Subordinate CA** creates a CA that must be signed by a parent CA above it in the hierarchy\. Subordinate CAs are typically be used to create other subordinate CAs or to issue end\-entity certificates to users, computers, and applications\. 

   After selecting a CA type, choose **Next**\.

1. <a name="PcaCreateCaName"></a>On the **Configure the certificate authority \(CA\) subject name**, configure the subject name of your private CA\. You must enter at least one of the following values:
   + **Organization \(O\)**
   + **Organization Unit \(OU\)**
   + **Country name \(C\)**
   + **State or province name**
   + **Locality name**
   + **Common Name \(CN\)**

   Because the backing certificate is self\-signed, the subject information you provide for a private CA is probably sparser than what a public CA would contain\. For more information about each of the values that make up a subject distinguished name, see [X\.500 Distinguished Name](PcaTerms.md#terms-x500dn)\.

    When done, choose **Next**\.

1. <a name="PcaCreateKeyAlg"></a>On the **Configure the certificate authority \(CA\) key type** page, select the key algorithm and the bit\-size of the key\. The default value is an RSA algorithm with a 2048\-bit key length\. If you expand the **Advanced** options, you can select from the following algorithms: 
   + RSA 2048
   + RSA 4096
   + ECDSA P256
   + ECDSA P384

   Make a selection and then choose **Next**\. 

1. <a name="PcaCreateCRL"></a>On the **Configure certificate revocation** page, you have the option of creating a certificate revocation list \(CRL\) managed by ACM Private CA\. Clients such as web browsers query CRLs to determine whether an end\-entity or subordinate CA certificate can be trusted\. For more information, see [Revoking a Private Certificate](PcaRevokeCert.md)\. 

   ACM Private CA automatically deposits the CRL in the Amazon S3 bucket you designate and updates it periodically\.
**Note**  
The CRL will only be deposited in Amazon S3 once a certificate has been issued that refers to it\. Prior to that, there will only be an `acm-pca-permission-test-key` file visible in the Amazon S3 bucket\.

   To create a CRL, complete the following steps: 

   1. Choose **Enable CRL distribution**\.

   1. To create a new Amazon S3 bucket for your CRL entries, choose **Yes** for the **Create a new S3 bucket** option and type a unique bucket name\. \(You do not need to include the path to the bucket\.\) Otherwise, choose **No** and select an existing bucket from the list\. 

      If you choose **Yes**, ACM Private CA creates the necessary bucket policy for you\. If you choose **No**, make sure the following policy is attached to your bucket\. For more information, see [Adding a Bucket Policy](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)\. 

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "acm-pca.amazonaws.com"
            },
            "Action": [
              "s3:PutObject",
              "s3:PutObjectAcl",
              "s3:GetBucketAcl",
              "s3:GetBucketLocation"
            ],
            "Resource": [
                "arn:aws:s3:::your-bucket-name/*",
                "arn:aws:s3:::your-bucket-name"
            ]
          }
        ]
      }
      ```
**Note**  
Your private CA may fail to create a CRL bucket if Amazon S3 **block public access** settings are enforced on your account\. Check your Amazon S3 settings if this occurs\. For more information, see [Using Amazon S3 Block Public Access](Amazon Simple Storage Service Developer Guideaccess-control-block-public-access.html)\.

   1. Expand **Advanced** for additional configuration options\.
      + Add a **Custom CRL Name** to create an alias for your Amazon S3 bucket\. This name is contained in certificates issued by the CA in the “CRL Distribution Points” extension that is defined by RFC 5280\.
      + Type the number of days your CRL will remain valid\. The default value is 7 days\. For online CRLs, a validity period of two to seven days is common\. ACM Private CA tries to regenerate the CRL at the midpoint of the specified period\. 

   Choose **Next**\.

1. On the **Add tags** page, you can optionally tag your CA\. Tags are key/value pairs that serve as metadata for identifying and organizing AWS resources\. For a list of ACM Private CA tag parameters and for instructions on how to add tags to CAs after creation, see [Add Tags to your Private Certificate Authority](PCAUpdateCA.md#PcaCaTagging)\.

   Choose **Next**\.

1. **Configure CA permissions**

   Optionally delegate automatic renewal permissions to the ACM service principal\. ACM can only automatically renew private end\-entity certificates generated by this CA if this permission is granted\. You can assign renewal permissions at any time with the ACM Private CA API\.

   The default is to enable these permissions\.

   Choose **Next**\.

1. On the **Review and create** page, confirm that your configuration is correct, check the box to acknowledge pricing information, and choose **Confirm and create**\. 

   If you want to continue on to creating and installing a CA certificate, choose **Get started** in the **Success** dialog box and follow the instructions at [Creating and Installing the Certificate for a Private CA](PCACertInstall.md)\. Otherwise choose **You can also finish this later**, which takes you to a list of your **Private CAs**\. 

**To create a private CA using the AWS CLI**  
Use the `[create\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/create-certificate-authority.html)` command to create a private CA\. You must specify the CA configuration, the revocation configuration, and the CA type\. You can optionally add an idempotency token or tags\. The CA configuration specifies the following:
+ The name of the algorithm
+ The key size to be used to create the CA private key
+ The type of signing algorithm that the CA uses to sign
+ X\.500 subject information

The CRL configuration specifies the following:
+ The CRL expiration period in days \(the validity period of the CRL\)
+ The Amazon S3 bucket that will contain the CRL 
+ A CNAME alias for the S3 bucket that is included in certificates issued by the CA

You can modify the following example files to use with this command\.

```
C:\ca_config.txt

{
  "KeyAlgorithm": "RSA_2048",
  "SigningAlgorithm": "SHA256WITHRSA",
  "Subject": 
    {"Country": "US",
     "Organization": "Example Corp",
     "OrganizationalUnit": "Sales",
     "State": "WA",
     "Locality": "Seattle",
     "CommonName": "www.example.com"}
}
```

```
C:\revoke_config.txt

{
  "CrlConfiguration": 
    {"Enabled": true,
     "ExpirationInDays": 7,
     "CustomCname": "some_name.crl",
     "S3BucketName": "your-bucket-name"}
}
```

```
aws acm-pca create-certificate-authority \
	--certificate-authority-configuration file://C:\ca_config.txt \
	--revocation-configuration file://C:\revoke_config.txt \
	--certificate-authority-type "ROOT" \
	--idempotency-token 98256344 \
	--tags  Key=Name,Value=MyPCA
```

If successful, this command outputs the ARN \(Amazon Resource Name\) of the CA\.

```
{
  "CertificateAuthorityArn": "arn:aws:acm-pca:region:account:
         certificate-authority/12345678-1234-1234-123456789012"
}
```

**To create a private CA using AWS CloudFormation**  
For information about creating a private CA using AWS CloudFormation, see [ACM PCA Resource Type Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_ACMPCA.html) in the *AWS CloudFormation User Guide*\.