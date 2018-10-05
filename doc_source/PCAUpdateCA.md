# Update Your Private CA<a name="PCAUpdateCA"></a>

You can update the revocation configuration of a private CA by changing any of the following values:
+ A value that specifies whether the private CA generates a certificate revocation list \(CRL\)\.
+ The number of days before a CRL expires\. Note that ACM PCA begins trying to regenerate the CRL at 1/2 the number of days you specify\. 
+ The name of the S3 bucket where your CRL is saved\.
+ An alias to hide the name of your S3 bucket from public view\.

**Important**  
Changing any of the preceding parameters can have negative consequences\. For example, disabling CRL generation, changing the validity period, or changing the S3 bucket after you have placed your private CA is in production could break existing certificates that depend on the CRL and the current CRL configuration\. Changing the alias can be done safely as long as the old alias remains linked to the correct bucket\. 

ACM PCA updates the status of your private CA at certain times\. You can also change the status, but you can only change it from `ACTIVE` to `DISABLED` and back\. For example, if you want to [delete](PCADeleteCA.md) your CA, you must disable it 30 days before deletion\. At various times, ACM PCA can set the following values: 
+ `CREATING` – ACM PCA is trying to create your private CA\.
+ `PENDING_CERTIFICATE` – Your private CA has been created but you must import the signed CA certificate\.
+ `ACTIVE` – Your private CA has been created and is functioning normally\.
+ `EXPIRED` – The CA certificate for your private CA has expired\.
+ `FAILED` – Your private CA is not functioning properly and has been placed in a failed state\.

If the CA certificate for your private CA expires, ACM PCA sets the status to `EXPIRED`\. You can set the status to `DISABLED` at this point, but you cannot set it to `ENABLED`\. You might want to set the status to `DISABLED` if you want to delete the CA in 30 days\. If you import a new CA certificate for your private CA, ACM PCA resets the status to `ACTIVE` unless you set it to `DISABLED` after the CA certificate expired\. Only you can set the status to `DISABLED`\. 

**Topics**

You can update a certificate from the AWS Management Console or AWS CLI\.
+ [Updating certificate revocation \(console\)](#UpdateCrlConsole)
+ [Updating certificate status \(console\)](#UpdateStatusConsole)
+ [Updating a private CA \(AWS CLI\)](#UpdateCli)

**To update certificate revocation \(console\)**

1. Sign in to your AWS account and open the ACM PCA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Choose **Private CAs**\.

1. Choose your private CA from the list\.

1. On the **Actions** menu, choose **Update CA revocation**\.

1. Select **Enable CRL distribution** to generate certificate revocation lists \(CRLs\)\.

1. For **Create a new S3 bucket**, choose **Yes** and type a unique bucket name or choose **No** and choose an existing bucket from the list\. 

1. For **Custom CRL Name**, type an alias to hide your S3 bucket name from public view\.

1. For **Valid for**, type a validity period in days\.

1. Choose **Update**\.

**To update certificate status \(console\)**

1. Sign in to your AWS account and open the ACM PCA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Choose **Private CAs**\.

1. Choose your private CA from the list\.

1. On the **Actions** menu, choose **Disable** to disable a private CA that's currently active or choose **Enable** to set a CA active\. 

**To update your private CA \(AWS CLI\)**  
Use the [update\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/update-certificate-authority.html) command\. You can use a file similar to the following to specify the CRL configuration\. 

```
{
  "CrlConfiguration": 
    {"Enabled": true,
     "ExpirationInDays": 7,
     "CustomCname": "https://www.somename.crl",
     "S3BucketName": "your-crl-bucket-name"}
}
```

The following command uses the preceding file to configure revocation and sets the status of the private CA to `ACTIVE`\. 

```
aws acm-pca update-certificate-authority \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-1232456789012 \
--revocation-configuration file://C:\revoke_config.txt \
--status "ACTIVE"
```