# Limits<a name="PcaLimits"></a>

This section specifies limits that affect AWS Certificate Manager Private Certificate Authority\.

**Topics**
+ [Limits on Certificates](#PcaLimits-certificates)
+ [API Rate Limits](#PcaLimits-api)

## Limits on Certificates<a name="PcaLimits-certificates"></a>

The following ACM PCA certificate limits apply to each region and each account\. To request higher limits, create a case at the [AWS Support Center](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-acm-pca)\. New AWS accounts might start with limits that are lower than those that are described here\.


****  

| Item | Default Limit | 
| --- | --- | 
| Number of private certificate authorities \(CAs\) | 10 | 
| Number of private certificates per private CA | 50,000 | 

**Note**  
A private CA that has been deleted will count towards your certificate limit until the end of its restoration period\. For more information, see [Delete Your Private CA](https://docs.aws.amazon.com/acm-pca/latest/userguide/PCADeleteCA.html)\.

ACM PCA is integrated with ACM\. You can use the ACM console, AWS CLI, or ACM API to request private certificates from an existing private certificate authority \(CA\)\. The certificates are managed by ACM and have the same restrictions as public certificates issued by ACM\. For a list of the restrictions, see [Request a Private Certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-private.html)\. You can also issue private certificates with the ACM PCA API or AWS CLI\. For more information, see [Issue a Private Certificate](PcaIssueCert.md)\. Regardless of which method you use, you can create 10 private CAs and 50,000 private certificates for each\. ACM places limits on public and imported certificates\. For more information, see [ACM Limits](https://docs.aws.amazon.com/acm/latest/userguide/acm-limits.html)\. 

## API Rate Limits<a name="PcaLimits-api"></a>

The following limits apply to the ACM PCA API for each region and account\. ACM PCA throttles API requests at different limits depending on the API operation\. Throttling means that ACM PCA rejects an otherwise valid request because the request exceeds the operation's limit for the number of requests per second\. When a request is throttled, ACM PCA returns a [https://docs.aws.amazon.com/acm-pca/latest/APIReference/CommonErrors.html](https://docs.aws.amazon.com/acm-pca/latest/APIReference/CommonErrors.html) error\. The following table lists each API operation and the limit at which ACM PCA throttles requests for that operaiton\.


**Requests per second limit for each ACM PCA API operation**  

| API operation | Requests per second | 
| --- | --- | 
| [CreateCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_CreateCertificateAuthority.html) | 1 | 
| [CreateCertificateAuthorityAuditReport](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_CreateCertificateAuthorityAuditReport.html) | 1 | 
| [DeleteCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DeleteCertificateAuthority.html) | 10 | 
| [DescribeCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DescribeCertificateAuthority.html) | 20 | 
| [DescribeCertificateAuthorityAuditReport](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DescribeCertificateAuthorityAuditReport.html) | 20 | 
| [GetCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificate.html) | 20 | 
| [GetCertificateAuthorityCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificateAuthorityCertificate.html) | 20 | 
| [GetCertificateAuthorityCsr](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificateAuthorityCsr.html) | 10 | 
| [ImportCertificateAuthorityCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_ImportCertificateAuthorityCertificate.html) | 10 | 
| [IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html) | 5 | 
| [ListCertificateAuthorities](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_ListCertificateAuthorities.html) | 20 | 
| [ListTags](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_ListTags.html) | 20 | 
| [RestoreCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_RestoreCertificateAuthority.html) | 20 | 
| [RevokeCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_RevokeCertificate.html) | 20 | 
| [TagCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_TagCertificateAuthority.html) | 10 | 
| [UntagCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_UntagCertificateAuthority.html) | 10 | 
| [UpdateCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_UpdateCertificateAuthority.html) | 10 | 

**Note**  
At this time, ACM PCA does not support individual API rate limit increases per customer\.