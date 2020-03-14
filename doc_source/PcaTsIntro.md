# Troubleshooting<a name="PcaTsIntro"></a>

Consult the following topics if you have problems using AWS Certificate Manager Private Certificate Authority\.

**Topics**
+ [Signing a CSR](PcaTsSignCsr.md)
+ [Amazon S3 Blocks CRL Bucket](PcaS3CsrBlock.md)
+ [Deleting a Self\-signed CA Certificate](#PcaRevokeSelfSigned)
+ [S3 Errors](PCA-TA-S3.md)
+ [Handling Exceptions](PCATsExceptions.md)

## Deleting a Self\-signed CA Certificate<a name="PcaRevokeSelfSigned"></a>

You cannot revoke a self\-signed CA certificate\. Instead, you must delete the CA\.