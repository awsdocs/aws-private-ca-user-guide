# Signing private CA certificates with an external CA<a name="PcaExternalRoot"></a>

If your private CA hierarchy's root of trust must be a CA outside of ACM Private CA, you can create and self\-sign your own root CA\. Alternatively, you can obtain a private CA certificate that is signed by an external private CA operated by your organization\. Use this externally obtained CA to sign a private subordinate CA certificate that ACM Private CA manages\. 

**Note**  
Procedures for creating or obtaining an external CA are outside the scope of this guide\.

Using an external parent CA with ACM Private CA also allows you to enforce CA name constraints\. Name constraints are defined in the internet public key infrastructure \(PKI\) standard RFC 5280\. The constraints provide a way for CA administrators to restrict subject names in certificates\. For more information, see the [Name Constraints](https://tools.ietf.org/html/rfc5280#section-4.2.1.10) section of RFC 5280\.

ACM Private CA cannot directly issue CA certificates with name constraints\. However, you can issue CA certificates that include name constraints from an external parent CA\. ACM Private CA will enforce these constraints when issuing subordinate CA and end\-entity certificates\. For more information, see [\(Optional\) Enforcing name constraints on an externally signed private CA](name_constraints.md)\.

**Topics**
+ [Step 1: Getting a certificate signing request \(CSR\) from ACM Private CA](PcaGetCsr.md)
+ [Step 2: Signing the private CA certificate](PcaSignCert.md)
+ [Step 3: Import your private CA certificate into ACM Private CA](PcaImportCaCert.md)
+ [\(Optional\) Enforcing name constraints on an externally signed private CA](name_constraints.md)