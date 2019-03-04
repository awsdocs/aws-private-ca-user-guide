# Step 1: Create a Private Certificate Authority<a name="PcaCreateCa"></a>

You can use a private certificate authority \(CA\) to issue and revoke private certificates within an organization\. You can issue certificates for users, applications, computers, servers, and other devices\. The certificates are SSL/TLS public key certificates based on the ISO X\.509 standard\. 
+  [Creating a CA \(Console\)](#CreateCaConsole)
+  [Creating a CA \(AWS CLI\)](#CreateCaCli)

**To create a CA \(console\)**

1. <a name="PcaSignIn"></a>Sign in to your AWS account and open the ACM PCA console at **[https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)**\. The introductory page will appear if your console opens to a region in which you do not have a CA\. Choose **Get started** beneath **Private certificate authority**\. Choose **Get started** again\. If the console opens to a region in which you already have one or more CAs, the introductory page will not be shown\. Choose **Private CAs** and then choose **Create CA**\. 

1. <a name="PcaCreateCaType"></a>Choose the type of the private certificate authority that you want to create\. Currently, you must choose **Subordinate**\. Choose **Next**\. 

   Your private subordinate CA certificate must be authenticated and then signed by an intermediate or root CA\. That root or intermediate CA must be higher in your organization’s CA hierarchy than the CA that you are creating\. For more information, see [Step 3: Sign Your Private CA Certificate](PcaSignCert.md)\. Subordinate CAs are typically used to issue end\-entity certificates to users, computers, and applications\. This enables you to protect your root by removing it from the network and perhaps physically isolating it when you are not using it to commission new subordinate CAs\. 

1. <a name="PcaCreateCaName"></a>Configure the subject name of your private CA\. Use the X\.500 distinguished name format\. You must enter at least one of the following values and then choose **Next**\. For more information about each of the values that make up a subject distinguished name, see [X\.500 Distinguished Name](PcaConcepts.md#concept-x500dn)\. 
   + Organization
   + Organization Unit
   + Country name
   + State or province name
   + Locality name
   + Common Name

1. <a name="PcaCreateKeyAlg"></a>Choose the private key algorithm and the bit size of the key\. The default value is an RSA algorithm with a 2048\-bit key length\. If you expand the **Advanced** options, you can choose one of the following combinations\. Make a selection and then choose **Next**\. 
   + RSA 2048
   + RSA 4096
   + ECDSA P256
   + ECDSA P384

1. <a name="PcaCreateCRL"></a>Configure a certificate revocation list \(CRL\) if you want ACM PCA to maintain one for the certificates revoked by your private CA\. Clients such as web browsers query CRLs to determine whether a certificate can be trusted\. For more information, see [Create a Certificate Revocation List \(CRL\)](PcaUsingCrl.md)\. If you want to create a CRL, do the following: 

   1. Choose **Enable CRL distribution**

   1. To create a new Amazon S3 bucket for your CRL entries, choose **Yes** for the **Create a new S3 bucket** option and type a unique bucket name\. Otherwise, choose **No** and select an existing bucket from the list\. 

      If you choose **Yes**, ACM PCA creates the necessary bucket policy for you\. If you choose **No**, make sure the following policy is attached to your bucket\. For more information, see [Adding a Bucket Policy](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)\. 

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

   1. Expand **Advanced** if you want to specify more about your CRL\. 
      + Add a **Custom CRL Name** to hide the name of your S3 bucket from public view\. 
      + Type the number of days your CRL will remain valid\. For online CRLs, a validity period of two \(2\) to seven \(7\) days is common\. ACM PCA tries to regenerate the CRL at the midpoint of the specified period\. 

1. Add tags to your CA\. Tags are words or phrases that act as metadata for identifying and organizing AWS resources\. Every AWS tag consists of a **key** and **value** pair of your choice\. For a list ofACM PCA tag parameters and instructions on how to add tags to CAs after creation, see [Add Tags](PcaCaTagging.md)\.

1. Choose **Next**\. If everything looks correct, choose **Confirm and create**\. 

1. If you want to continue getting and importing a certificate signing request \(CSR\), leave the **Success** dialog box open and follow the instructions at [Step 2: Get a Certificate Signing Request \(CSR\)](PcaGetCsr.md)\. Otherwise choose **You can also finish this later**\. 

**To create a CA \(AWS CLI\)**  
Use the `[create\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/create-certificate-authority.html)` command to create a private CA\. You must specify the CA configuration, the revocation configuration, the CA type\. You can optionally add an idempotency token or tags\. The CA configuration specifies the following:
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
	--certificate-authority-type "SUBORDINATE" \
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