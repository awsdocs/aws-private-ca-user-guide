# What Is ACM PCA?<a name="PcaWelcome"></a>

AWS Certificate Manager Private Certificate Authority is a managed private CA service with which you can easily and securely manage your certificate authority infrastructure and your private certificates\. ACM PCA provides a highly available private CA service without the investment and maintenance costs of operating your own certificate authority\. ACM PCA extends ACM certificate management to private certificates, enabling you to manage public and private certificates in one console\. 

Private certificates identify resources within an organization\. Examples include clients, servers, applications, services, devices, and users\. In establishing a secure encrypted communications channel, each resource endpoint uses a certificate and cryptographic techniques to prove its identity to another endpoint\. Internal API endpoints, web servers, VPN users, IoT devices, and many other applications use private certificates to establish encrypted communication channels that are necessary for their secure operation\. For more information, see [Concepts](PcaConcepts.md)\. 

Both public and private certificates help customers identify resources on networks and secure communication between these resources\. Public certificates identify resources on the public internet whereas private certificates do so for private networks\. One key difference is that applications and browsers trust public certificates by default whereas an administrator must explicitly configure applications to trust private certificates\. Public CAs, the entities that issue public certificates, must follow strict rules, provide operational visibility, and meet security standards imposed by the browser and operating system vendors\. Private CAs are managed by private organizations, and private CA administrators can make their own rules for issuing private certificates\. These include practices for issuing certificates and what information a certificate can include\. 

To get started using ACM PCA, you must have an intermediate or root CA available for your organization\. This might be an on–premises CA, or one that is in the cloud, or one that is commercially available\. Create your private CA and then use your organization's CA to create and sign the private CA certificate\. After the certificate is signed, import it back into ACM PCA For more information about the steps you must follow to create a private CA, see [Getting Started](PcaGetStarted.md)\. 

Once you have a private CA, you can do the following:
+ [Issue private certificates](PcaIssueCert.md)
+ [Create a certificate revocation list \(CRL\)](PcaUsingCrl.md)
+ [Revoke issued certificates](PcaRevokeCert.md)
+ [Retrieve issued or revoked certificates](PcaGetCert.md)
+ [Create audit reports that contain issuance and revocation information](PcaAuditReport.md)
+ [Change the status of your private CA](PCAUpdateCA.md)
+ [Add tags to your private CA](PcaCaTagging.md)
+ [Delete your private CA](PCADeleteCA.md)
+ [Restore your private CA](PCARestoreCA.md)

The primary reason to create a private CA is, of course, to issue private certificates\. You can use ACM PCA to do so, or you can use AWS Certificate Manager \(ACM\)\. ACM PCA is tightly integrated with ACM\. Each service provides unique benefits\. 

The AWS Certificate Manager service:
+ Provides a console for you to use to request and manage private certificates\.
+ Manages the private keys that are associated with your certificates\.
+ Renews private certificates that ACM manages\.
+ Enables you to export your private certificates for use anywhere\.
+ Enables you to deploy your certificates with other integrated AWS services\.

ACM PCA allows you to:
+ Create a certificate with any subject name to identify anything you want\.
+ Use any supported private key algorithm and key length\.
+ Use any supported signing algorithm\.
+ Avoid constraints imposed on public certificates and CAs\.

**Topics**
+ [Concepts](PcaConcepts.md)
+ [Regions](PcaRegions.md)
+ [Integrated Services](PcaIntegratedServices.md)
+ [Limits](PcaLimits.md)
+ [Security](PcaSecurity.md)
+ [Best Practices](PcaBestPractices.md)
+ [CloudWatch Metrics](PcaCloudWatch.md)
+ [Pricing](PcaPricing.md)