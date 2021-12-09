# Troubleshooting<a name="PcaTsIntro"></a>

Consult the following topics if you have problems using AWS Certificate Manager Private Certificate Authority\.

**Topics**
+ [Signing a CSR](PcaTsSignCsr.md)
+ [Latency in OCSP responses](#OCSP-latency-troubleshooting)
+ [Amazon S3 blocks CRL bucket](PcaS3CsrBlock.md)
+ [Deleting a self\-signed CA certificate](PcaRevokeSelfSigned.md)
+ [Handling exceptions](PCATsExceptions.md)

## Latency in OCSP responses<a name="OCSP-latency-troubleshooting"></a>

OCSP responsiveness may be slower if the caller is geographically distant from a regional edge cache or from the Region of the issuing CA\. For more information about regional edge cache availability, see [Global Edge Network](https://aws.amazon.com/cloudfront/details#Global_Edge_Network)\. We recommend issuing certificates in a Region near where they will be used\.