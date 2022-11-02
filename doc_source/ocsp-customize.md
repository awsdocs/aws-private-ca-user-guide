# Configuring a Custom URL for AWS Private CA OCSP<a name="ocsp-customize"></a>

**Note**  
This topic is for customers who want to customize the public URL of the OCSP responder endpoint for branding or other purposes\. If you plan to use the default configuration of AWS Private CA managed OCSP, you can skip this topic and follow the configuration instructions in [Configure revocation](Create-CA-console.md#PcaCreateRevocation)\.

By default, when you enable OCSP for AWS Private CA, each certificate that you issue contains the URL for the AWS OCSP responder\. This allows clients requesting a cryptographically secure connection to send OCSP validation queries directly to AWS\. However, in some cases it might be preferable to state a different URL in your certificates while still ultimately submitting OCSP queries to AWS\.

**Note**  
For information about using a certificate revocation list \(CRL\) as an alternative or a supplement to OCSP, see [Configure revocation](Create-CA-console.md#PcaCreateRevocation) and [Planning a certificate revocation list \(CRL\)](crl-planning.md)\.

Three elements are involved in configuring a custom URL for OCSP\.
+ **CA configuration** – Specify a custom OCSP URL in the `RevocationConfiguration` for your CA as described in [ Example 2: Create a CA with OCSP and a custom CNAME enabled  In this example, the revocation file enables customized OCSP support\. The `OcspCustomCname` parameter takes a fully qualified domain name \(FQDN\) as its value\.   When you provide an FQDN in this field, AWS Private CA inserts the FQDN into the *Authority Information Access* extension of each issued certificate in place of the default URL for the AWS OCSP responder\. When an endpoint receives a certificate containing the custom FQDN, it queries that address for an OCSP response\. For this mechanism to work, you need to take two additional actions:    Use a proxy server to forward traffic that arrives at your custom FQDN to the AWS OCSP responder\.   Add a corresponding CNAME record to your DNS database\.     For more information about implementing a complete OCSP solution using a custom CNAME, see [Configuring a Custom URL for AWS Private CA OCSP](ocsp-customize.md)\.  For example, here is a CNAME record for customized OCSP as it would appear in Amazon Route 53\.  


****  

| Record name | Type | Routing policy | Differentiator | Value/Route traffic to | 
| --- | --- | --- | --- | --- | 
|  alternative\.example\.com  | CNAME | Simple | \- | proxy\.example\.com |   The value of the CNAME must not include a protocol prefix such as "http://" or "https://"\.  **File: revoke\_config\.txt for OCSP** 

```
{
   "OcspConfiguration":{
      "Enabled":true,
      "OcspCustomCname":"alternative.example.com"
   }
}
``` **Command** 

```
$ aws acm-pca create-certificate-authority \
	--certificate-authority-configuration file://ca_config.txt \
	--revocation-configuration file://revoke_config.txt \
	--certificate-authority-type "ROOT" \
	--idempotency-token 01234567 \
	--tags Key=Name,Value=MyPCA-3
``` If successful, this command outputs the Amazon Resource Name \(ARN\) of the CA\. 

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
}
``` Use the following command to inspect the configuration of your CA\. 

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/CA_ID" \
      --output json
``` This description should contain the following section\. 

```
"RevocationConfiguration": {
   ...
   "OcspConfiguration": {
      "Enabled": true,
      "OcspCustomCname": "alternative.example.com"
   }
   ...
}
``` ](Create-CA-CLI.md#example_2) in [Procedure for creating a CA \(CLI\) ](Create-CA-CLI.md)\.
+ **DNS** – Add a CNAME record to your domain configuration to map the URL appearing in the certificates to a proxy server URL\. For more information, see [ Example 2: Create a CA with OCSP and a custom CNAME enabled  In this example, the revocation file enables customized OCSP support\. The `OcspCustomCname` parameter takes a fully qualified domain name \(FQDN\) as its value\.   When you provide an FQDN in this field, AWS Private CA inserts the FQDN into the *Authority Information Access* extension of each issued certificate in place of the default URL for the AWS OCSP responder\. When an endpoint receives a certificate containing the custom FQDN, it queries that address for an OCSP response\. For this mechanism to work, you need to take two additional actions:    Use a proxy server to forward traffic that arrives at your custom FQDN to the AWS OCSP responder\.   Add a corresponding CNAME record to your DNS database\.     For more information about implementing a complete OCSP solution using a custom CNAME, see [Configuring a Custom URL for AWS Private CA OCSP](ocsp-customize.md)\.  For example, here is a CNAME record for customized OCSP as it would appear in Amazon Route 53\.  


****  

| Record name | Type | Routing policy | Differentiator | Value/Route traffic to | 
| --- | --- | --- | --- | --- | 
|  alternative\.example\.com  | CNAME | Simple | \- | proxy\.example\.com |   The value of the CNAME must not include a protocol prefix such as "http://" or "https://"\.  **File: revoke\_config\.txt for OCSP** 

```
{
   "OcspConfiguration":{
      "Enabled":true,
      "OcspCustomCname":"alternative.example.com"
   }
}
``` **Command** 

```
$ aws acm-pca create-certificate-authority \
	--certificate-authority-configuration file://ca_config.txt \
	--revocation-configuration file://revoke_config.txt \
	--certificate-authority-type "ROOT" \
	--idempotency-token 01234567 \
	--tags Key=Name,Value=MyPCA-3
``` If successful, this command outputs the Amazon Resource Name \(ARN\) of the CA\. 

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
}
``` Use the following command to inspect the configuration of your CA\. 

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/CA_ID" \
      --output json
``` This description should contain the following section\. 

```
"RevocationConfiguration": {
   ...
   "OcspConfiguration": {
      "Enabled": true,
      "OcspCustomCname": "alternative.example.com"
   }
   ...
}
``` ](Create-CA-CLI.md#example_2) in [Procedure for creating a CA \(CLI\) ](Create-CA-CLI.md)\.
+ **Forwarding proxy server** – Set up a proxy server that can transparently forward OCSP traffic that it receives to the AWS OCSP responder\.

The following diagram illustrates how these elements work together\.

![\[Custom OCSP topology\]](http://docs.aws.amazon.com/privateca/latest/userguide/images/ocsp.png)

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