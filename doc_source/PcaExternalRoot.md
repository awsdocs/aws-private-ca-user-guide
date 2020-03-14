# Signing Private CA Certificates with an External CA<a name="PcaExternalRoot"></a>

If your private CA hierarchy's root of trust must be a CA outside of ACM Private CA, you can create and self\-sign your own root CA\. Alternatively, you can obtain a private CA certificate that is signed by an external private CA operated by your organization\. Use this externally obtained CA to sign a private subordinate CA certificate that ACM Private CA manages\. 

**Note**  
Procedures for creating or obtaining an external CA are outside the scope of this guide\.

Using an external parent CA with ACM Private CA also allows you to enforce CA name constraints\. Name constraints are defined in the internet public key infrastructure \(PKI\) standard RFC 5280\. The constraints provide a way for CA administrators to restrict subject names in certificates\. For more information, see the [Name Constraints](https://tools.ietf.org/html/rfc5280#section-4.2.1.10) section of RFC 5280\.

ACM Private CA cannot directly issue CA certificates with name constraints\. However, you can issue CA certificates that include name constraints from an external parent CA\. ACM Private CA will enforce these constraints when issuing subordinate CA and end\-entity certificates\. For more information, see [Enforcing Name Constraints on a Private CA](name_constraints.md)\.

## Get a Certificate Signing Request \(CSR\)<a name="PcaGetCsr"></a>

If you have created a private subordinate CA that you want to sign with an external CA, you must retrieve a certificate signing request \(CSR\)\. Then save it to a file\. You can do this using the AWS Management Console or the AWS CLI as discussed in the procedures that follow\. If you want to inspect the CSR, use the following OpenSSL command: 

```
openssl req -in path_to_CSR_file -text -noout
```

This command generates output similar to the following\. Notice that the **CA** extension is `TRUE`, indicating that the CSR is for a CA certificate\. 

```
Certificate Request:
    Data:
        Version: 0 (0x0)
        Subject: O=ExampleCompany, OU=Corporate Office, CN=Example CA 1
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:d4:23:51:b3:dd:01:09:01:0b:4c:59:e4:ea:81:
                    1d:7f:48:36:ef:2a:e9:45:82:ec:95:1d:c6:d7:c9:
                    7f:19:06:73:c5:cd:63:43:14:eb:c8:03:82:f8:7b:
                    c7:89:e6:8d:03:eb:b6:76:58:70:f2:cb:c3:4c:67:
                    ea:50:fd:b9:17:84:b8:60:2c:64:9d:2e:d5:7d:da:
                    46:56:38:34:a9:0d:57:77:85:f1:6f:b8:ce:73:eb:
                    f7:62:a7:8e:e6:35:f5:df:0c:f7:3b:f5:7f:bd:f4:
                    38:0b:95:50:2c:be:7d:bf:d9:ad:91:c3:81:29:23:
                    b2:5e:a6:83:79:53:f3:06:12:20:7e:a8:fa:18:d6:
                    a8:f3:a3:89:a5:a3:6a:76:da:d0:97:e5:13:bc:84:
                    a6:5c:d6:54:1a:f0:80:16:dd:4e:79:7b:ff:6d:39:
                    b5:67:56:cb:02:6b:14:c3:17:06:0e:7d:fb:d2:7e:
                    1c:b8:7d:1d:83:13:59:b2:76:75:5e:d1:e3:23:6d:
                    8a:5e:f5:85:ca:d7:e9:a3:f1:9b:42:9f:ed:8a:3c:
                    14:4d:1f:fc:95:2b:51:6c:de:8f:ee:02:8c:0c:b6:
                    3e:2d:68:e5:f8:86:3f:4f:52:ec:a6:f0:01:c4:7d:
                    68:f3:09:ae:b9:97:d6:fc:e4:de:58:58:37:09:9a:
                    f6:27
                Exponent: 65537 (0x10001)
        Attributes:
        Requested Extensions:
            X509v3 Basic Constraints:
                CA:TRUE
    Signature Algorithm: sha256WithRSAEncryption
         c5:64:0e:6c:cf:11:03:0b:b7:b8:9e:48:e1:04:45:a0:7f:cc:
         a7:fd:e9:4d:c9:00:26:c5:6e:d0:7e:69:7a:fb:17:1f:f3:5d:
         ac:f3:65:0a:96:5a:47:3c:c1:ee:45:84:46:e3:e6:05:73:0c:
         ce:c9:a0:5e:af:55:bb:89:46:21:92:7b:10:96:92:1b:e6:75:
         de:02:13:2d:98:72:47:bd:b1:13:1a:3d:bb:71:ae:62:86:1a:
         ee:ae:4e:f4:29:2e:d6:fc:70:06:ac:ca:cf:bb:ee:63:68:14:
         8e:b2:8f:e3:8d:e8:8f:e0:33:74:d6:cf:e2:e9:41:ad:b6:47:
         f8:2e:7d:0a:82:af:c6:d8:53:c2:88:a0:32:05:09:e0:04:8f:
         79:1c:ac:0d:d4:77:8e:a6:b2:5f:07:f8:1b:e3:98:d4:12:3d:
         28:32:82:b5:50:92:a4:b2:4c:28:fc:d2:73:75:75:ff:10:33:
         2c:c0:67:4b:de:fd:e6:69:1c:a8:bb:e8:31:93:07:35:69:b7:
         d6:53:37:53:d5:07:dd:54:35:74:50:50:f9:99:7d:38:b7:b6:
         7f:bd:6c:b8:e4:2a:38:e5:04:00:a8:a3:d9:e5:06:38:e0:38:
         4c:ca:a9:3c:37:6d:ba:58:38:11:9c:30:08:93:a5:62:00:18:
         d1:83:66:40
```

**Topics**
+ [Retrieving a CSR \(Console\): Method 1](#getCsrConsole1)
+ [Retrieving a CSR \(Console\): Method 2](#getCsrConsole2)
+ [Retrieving a CSR \(AWS CLI\)](#getCsrCli)

### Retrieving a CSR \(Console\): Method 1<a name="getCsrConsole1"></a>

Use this procedure if you followed the steps to [create a private CA](PcaCreateCa.md) in ACM Private CA and left the **Success** dialog box open\. 

**To retrieve a CSR \(console\): method 1**

1. Immediately after ACM Private CA has successfully created your private CA, choose **Get started**\. The ACM Private CA console returns the CSR\. You can return to this step later\. 

1. Choose **Export CSR to a file** and save it locally\.

1. Choose **Next**\.

1. Follow the instructions in [Sign Your Private CA Certificate](#PcaSignCert)\.

### Retrieving a CSR \(Console\): Method 2<a name="getCsrConsole2"></a>

Use this procedure if you followed the steps to [create a private CA](PcaCreateCa.md) in ACM Private CA and closed the **Success** dialog box\. 

**To retrieve a CSR \(console\): method 2**

1. When you are ready to continue, open the AWS Certificate Manager console and choose **Private CAs** in the left navigation pane\. 

1. Select your private CA from the list\.

1. From the **Actions** menu, choose **Import CA certificate**\. The ACM Private CA console returns the CSR\. 

1. Choose **Export CSR to a file** and save it locally\.

1. Choose **Next**\.

1. Follow the instructions in [Sign Your Private CA Certificate](#PcaSignCert)\.

### Retrieving a CSR \(AWS CLI\)<a name="getCsrCli"></a>

Use this procedure to retrieve a CSR using the AWS Command Line Interface\.

**To retrieve a CSR \(AWS CLI\)**

1. Use the [get\-certificate\-authority\-csr](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate-authority-csr.html) command to retrieve the certificate signing request \(CSR\) for your private CA\. If you want to send the CSR to your display, use the `--output text` option to eliminate CR/LF characters from the end of each line\. To send the CSR to a file, use the redirect option \(>\) followed by a file name\. 

   ```
   aws acm-pca get-certificate-authority-csr \
   --certificate-authority-arn arn:aws:acm-pca:region:account:\
   certificate-authority/12345678-1234-1234-1234-123456789012 \
   --output text
   ```

1. Follow the instructions in [Sign Your Private CA Certificate](#PcaSignCert)\.

## Sign Your Private CA Certificate<a name="PcaSignCert"></a>

After you [create](PcaCreateCa.md) your private CA using ACM Private CA and [retrieve](#PcaGetCsr) a certificate signing request \(CSR\), you must take the CSR to your external X\.509 infrastructure\. Use an intermediate or root CA to create your private CA certificate and sign it\. Signing affirms the identity of the private CA within your organization\. When you have completed this process, follow the instructions in [Import Your Private CA Certificate into ACM Private CA](#PcaImportCaCert)\. 

**Important**  
Details of your X\.509 infrastructure and the CA hierarchy within it are beyond the scope of this guide\. For more information, see [Creating and Signing a Private CA Certificate](PcaTsSignCsr.md)\. 
The validity period of a private CA is determined by the validity period you specify when you create the private CA certificate\. Set the **Not Before** and **Not After** fields\. Aside from enforcing the defined period, ACM Private CA does not restrict the lifetime of a CA\. 
If you must create a CA certificate that does not effectively expire, set the special value `99991231235959Z` in the **Not After** field\. We do not recommend this as a general practice\. 

The signed certificate is typically returned to you as a base64\-encoded PEM file or string\. This is shown by the following example\. If the certificate is encoded in a different format, you must convert it to PEM\. Various OpenSSL commands are available to perform format conversion\. 

```
-----BEGIN CERTIFICATE-----
MIIDRzCCA..................
..........9YFbLXtPgZooy2IgZ
------END CERTIFICATE------
```

You can use the OpenSSL `x509` command to view the contents of your signed PEM format certificate\. 

```
openssl x509 -in path_to_certificate_file -text -noout
```

This command outputs a certificate similar to the following example\.

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 4122 (0x101a)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=US, ST=Washington, L=Seattle, O=Example Company, OU=Corp, CN=www.example.com/emailAddress=corp@www.example.com
        Validity
            Not Before: Mar 29 19:28:43 2018 GMT
            Not After : Mar 26 19:28:43 2028 GMT
        Subject: O=Example Company, OU=Corporate Office, CN=Example Company CA 1
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:d4:23:51:b3:dd:01:09:01:0b:4c:59:e4:ea:81:
                    1d:7f:48:36:ef:2a:e9:45:82:ec:95:1d:c6:d7:c9:
                    7f:19:06:73:c5:cd:63:43:14:eb:c8:03:82:f8:7b:
                    c7:89:e6:8d:03:eb:b6:76:58:70:f2:cb:c3:4c:67:
                    ea:50:fd:b9:17:84:b8:60:2c:64:9d:2e:d5:7d:da:
                    46:56:38:34:a9:0d:57:77:85:f1:6f:b8:ce:73:eb:
                    f7:62:a7:8e:e6:35:f5:df:0c:f7:3b:f5:7f:bd:f4:
                    38:0b:95:50:2c:be:7d:bf:d9:ad:91:c3:81:29:23:
                    b2:5e:a6:83:79:53:f3:06:12:20:7e:a8:fa:18:d6:
                    a8:f3:a3:89:a5:a3:6a:76:da:d0:97:e5:13:bc:84:
                    a6:5c:d6:54:1a:f0:80:16:dd:4e:79:7b:ff:6d:39:
                    b5:67:56:cb:02:6b:14:c3:17:06:0e:7d:fb:d2:7e:
                    1c:b8:7d:1d:83:13:59:b2:76:75:5e:d1:e3:23:6d:
                    8a:5e:f5:85:ca:d7:e9:a3:f1:9b:42:9f:ed:8a:3c:
                    14:4d:1f:fc:95:2b:51:6c:de:8f:ee:02:8c:0c:b6:
                    3e:2d:68:e5:f8:86:3f:4f:52:ec:a6:f0:01:c4:7d:
                    68:f3:09:ae:b9:97:d6:fc:e4:de:58:58:37:09:9a:
                    f6:27
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                3D:5B:CC:96:FA:A5:91:37:2A:C9:97:22:F8:AF:10:A7:4E:E1:A0:6A
            X509v3 Authority Key Identifier:
                keyid:0D:CE:76:F2:E3:3B:93:2D:36:05:41:41:16:36:C8:82:BC:CB:F8:A0

            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Key Usage: critical
                Digital Signature, Certificate Sign, CRL Sign
    Signature Algorithm: sha256WithRSAEncryption
         7e:4b:7c:ca:31:b2:66:25:eb:99:26:91:e2:77:1b:7c:2c:a5:
         d5:e4:ab:c3:98:2a:a2:d7:d9:3b:4a:89:27:cd:94:5c:50:fb:
         59:00:9b:13:56:08:da:87:3c:50:e4:eb:f9:b3:35:92:f8:72:
         d9:11:f0:1e:f3:3b:2e:f5:42:12:de:46:ce:36:ab:f7:b9:2f:
         7e:dd:50:47:49:ad:a6:ee:f6:67:b3:c6:2f:6c:7a:fe:16:9c:
         47:41:81:18:cd:ff:4e:b9:66:8b:ed:04:7a:d0:ce:cb:f3:88:
         c8:89:20:68:6a:2f:6c:3d:60:56:cb:5e:d3:e0:66:8a:32:d8:
         88:19
```

## Import Your Private CA Certificate into ACM Private CA<a name="PcaImportCaCert"></a>

After you [create](PcaCreateCa.md) your private CA, [retrieve](#PcaGetCsr) the certificate signing request \(CSR\), and [sign](#PcaSignCert) the CA certificate, you must import the certificate into ACM Private CA\. After signing and importing the certificate, you can use your private CA to issue and revoke trusted private SSL/TLS certificates\. These enable trusted communication between users, applications, computers, and other devices internal to your organization\. The certificates cannot be publicly trusted\. 

You must also retrieve the certificate chain that contains the certificate of the intermediate or root CA used to sign your private CA certificate and any preceding certificates\. To create the chain, concatenate your root certificate, if available, and any subordinate certificates that you might have into a single file\. You can use the `cat` command \(Linux\) to do so\. Each certificate must directly certify the one preceding\. The following example contains three certificates, but your PKI infrastructure might have more or fewer\. 

**Note**  
Your chain must be PEM formatted\.

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

**Topics**
+ [Importing the Private CA Certificate \(Console\)](#ImportConsole)
+ [Importing the Private CA Certificate \(AWS CLI\)](#ImportCli)

### Importing the Private CA Certificate \(Console\)<a name="ImportConsole"></a>

You can import a private CA certificate using the AWS Management Console\.

**To import the CA certificate \(console\)**

1. If your console is still open to the **Import a signed certificate authority \(CA\) certificate** page, skip to step 7\. Otherwise, continue\.

1. Sign in to your AWS account and open the ACM Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Choose **Private CAs**\.

1. Select your private CA from the list\.

1. On the **Actions** menu, choose **Import CA certificate**\.

1. Choose **Next**\.

1. For **Certificate body**, copy your signed private CA certificate into the textbox or import it from a file\. 

1. For **Certificate chain**, copy the certificate chain into the textbox or import it from a file\. 

1. Choose **Next**\.

1. Choose **Confirm and Import** to import the private CA certificate\. 

### Importing the Private CA Certificate \(AWS CLI\)<a name="ImportCli"></a>

Before beginning, make sure that you have your signed CA certificate and your certificate chain in PEM formatted files\.

**To import the CA certificate \(AWS CLI\)**  
Use the [import\-certificate\-authority\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/import-certificate-authority-certificate.html) command to import the private CA certificate into ACM Private CA\.

```
aws acm-pca import-certificate-authority-certificate \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--certificate file://C:\example_ca_cert.pem \
--certificate-chain file://C:\example_ca_cert_chain.pem
```

## Retrieve a Private Certificate<a name="PcaGetCert"></a>

You can use the ACM Private CA API and AWS CLI to issue a private certificate\. If you do, you can use the AWS CLI or ACM Private CA API to retrieve that certificate\. If you used ACM to create your private CA and to request certificates, you must use ACM to export the certificate and the encrypted private key\. For more information, see [Exporting a Private Certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-export-private.html)\. 

Use the [get\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate.html) command to retrieve a private certificate\. You can also use the [GetCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificate.html) operation\. Use the `--output text` option to output the certificate without <CR><LF> pairs\. 

**Note**  
If you want to revoke a certificate, you can use the get\-certificate command to retrieve the serial number in hexadecimal format\. You can also create an audit report to retrieve the hex serial number\. For more information, see [Creating an Audit Report for Your Private CA](PcaAuditReport.md)\. 

```
aws acm-pca get-certificate \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--certificate-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012/\
certificate/6707447683a9b7f4055627ffd55cebcc \
--output text
```

This command outputs the base64 encoded PEM format certificate and the certificate chain\.

```
-----BEGIN CERTIFICATE-----
...Base64-encoded certificate...
-----END CERTIFICATE----
-----BEGIN CERTIFICATE-----
...Base64-encoded certificate...
-----END CERTIFICATE----
-----BEGIN CERTIFICATE-----
...Base64-encoded certificate...
-----END CERTIFICATE----
```

## Retrieve a Certificate Authority \(CA\) Certificate<a name="PcaGetCACert"></a>

You can use the ACM Private CA API and AWS CLI to retrieve the certificate authority \(CA\) certificate for your private CA\. Run the `[get\-certificate\-authority\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate-authority-certificate.html)` command\. You can also call the `[GetCertificateAuthorityCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificateAuthorityCertificate.html)` operation\. Use the `--output text` option to output the certificate without `<CR><LF>` pairs\. 

```
aws acm-pca get-certificate-authority-certificate \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--output text
```

This command outputs the base64 encoded PEM format certificate and the certificate chain\.

```
-----BEGIN CERTIFICATE-----
...Base64-encoded certificate...
-----END CERTIFICATE----
-----BEGIN CERTIFICATE-----
...Base64-encoded certificate...
-----END CERTIFICATE----
```