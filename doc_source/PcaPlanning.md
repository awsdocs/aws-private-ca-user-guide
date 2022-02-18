# Planning your ACM Private CA deployment<a name="PcaPlanning"></a>

ACM Private CA gives you complete, cloud\-based control over your organization's private PKI \(public key infrastructure\), extending from a root certificate authority \(CA\), through subordinate CAs, to end\-entity certificates\. Thorough planning is essential for a PKI that is secure, maintainable, extensible, and suited to your organization's needs\. This section provides guidance on designing a CA hierarchy, managing your private CA and private end\-entity certificate lifecycles, and applying best practices for security\.

This section describes how to prepare ACM Private CA for use before you create a private certificate authority \(CA\)\. It also explains the option to add revocation support through Online Certificate Status Protocol \(OCSP\) or a certificate revocation list \(CRL\)\. 

In addition, you should determine whether your organization prefers to host its private root CA credentials on premises rather than with AWS\. In that case, you need to set up and secure a self\-managed private PKI before using ACM Private CA\. In this scenario, you then create a subordinate CA in ACM Private CA backed by a parent CA outside of ACM Private CA\. For more information, see [Installing a subordinate CA certificate signed by an external parent CA](https://docs.aws.amazon.com/acm-pca/latest/userguide/PCACertInstall.html#InstallSubordinateExternal)\.

**Topics**
+ [Setting up your AWS account and the AWS CLI](setup-aws.md)
+ [Designing a CA hierarchy](ca-hierarchy.md)
+ [Managing the private CA lifecycle](ca-lifecycle.md)
+ [Setting up a certificate revocation method](revocation-setup.md)
+ [Planning for resilience](disaster-recovery-resilience.md)