# Configuring a Custom URL for ACM Private CA OCSP<a name="ocsp-customize"></a>

**Note**  
This topic is for customers who want to customize the public URL of the OCSP responder endpoint for branding or other purposes\. If you plan to use the default configuration of ACM Private CA managed OCSP, you can skip this topic and follow the configuration instructions in [Configure revocation](Create-CA-console.md#PcaCreateRevocation)\.

By default, when you enable OCSP for ACM Private CA, each certificate that you issue contains the URL for the AWS OCSP responder\. This allows clients requesting a cryptographically secure connection to send OCSP validation queries directly to AWS\. However, in some cases it might be preferable to state a different URL in your certificates while still ultimately submitting OCSP queries to AWS\.

**Note**  
For information about using a certificate revocation list \(CRL\) as an alternative or a supplement to OCSP, see [Configure revocation](Create-CA-console.md#PcaCreateRevocation) and [Planning a certificate revocation list \(CRL\)](crl-planning.md)\.

Three elements are involved in configuring a custom URL for OCSP\.
+ **CA configuration** – Specify a custom OCSP URL in the `RevocationConfiguration` for your CA as described in [Example 2: Create a CA with OCSP and a custom CNAME enabled](Create-CA-CLI.md#Create-CA-OSCP-custom-CNAME) in [Procedure for creating a CA \(CLI\) ](Create-CA-CLI.md)\.
+ **DNS** – Add a CNAME record to your domain configuration to map the URL appearing in the certificates to a proxy server URL\. For more information, see [Example 2: Create a CA with OCSP and a custom CNAME enabled](Create-CA-CLI.md#Create-CA-OSCP-custom-CNAME) in [Procedure for creating a CA \(CLI\) ](Create-CA-CLI.md)\.
+ **Forwarding proxy server** – Set up a proxy server that can transparently forward OCSP traffic that it receives to the AWS OCSP responder\.

The following diagram illustrates how these elements work together\.

![\[Custom OCSP topology\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/ocsp.png)

As shown in the diagram, the customized OCSP validation process involves the following steps:

1. Client queries DNS for the target domain\.

1. Client receives the target IP\.

1. Client opens a TCP connection with target\.

1. Client receives target TLS certificate\.

1. Client queries DNS for the OCSP domain listed in the certificate\.

1. Client receives proxy IP\.

1. Client sends OCSP query to proxy\.

1. Proxy forwards query to the OCSP responder\.

1. Responder returns certificate status to the proxy\.

1. Proxy forwards certificate status to the client\.

1. If certificate is valid, client begins TLS handshake\.

**Tip**  
This example can be implemented using [Amazon CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/) and [Amazon Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/) after you have configured a CA as described above\.  
In CloudFront, create a distribution and configure it as follows:  
Create an alternate name that matches your custom CNAME\.
Bind your certificate to it\.
Set ocsp\.acm\-pca\.*<region>*\.amazonaws\.com as the origin\.
Apply the `Managed-CachingDisabled` policy\.
Set **Viewer protocol policy** to **HTTP and HTTPS**\.
Set **Allowed HTTP methods** to **GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE**\.
In Route 53, create a DNS record that maps your custom CNAME to the URL of the CloudFront distribution\.