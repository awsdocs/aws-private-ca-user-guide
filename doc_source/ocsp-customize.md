# Customizing Online Certificate Status Protocol \(OCSP\)<a name="ocsp-customize"></a>

Typically, after you set up Online Certificate Status Protocol \(OCSP\) validation of your ACM Private CA certificates, each new certificate issued contains the URL for the AWS OCSP responder\. This allows clients requesting a TLS connection to send validation OCSP queries directly to AWS\. However, in some cases it may be preferable to state a different URL in your certificates while still ultimately submitting OCSP queries to AWS\. This topic describes how to configure such a scenario\.

**Note**  
OCSP is not currently supported in the ACM Private CA console\. Beta OCSP users can find AWS CLI examples at [Using the AWS CLI to Create a CA](PcaCreateCa.md#Create-CA-CLI) and [Updating a CA \(AWS CLI\)](PCAUpdateCA.md#ca-update-cli)

Three elements are involved in configuring a custom OCSP reponder URL\.
+ **CA configuration** – Specify a custom OCSP URL in the `RevocationConfiguration` for your CA as described in [Example 4: Create a CA with OCSP and a custom CNAME enabled](PcaCreateCa.md#Create-CA-OSCP-custom-CNAME) in [Using the AWS CLI to Create a CA](PcaCreateCa.md#Create-CA-CLI)\.
+ **DNS** – Add a CNAME record to your domain configuration to map the URL appearing in the certificates to a proxy server URL\. For more information, see [Example 4: Create a CA with OCSP and a custom CNAME enabled](PcaCreateCa.md#Create-CA-OSCP-custom-CNAME) in [Using the AWS CLI to Create a CA](PcaCreateCa.md#Create-CA-CLI)
+ **Forwarding proxy server** – Set up a proxy server that can transparently forward OCSP traffic it receives to the AWS OCSP responder\.

The following diagram illustrates how these elements work together\.

![\[Custom OCSP topology\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/ocsp.png)

First the client connects to a target server that has TLS support\. This requires querying DNS for the target domain and opening a connection using the returned IP address \(1\-3\)\. The target responds by offering a TLS server certificate \(4\)\. The client reads the embedded OCSP URL and queries DNS again \(5\)\. This time, a CNAME record for the embedded domain returns the IP address of the proxy server \(6\)\. When the client sends an OCSP query to the proxy address, the proxy forwards the request to the AWS OCSP responder \(7\-8\)\. The responder in turn sends an OCSP status message to the client via the proxy \(9\)\. Assuming that the status is valid, the client then resumes the TLS handshake with the target server \(10\)\.

**Tip**  
This example can be implemented using Amazon CloudFront and Amazon Route 53 after you have configured a CA as described\.  
In CloudFront, create a distribution and configure it as follows:  
Create an alternate name that matches your custom CNAME\.
Bind your certificate to it\.
Set ocsp\.*<region>*\.amazonaws\.com as the origin\.
Apply the `Managed-CachingDisabled` policy\.
In Route 53, create a DNS record that maps your custom CNAME to the URL of the CloudFront distribution\.