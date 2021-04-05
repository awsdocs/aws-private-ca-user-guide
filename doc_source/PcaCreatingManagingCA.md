# Creating and Managing a Private CA<a name="PcaCreatingManagingCA"></a>

Using ACM Private CA, you can create an entirely AWS hosted hierarchy of root and subordinate certificate authorities \(CAs\) for internal use by your organization\. To manage certificate revocation, you can enable Online Certificate Status Protocol \(OCSP\), certificate revocation lists \(CRLs\), or both\. ACM Private CA stores and manages your CA certificates, CRLs, and OCSP responses, and the private keys for your root authorities are securely stored in AWS\.

You can access ACM Private CA using the AWS Management Console, the AWS CLI, and the ACM Private CA API\. The following topics show you how to use the console and the CLI\. To learn more about the API, see the [AWS Certificate Manager Private Certificate Authority API Reference](https://docs.aws.amazon.com/acm-pca/latest/APIReference/)\. For Java examples that show you how to use the API, see [Using the ACM Private CA API \(Java Examples\)](PcaApiIntro.md)\. 

**Note**  
OCSP is not currently supported in the ACM Private CA console\. Beta OCSP users can find AWS CLI examples at [Using the AWS CLI to Create a CA](PcaCreateCa.md#Create-CA-CLI) and [Updating a CA \(AWS CLI\)](PCAUpdateCA.md#ca-update-cli)\.

**Note**  
The OCSP implementation in ACM Private CA does not support OCSP request extensions\. If you submit an OCSP batch query containing multiple certificates, the AWS OCSP responder processes only the first certificate in the queue and drops the others\. A revocation may take up to an hour to appear in OCSP responses\.

**Topics**
+ [Setting Up ACM Private CA](PcaGettingStarted.md)
+ [Creating a Private CA and Revocation](PcaCreateCa.md)
+ [Creating and Installing the Certificate for a Private CA](PCACertInstall.md)
+ [Controlling Access to a Private CA](granting-ca-access.md)
+ [Updating Your Private CA](PCAUpdateCA.md)
+ [Deleting Your Private CA](PCADeleteCA.md)
+ [Restoring Your Private CA](PCARestoreCA.md)