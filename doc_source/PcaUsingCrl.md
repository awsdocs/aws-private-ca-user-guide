# Create a Certificate Revocation List \(CRL\)<a name="PcaUsingCrl"></a>

ACM PCA can create a certificate revocation list \(CRL\) for you when you create a new private CA or update an existing CA\. The CRL is written to an Amazon S3 bucket that you specify\. Note that ACM PCA does not support Amazon S3 server\-side encryption \(SSE\) for CRLs and audit reports\. Each CRL is a DER encoded file\. You can download the file and use OpenSSL to view it\. 

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

**Topics**
+ [Creating the CRL When You Create a CA](#CreateCRLCreatePCA)
+ [Updating a CRL Configuration \(Console\)](#CreateCRLUpdatePCAconsole)
+ [Updating a CRL Configuration \(AWS CLI\)](#CreateCRLUpdatePCAcli)

## Creating the CRL When You Create a CA<a name="CreateCRLCreatePCA"></a>

You can use the integrated ACM console to configure the parameters for a CRL when you create a private CA\. For more information, see step 5 in [Creating a CA \(Console\)](PcaCreateCa.md#CreateCaConsole)\. You can also use the AWS CLI, but you must specify a file that contains your revocation configuration\. For more information, see [Creating a CA \(AWS CLI\)](PcaCreateCa.md#CreateCaCli)\. You can also use the [CreateCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_CreateCertificateAuthority.html) API\. 

## Updating a CRL Configuration \(Console\)<a name="CreateCRLUpdatePCAconsole"></a>

You can use the ACM console to update the CRL configuration for an existing private CA\.

**To update a CRL configuration \(console\)**

1. Sign in to your AWS account and open the ACM PCA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\. Choose **Private CAs**\. 

1. Choose your CA from the list\.

1. On the **Actions** menu, choose **Update CA revocation**\.

1. Choose **Enable CRL distribution**

1. To create a new S3 bucket for your CRL entries, choose **Yes** for the **Create a new S3 bucket** option and type a unique bucket name\. Otherwise, choose **No** and choose an existing bucket from the list\. 

1. Choose **Advanced** if you want to specify more about your CRL\.

1. Add an optional **Custom CRL Name** to hide the name of your S3 bucket from public view\. 

1. Type the number of days your CRL will remain valid\. For online CRLs, a validity period of 7 days is common\. ACM PCA tries to regenerate the CRL at 1/2 of the specified period\. 

1. Choose **Update**\.

## Updating a CRL Configuration \(AWS CLI\)<a name="CreateCRLUpdatePCAcli"></a>

You can use the AWS CLI to update the CRL configuration for an existing private CA\.

**To update a CRL configuration \(AWS CLI\)**  
Use the [update\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/update-certificate-authority.html) command to update a CRL configuration\. You can modify the following example file to use this command\. 

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
aws acm-pca update-certificate-authority \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-1232456789012 \
--revocation-configuration file://C:\revoke_config.txt \
--status "ACTIVE"
```