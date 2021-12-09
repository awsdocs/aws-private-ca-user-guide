# Private CA administration<a name="creating-managing"></a>

Using ACM Private CA, you can create an entirely AWS hosted hierarchy of root and subordinate certificate authorities \(CAs\) for internal use by your organization\. To manage certificate revocation, you can enable Online Certificate Status Protocol \(OCSP\), certificate revocation lists \(CRLs\), or both\. ACM Private CA stores and manages your CA certificates, CRLs, and OCSP responses, and the private keys for your root authorities are securely stored by AWS\.

**Note**  
The OCSP implementation in ACM Private CA does not support OCSP request extensions\. If you submit an OCSP batch query containing multiple certificates, the AWS OCSP responder processes only the first certificate in the queue and drops the others\. A revocation might take up to an hour to appear in OCSP responses\.

You can access ACM Private CA using the AWS Management Console, the AWS CLI, and the ACM Private CA API\. The following topics show you how to use the console and the CLI\. To learn more about the API, see the [AWS Certificate Manager Private Certificate Authority API Reference](https://docs.aws.amazon.com/acm-pca/latest/APIReference/)\. For Java examples that show you how to use the API, see [Using the ACM Private CA API \(Java examples\)](PcaApiIntro.md)\. 

**Topics**
+ [Creating a private CA](create-CA.md)
+ [Creating and installing the CA certificate](PCACertInstall.md)
+ [Controlling access to a private CA](granting-ca-access.md)
+ [Adding tags to your private CA](PcaCaTagging.md)
+ [Updating your private CA](PCAUpdateCA.md)
+ [Deleting your private CA](PCADeleteCA.md)
+ [Restoring a private CA](PCARestoreCA.md)