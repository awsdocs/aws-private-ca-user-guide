# Limits<a name="PcaLimits"></a>

The following ACM PCA limits apply to each region and each account\. To request higher limits, create a case at the [AWS Support Center](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-acm-pca)\. New AWS accounts might start with limits that are lower than those that are described here\. 


****  

| Item | Default Limit | 
| --- | --- | 
| Number of private certificate authorities \(CAs\) | 10 | 
| Number of private certificates per private CA | 50,000 | 

**Note**  
A private CA that has been deleted will count towards your limit until the end of its restoration period\. For more information, see [Delete Your Private CA](https://docs.aws.amazon.com/acm-pca/latest/userguide/PCADeleteCA.html)\.

ACM PCA is integrated with ACM\. You can use the ACM console, AWS CLI, or ACM API to request private certificates from an existing private certificate authority \(CA\)\. The certificates are managed by ACM and have the same restrictions as public certificates issued by ACM\. For a list of the restrictions, see [Request a Private Certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-private.html)\. You can also issue private certificates with the ACM PCA API or AWS CLI\. For more information, see [Issue a Private Certificate](PcaIssueCert.md)\. Regardless of which method you use, you can create 10 private CAs and 50,000 private certificates for each\. ACM places limits on public and imported certificates\. For more information, see [ACM Limits](https://docs.aws.amazon.com/acm/latest/userguide/acm-limits.html)\. 