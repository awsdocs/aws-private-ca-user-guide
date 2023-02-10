# Setting up a certificate revocation method<a name="revocation-setup"></a>

As you plan your private PKI with AWS Private CA, you should consider how to handle situations where you no longer wish endpoints to trust an issued certificate, such as when the private key of an endpoint is exposed\. The common approaches to this problem are to use short\-lived certificates or to configure certificate revocation\. Short\-lived certificates expire in such a short period of time, in hours or days, that revocation makes no sense, with the certificate becoming invalid in about the same time it takes to notify an endpoint of revocation\. This section describes the revocation options for AWS Private CA customers, including configuration and best practices\.

Customers looking for a revocation method can choose Online Certificate Status Protocol \(OCSP\), certificate revocation lists \(CRLs\), or both\.

**Note**  
If you create your CA without configuring revocation, you can always configure it later\. For more information, see [Updating your private CA](PCAUpdateCA.md)\. 
+ **Online Certificate Status Protocol \(OCSP\)**

  AWS Private CA provides a fully managed OCSP solution to notify endpoints that certificates have been revoked without the need for customers to operate infrastructure themselves\. Customers can enable OCSP on new or existing CAs with a single operation using the AWS Private CA console, the API, the CLI, or through AWS CloudFormation\. Whereas CRLs are stored and processed on the endpoint and can become stale, OCSP storage and processing requirements are handled synchronously on the responder backend\.

  When you enable OCSP for a CA, AWS Private CA includes the URL of the OCSP responder in the *Authority Information Access* \(AIA\) extension of each new certificate issued\. The extension allows clients such as web browsers to query the responder and determine whether an end\-entity or subordinate CA certificate can be trusted\. The responder returns a status message that is cryptographically signed to assure its authenticity\. 

  The AWS Private CA OCSP responder is compliant with [RFC 5019](https://datatracker.ietf.org/doc/html/rfc5019)\.

  **OCSP considerations**
  + OCSP status messages are signed using the same signing algorithm that the issuing CA was configured to use\. CAs created in the AWS Private CA console use the SHA256WITHRSA signature algorithm by default\. Other supported algorithms can be found in the [CertificateAuthorityConfiguration](https://docs.aws.amazon.com/privateca/latest/APIReference/API_CertificateAuthorityConfiguration.html) API documentation\.
  + [APIPassthrough and CSRPassthrough](https://docs.aws.amazon.com/privateca/latest/userguide/UsingTemplates.html#template-varieties) certificate templates will not work with the AIA extension if the OCSP responder is enabled\.
  + The endpoint of the managed OCSP service is accessible on the public internet\. Customers who want OCSP but prefer not to have a public endpoint will need to operate their own OCSP infrastructure\.
+ **Certificate Revocation Lists \(CRLs\)**

  A CRL contains a list of revoked certificates\. When you configure a CA to generate CRLs, AWS Private CA includes the *CRL Distribution Points* extension in each new certificate issued\. This extension provides the URL for the CRL\. The extension allows clients such as web browsers to query the CRL and determine whether an end\-entity or subordinate CA certificate can be trusted\. 

Because a client must download CRLs and process them locally, their use is more memory\-intensive than OCSP\. CRLs may consume less network bandwidth because the list of CRLs is downloaded and cached, compared with OCSP which checks revocation status for each new connection attempt\.

**Note**  
Both OCSP and CRLs exhibit some delay between revocation and the availability of the status change\.  
OCSP responses may take up to 60 minutes to reflect the new status when you revoke a certificate\. In general, OCSP tends to support faster distribution of revocation information because, unlike CRLs which can be cached by clients for days, OCSP responses are typically not cached by clients\.
A CRL is typically updated approximately 30 minutes after a certificate is revoked\. If for any reason a CRL update fails, AWS Private CA makes further attempts every 15 minutes\.

## General requirements for revocation configurations<a name="revocation-requirements"></a>

The following requirements apply to all revocation configurations\.
+ A configuration disabling CRLs or OCSP must contain only the `Enabled=False` parameter, and will fail if other parameters such as `CustomCname` or `ExpirationInDays` are included\.
+ In a CRL configuration, the `S3BucketName` parameter must conform to [Amazon Simple Storage Service bucket naming rules](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html)\.
+ A configuration containing a custom Canonical Name \(CNAME\) parameter for CRLs or OCSP must conform to [RFC7230](https://www.ietf.org/rfc/rfc7230.txt) restrictions on the use of special characters in a CNAME\. 
+ In a CRL or OCSP configuration, the value of a CNAME parameter must not include a protocol prefix such as "http://" or "https://"\.

**Topics**
+ [General requirements for revocation configurations](#revocation-requirements)
+ [Planning a certificate revocation list \(CRL\)](crl-planning.md)
+ [Configuring a Custom URL for AWS Private CA OCSP](ocsp-customize.md)