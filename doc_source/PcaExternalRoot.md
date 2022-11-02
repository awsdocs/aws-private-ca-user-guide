# Externally signed private CA certificates<a name="PcaExternalRoot"></a>

If your private CA hierarchy's root of trust must be a CA outside of AWS Private CA, you can create and self\-sign your own root CA\. Alternatively, you can obtain a private CA certificate that is signed by an external private CA operated by your organization\. Whatever its source, you can use this externally obtained CA to sign a private subordinate CA certificate that AWS Private CA manages\. 

**Note**  
Procedures for creating or obtaining an external trust services provider are outside the scope of this guide\.

Using an external parent CA with AWS Private CA permits you to enforce CA name constraints as defined in the [Name Constraints](https://datatracker.ietf.org/doc/html/rfc5280#section-4.2.1.10) section of RFC 5280\. Name constraints provide a way for CA administrators to restrict subject names in certificates\.

If you plan to sign a private subordinate CA certificate with an external CA, there are three tasks to complete before you have a working CA in AWS Private CA:

1. Generate a certificate signing request \(CSR\)\.

1. Submit the CSR to your external signing authority and return with a signed certificate and certificate chain\.

1. Install a signed certificate in AWS Private CA\.

The following procedures describe how to complete these tasks using either the AWS Management Console or the AWS CLI\.



**To obtain and install an externally signed CA certificate \(console\)**

1. \(Optional\) If you are not already on the CA's details page, open the AWS Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\. On the **Private certificate authorities** page, choose a subordinate CA with status **Pending certificate**, **Active**, **Disabled**, or **Expired**\.

1. Choose **Actions**, **Install CA Certificate** to open the **Install subordinate CA certificate** page\.

1. On the **Install subordinate CA certificate** page, under **Select CA type**, choose **External private CA**\.

1. Under **CSR for this CA**, the console displays the Base64\-encoded ASCII text of the CSR\. You can copy the text using the **Copy** button or you can choose **Export CSR to a file** and save it locally\.
**Note**  
The exact format of the CSR text must be preserved when coping and pasting\.

1. If you cannot immediately perform the offline steps to obtain a signed certificate from your external signing authority, you can close the page and return to it once you possess a signed certificate and a certificate chain\.

   Otherwise, if you are ready, do either of the following:
   + Paste the Base64\-encoded ASCII text of your certificate body and of your certificate chain into their respective text boxes\.
   + Choose **Upload** to load the certificate body and certificate chain from local files into their respective text boxes\.

1. Choose **Confirm and install**\.



**To obtain and install an externally signed CA certificate \(CLI\)**

1. Use the [get\-certificate\-authority\-csr](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate-authority-csr.html) command to retrieve the certificate signing request \(CSR\) for your private CA\. If you want to send the CSR to your display, use the `--output text` option to eliminate CR/LF characters from the end of each line\. To send the CSR to a file, use the redirect option \(>\) followed by a file name\. 

   ```
   $ aws acm-pca get-certificate-authority-csr \
   --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
   --output text
   ```

   After saving a CSR as a local file, you can inspect it by using the following [OpenSSL](https://www.openssl.org/) command: 

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

1. Submit the CSR to your external signing authority and obtain files containing the Base64 PEM\-encoded signed certificate and certificate chain\.

1. Use the [import\-certificate\-authority\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/import-certificate-authority-certificate.html) command to import the private CA certificate file and the chain file into AWS Private CA\.

   ```
   $ aws acm-pca import-certificate-authority-certificate \
   --certificate-authority-arn arn:aws:acm-pca:region:account:\
   certificate-authority/12345678-1234-1234-1234-123456789012 \
   --certificate file://C:\example_ca_cert.pem \
   --certificate-chain file://C:\example_ca_cert_chain.pem
   ```