# Private Certificate Authority Security<a name="PcaSecurity"></a>

The private keys for your private CAs are stored in AWS managed hardware security modules \(HSMs\)\. These adhere to FIPS 140\-2 Level 3 security standards\. You can control access to your CAs by setting AWS Identity and Access Management \(IAM\) policies on users, groups, and roles in your organization\. 

Your private CA can automatically create certificate revocation lists \(CRLs\)\. Client applications can query a CRL to determine whether a certificate has been revoked\. You can generate audit reports that list the certificates that your private CA has issued and revoked\. You can turn on CloudTrail to log AWS and ACM Private CA access\. 

For more information about AWS and ACM Private CA features that you can use to help secure your private CA, see the following topics: 
+ [Creating a Certificate Revocation List \(CRL\)](PcaUsingCrl.md)
+ [Creating an Audit Report for Your Private CA](PcaAuditReport.md)
+ [Configure Access to ACM Private CA](PcaAuthAccess.md)