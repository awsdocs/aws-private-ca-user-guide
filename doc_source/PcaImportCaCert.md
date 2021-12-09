# Step 3: Import your private CA certificate into ACM Private CA<a name="PcaImportCaCert"></a>

After you [create your private subordinate CA](create-CA.md), [obtain the certificate signing request \(CSR\)](PcaGetCsr.md), and have your external root or subordinate CA [sign the CA certificate](PcaSignCert.md), you must import the signed certificate into ACM Private CA\. After signing and importing the certificate, you can use your private subordinate CA to issue and revoke trusted private TLS certificates\. These enable trusted communication between users, applications, computers, and other devices internal to your organization\. The certificates cannot be publicly trusted\. 

You must also obtain the certificate chain that contains the certificate of the root or subordinate CA used to sign your private CA certificate as well as any preceding certificates\. To create the chain, concatenate your root certificate, if available, and any subordinate certificates that you might have into a single file\. You can use the `cat` command \(Linux\) to do so\. Each certificate must directly certify the one preceding, and the entire chain must be PEM\-formatted\. The following example contains three certificates, but your PKI infrastructure might have more or fewer\.

```
-----BEGIN CERTIFICATE-----
Base64-encoded intermediate CA certificate 
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
Base64-encoded intermediate CA certificate 
-----END CERTIFICATE-----    
-----BEGIN CERTIFICATE-----
Base64-encoded root or intermediate CA certificate
-----END CERTIFICATE-----
```

**Note**  
ACM Private CA securely generates and stores your certificate's private key\. You never import an externally generated secret key\. 

## Importing the Private CA certificate \(console\)<a name="ImportConsole"></a>

You can import a private CA certificate using the AWS Management Console\.

**To import the CA certificate \(console\)**

1. If your console is still open to the **Import signed CA certificate** page, skip to step 7\. Otherwise, continue\.

1. Sign in to your AWS account and open the ACM Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. On the **Private certificate authorities** page, choose your subordinate CA from the list of CAs\. In the details panel below the list, the **Detailed status** is **Action required**\.

1. Choose **Actions**, **Install CA certificate**\.

1. Choose **External private CA** and **Next**\. 

1. On the **Export a certificate signing request \(CSR\)** page, choose **Next**\.

1. On the **Import signed CA certificate** page, provide the required information\.
   + For **Certificate body**, copy your signed private CA certificate into the text box or import it from a file\. 
   + For **Certificate chain**, copy the certificate chain into the text box or import it from a file\. 

   Then choose **Next**\.

1. On the **Review and install** page, choose **Confirm and install** to import the certificate\. 

## Importing the private CA certificate \(AWS CLI\)<a name="ImportCli"></a>

Before beginning, make sure that you have your signed CA certificate and your certificate chain in PEM formatted files\.

**To import the CA certificate \(AWS CLI\)**  
Use the [import\-certificate\-authority\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/import-certificate-authority-certificate.html) command to import the private CA certificate into ACM Private CA\.

```
$ aws acm-pca import-certificate-authority-certificate \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--certificate file://C:\example_ca_cert.pem \
--certificate-chain file://C:\example_ca_cert_chain.pem
```