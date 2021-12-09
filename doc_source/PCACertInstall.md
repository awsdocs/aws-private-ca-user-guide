# Creating and installing the CA certificate<a name="PCACertInstall"></a>

Complete the following procedures to create and install your private CA certificate\. Your CA will then be ready to use\.

ACM Private CA supports three scenarios for installing a CA certificate:
+ Installing a certificate for a root CA hosted by ACM Private CA
+ Installing a subordinate CA certificate whose parent authority is hosted by ACM Private CA
+ Installing a subordinate CA certificate whose parent authority is externally hosted

 The following sections describe procedures for each scenario\. The console procedures begin on the console page **Private CAs**\.

## If you are installing a root CA certificate<a name="InstallRoot"></a>

You can install a root CA certificate from the AWS Management Console or the AWS CLI\.

**To create and install a certificate for your private root CA \(console\)**

1. Begin as required by the following conditions:
   + If you previously created a root CA and chose **Install CA certificate** from the **Success\!** window, you were sent directly to the **Install root CA certificate** wizard\.
   + If you postponed certificate installation, open the ACM Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\. On the **List certificate authorities** page, choose a root CA with status **Pending Certificate** or **Active**\. Then choose **Actions**, **Install CA Certificate** to open the **Install root CA certificate** wizard\.

1. In the section **Specify the root CA certificate parameters**, specify the following certificate parameters:
   + **Validity value** and **Validity type** — Specifies the time until the CA certificate expires\. The value is an integer; the type is **Years**, **Months**, or **Days**\. The ACM Private CA default validity period for a root CA certificate is 10 years\.
   + **Signature algorithm** — Specifies the signing algorithm to use when the root CA issues new certificates\.

   Choose **Next**\.

1. On the **Review, generate, and install root CA certificate** page, confirm that the configuration is correct and choose **Confirm and install**\. ACM Private CA exports a CSR for your CA and issues a self\-signed root CA certificate using your CA and a root CA [template](UsingTemplates.md)\. ACM Private CA then imports the self\-signed root CA certificate\.

   The **List certificate authorities** page displays the status of the installation \(success or failure\) at the top\. If the installation was successful, the newly completed root CA displays a status of **Active** in the list\.

**To create and install a certificate for your private root CA \(AWS CLI\)**

1. Generate a certificate signing request \(CSR\)\.

   ```
   $ aws acm-pca get-certificate-authority-csr \
        --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
        --output text \
        --endpoint https://acm-pca.amazonaws.com --region region > ca.csr
   ```

   The resulting file `ca.csr`, a PEM file encoded in base64 format, has the following appearance\.

   ```
   -----BEGIN CERTIFICATE REQUEST-----
   MIIC1DCCAbwCAQAwbTELMAkGA1UEBhMCVVMxFTATBgNVBAoMDEV4YW1wbGUgQ29y
   cDEOMAwGA1UECwwFU2FsZXMxCzAJBgNVBAgMAldBMRgwFgYDVQQDDA93d3cuZXhh
   bXBsZS5jb20xEDAOBgNVBAcMB1NlYXR0bGUwggEiMA0GCSqGSIb3DQEBAQUAA4IB
   DwAwggEKAoIBAQDD+7eQChWUO2m6pHslI7AVSFkWvbQofKIHvbvy7wm8VO9/BuI7
   LE/jrnd1jGoyI7jaMHKXPtEP3uNlCzv+oEza07OjgjqPZVehtA6a3/3vdQ1qCoD2
   rXpv6VIzcq2onx2X7m+Zixwn2oY1l1ELXP7I5g0GmUStymq+pY5VARPy3vTRMjgC
   JEiz8w7VvC15uIsHFAWa2/NvKyndQMPaCNft238wesV5s2cXOUS173jghIShg99o
   ymf0TRUgvAGQMCXvsW07MrP5VDmBU7k/AZ9ExsUfMe2OB++fhfQWr2N7/lpC4+DP
   qJTfXTEexLfRTLeLuGEaJL+c6fMyG+Yk53tZAgMBAAGgIjAgBgkqhkiG9w0BCQ4x
   EzARMA8GA1UdEwEB/wQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEBAA7xxLVI5s1B
   qmXMMT44y1DZtQx3RDPanMNGLGO1TmLtyqqnUH49Tla+2p7nrl0tojUf/3PaZ52F
   QN09SrFk8qtYSKnMGd5PZL0A+NFsNW+w4BAQNKlg9m617YEsnkztbfKRloaJNYoA
   HZaRvbA0lMQ/tU2PKZR2vnao444Ugm0O/t3jx5rj817b31hQcHHQ0lQuXV2kyTrM
   ohWeLf2fL+K0xJ9ZgXD4KYnY0zarpreA5RBeO5xs3Ms+oGWc13qQfMBx33vrrz2m
   dw5iKjg71uuUUmtDV6ewwGa/VO5hNinYAfogdu5aGuVbnTFT3n45B8WHz2+9r0dn
   bA7xUel1SuQ=
   -----END CERTIFICATE REQUEST-----
   ```

   You can use [OpenSSL](https://www.openssl.org/) to view and verify the contents of the CSR\.

   ```
   openssl req -text -noout -verify -in ca.csr
   ```

   This yields output similar to the following\.

   ```
   verify OK
   Certificate Request:
       Data:
           Version: 0 (0x0)
           Subject: C=US, O=Example Corp, OU=Sales, ST=WA, CN=www.example.com, L=Seattle
           Subject Public Key Info:
               Public Key Algorithm: rsaEncryption
                   Public-Key: (2048 bit)
                   Modulus:
                       00:c3:fb:b7:90:0a:15:94:3b:69:ba:a4:7b:25:23:
                       b0:15:48:59:16:bd:b4:28:7c:a2:07:bd:bb:f2:ef:
                       09:bc:54:ef:7f:06:e2:3b:2c:4f:e3:ae:77:75:8c:
                       6a:32:23:b8:da:30:72:97:3e:d1:0f:de:e3:65:0b:
                       3b:fe:a0:4c:da:d3:b3:a3:82:3a:8f:65:57:a1:b4:
                       0e:9a:df:fd:ef:75:0d:6a:0a:80:f6:ad:7a:6f:e9:
                       52:33:72:ad:a8:9f:1d:97:ee:6f:99:8b:1c:27:da:
                       86:35:97:51:0b:5c:fe:c8:e6:0d:06:99:44:ad:ca:
                       6a:be:a5:8e:55:01:13:f2:de:f4:d1:32:38:02:24:
                       48:b3:f3:0e:d5:bc:2d:79:b8:8b:07:14:05:9a:db:
                       f3:6f:2b:29:dd:40:c3:da:08:d7:ed:db:7f:30:7a:
                       c5:79:b3:67:17:39:44:b5:ef:78:e0:84:84:a1:83:
                       df:68:ca:67:f4:4d:15:20:bc:01:90:30:25:ef:b1:
                       6d:3b:32:b3:f9:54:39:81:53:b9:3f:01:9f:44:c6:
                       c5:1f:31:ed:8e:07:ef:9f:85:f4:16:af:63:7b:fe:
                       5a:42:e3:e0:cf:a8:94:df:5d:31:1e:c4:b7:d1:4c:
                       b7:8b:b8:61:1a:24:bf:9c:e9:f3:32:1b:e6:24:e7:
                       7b:59
                   Exponent: 65537 (0x10001)
           Attributes:
           Requested Extensions:
               X509v3 Basic Constraints: critical
                   CA:TRUE
       Signature Algorithm: sha256WithRSAEncryption
            0e:f1:c4:b5:48:e6:cd:41:aa:65:cc:31:3e:38:cb:50:d9:b5:
            0c:77:44:33:da:9c:c3:46:2c:63:b5:4e:62:ed:ca:aa:a7:50:
            7e:3d:4e:56:be:da:9e:e7:ae:5d:2d:a2:35:1f:ff:73:da:67:
            9d:85:40:dd:3d:4a:b1:64:f2:ab:58:48:a9:cc:19:de:4f:64:
            bd:00:f8:d1:6c:35:6f:b0:e0:10:10:34:a9:60:f6:6e:b5:ed:
            81:2c:9e:4c:ed:6d:f2:91:96:86:89:35:8a:00:1d:96:91:bd:
            b0:34:94:c4:3f:b5:4d:8f:29:94:76:be:76:a8:e3:8e:14:82:
            6d:0e:fe:dd:e3:c7:9a:e3:f3:5e:db:df:58:50:70:71:d0:d2:
            54:2e:5d:5d:a4:c9:3a:cc:a2:15:9e:2d:fd:9f:2f:e2:b4:c4:
            9f:59:81:70:f8:29:89:d8:d3:36:ab:a6:b7:80:e5:10:5e:3b:
            9c:6c:dc:cb:3e:a0:65:9c:d7:7a:90:7c:c0:71:df:7b:eb:af:
            3d:a6:77:0e:62:2a:38:3b:d6:eb:94:52:6b:43:57:a7:b0:c0:
            66:bf:54:ee:61:36:29:d8:01:fa:20:76:ee:5a:1a:e5:5b:9d:
            31:53:de:7e:39:07:c5:87:cf:6f:bd:af:47:67:6c:0e:f1:51:
            e9:75:4a:e4
   ```

1. Using the CSR from the previous step as the argument for the `--csr` parameter, issue the root certificate\.
**Note**  
If you are using AWS CLI version 1\.6\.3 or later, use the prefix `fileb://` when specifying the required input file\. This ensures that ACM Private CA parses the Base64\-encoded data correctly\.

   ```
   $ aws acm-pca issue-certificate \
        --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
        --csr file://ca.csr \
        --signing-algorithm SHA256WITHRSA \
        --template-arn arn:aws:acm-pca:::template/RootCACertificate/V1 \
        --validity Value=365,Type=DAYS
   ```

1. Retrieve the root certificate\.

   ```
   $ aws acm-pca get-certificate \
   	--certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
   	--certificate-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID \
   	--output text > cert.pem
   ```

   The resulting file `cert.pem`, a PEM file encoded in base64 format, has the following appearance\.

   ```
   -----BEGIN CERTIFICATE-----
   MIIDpzCCAo+gAwIBAgIRAIIuOarlQETlUQEOZJGZYdIwDQYJKoZIhvcNAQELBQAw
   bTELMAkGA1UEBhMCVVMxFTATBgNVBAoMDEV4YW1wbGUgQ29ycDEOMAwGA1UECwwF
   U2FsZXMxCzAJBgNVBAgMAldBMRgwFgYDVQQDDA93d3cuZXhhbXBsZS5jb20xEDAO
   BgNVBAcMB1NlYXR0bGUwHhcNMjEwMzA4MTU0NjI3WhcNMjIwMzA4MTY0NjI3WjBt
   MQswCQYDVQQGEwJVUzEVMBMGA1UECgwMRXhhbXBsZSBDb3JwMQ4wDAYDVQQLDAVT
   YWxlczELMAkGA1UECAwCV0ExGDAWBgNVBAMMD3d3dy5leGFtcGxlLmNvbTEQMA4G
   A1UEBwwHU2VhdHRsZTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAMP7
   t5AKFZQ7abqkeyUjsBVIWRa9tCh8oge9u/LvCbxU738G4jssT+Oud3WMajIjuNow
   cpc+0Q/e42ULO/6gTNrTs6OCOo9lV6G0Dprf/e91DWoKgPatem/pUjNyraifHZfu
   b5mLHCfahjWXUQtc/sjmDQaZRK3Kar6ljlUBE/Le9NEyOAIkSLPzDtW8LXm4iwcU
   BZrb828rKd1Aw9oI1+3bfzB6xXmzZxc5RLXveOCEhKGD32jKZ/RNFSC8AZAwJe+x
   bTsys/lUOYFTuT8Bn0TGxR8x7Y4H75+F9BavY3v+WkLj4M+olN9dMR7Et9FMt4u4
   YRokv5zp8zIb5iTne1kCAwEAAaNCMEAwDwYDVR0TAQH/BAUwAwEB/zAdBgNVHQ4E
   FgQUaW3+r328uTLokog2TklmoBK+yt4wDgYDVR0PAQH/BAQDAgGGMA0GCSqGSIb3
   DQEBCwUAA4IBAQAXjd/7UZ8RDE+PLWSDNGQdLemOBTcawF+tK+PzA4Evlmn9VuNc
   g+x3oZvVZSDQBANUz0b9oPeo54aE38dW1zQm2qfTab8822aqeWMLyJ1dMsAgqYX2
   t9+u6w3NzRCw8Pvz18V69+dFE5AeXmNP0Z5/gdz8H/NSpctjlzopbScRZKCSlPid
   Rf3ZOPm9QP92YpWyYDkfAU04xdDo1vR0MYjKPkl4LjRqSU/tcCJnPMbJiwq+bWpX
   2WJoEBXB/p15Kn6JxjI0ze2SnSI48JZ8it4fvxrhOo0VoLNIuCuNXJOwU17Rdl1W
   YJidaq7je6k18AdgPA0Kh8y1XtfUH3fTaVw4
   -----END CERTIFICATE-----
   ```

   You can use OpenSSL to view and verify the contents of the certificate\.

   ```
   openssl x509 -in cert.pem -text -noout
   ```

   This yields output similar to the following\.

   ```
   Certificate:
       Data:
           Version: 3 (0x2)
           Serial Number:
               82:2e:39:aa:e5:40:44:e5:51:01:0e:64:91:99:61:d2
       Signature Algorithm: sha256WithRSAEncryption
           Issuer: C=US, O=Example Corp, OU=Sales, ST=WA, CN=www.example.com, L=Seattle
           Validity
               Not Before: Mar  8 15:46:27 2021 GMT
               Not After : Mar  8 16:46:27 2022 GMT
           Subject: C=US, O=Example Corp, OU=Sales, ST=WA, CN=www.example.com, L=Seattle
           Subject Public Key Info:
               Public Key Algorithm: rsaEncryption
                   Public-Key: (2048 bit)
                   Modulus:
                       00:c3:fb:b7:90:0a:15:94:3b:69:ba:a4:7b:25:23:
                       b0:15:48:59:16:bd:b4:28:7c:a2:07:bd:bb:f2:ef:
                       09:bc:54:ef:7f:06:e2:3b:2c:4f:e3:ae:77:75:8c:
                       6a:32:23:b8:da:30:72:97:3e:d1:0f:de:e3:65:0b:
                       3b:fe:a0:4c:da:d3:b3:a3:82:3a:8f:65:57:a1:b4:
                       0e:9a:df:fd:ef:75:0d:6a:0a:80:f6:ad:7a:6f:e9:
                       52:33:72:ad:a8:9f:1d:97:ee:6f:99:8b:1c:27:da:
                       86:35:97:51:0b:5c:fe:c8:e6:0d:06:99:44:ad:ca:
                       6a:be:a5:8e:55:01:13:f2:de:f4:d1:32:38:02:24:
                       48:b3:f3:0e:d5:bc:2d:79:b8:8b:07:14:05:9a:db:
                       f3:6f:2b:29:dd:40:c3:da:08:d7:ed:db:7f:30:7a:
                       c5:79:b3:67:17:39:44:b5:ef:78:e0:84:84:a1:83:
                       df:68:ca:67:f4:4d:15:20:bc:01:90:30:25:ef:b1:
                       6d:3b:32:b3:f9:54:39:81:53:b9:3f:01:9f:44:c6:
                       c5:1f:31:ed:8e:07:ef:9f:85:f4:16:af:63:7b:fe:
                       5a:42:e3:e0:cf:a8:94:df:5d:31:1e:c4:b7:d1:4c:
                       b7:8b:b8:61:1a:24:bf:9c:e9:f3:32:1b:e6:24:e7:
                       7b:59
                   Exponent: 65537 (0x10001)
           X509v3 extensions:
               X509v3 Basic Constraints: critical
                   CA:TRUE
               X509v3 Subject Key Identifier:
                   69:6D:FE:AF:7D:BC:B9:32:E8:92:88:36:4E:49:66:A0:12:BE:CA:DE
               X509v3 Key Usage: critical
                   Digital Signature, Certificate Sign, CRL Sign
       Signature Algorithm: sha256WithRSAEncryption
            17:8d:df:fb:51:9f:11:0c:4f:8f:2d:64:83:34:64:1d:2d:e9:
            8e:05:37:1a:c0:5f:ad:2b:e3:f3:03:81:2f:96:69:fd:56:e3:
            5c:83:ec:77:a1:9b:d5:65:20:d0:04:03:54:cf:46:fd:a0:f7:
            a8:e7:86:84:df:c7:56:d7:34:26:da:a7:d3:69:bf:3c:db:66:
            aa:79:63:0b:c8:9d:5d:32:c0:20:a9:85:f6:b7:df:ae:eb:0d:
            cd:cd:10:b0:f0:fb:f3:d7:c5:7a:f7:e7:45:13:90:1e:5e:63:
            4f:d1:9e:7f:81:dc:fc:1f:f3:52:a5:cb:63:97:3a:29:6d:27:
            11:64:a0:92:94:f8:9d:45:fd:d9:38:f9:bd:40:ff:76:62:95:
            b2:60:39:1f:01:4d:38:c5:d0:e8:d6:f4:74:31:88:ca:3e:49:
            78:2e:34:6a:49:4f:ed:70:22:67:3c:c6:c9:8b:0a:be:6d:6a:
            57:d9:62:68:10:15:c1:fe:9d:79:2a:7e:89:c6:32:34:cd:ed:
            92:9d:22:38:f0:96:7c:8a:de:1f:bf:1a:e1:3a:8d:15:a0:b3:
            48:b8:2b:8d:5c:93:b0:53:5e:d1:76:5d:56:60:98:9d:6a:ae:
            e3:7b:a9:35:f0:07:60:3c:0d:0a:87:cc:b5:5e:d7:d4:1f:77:
            d3:69:5c:38
   ```

1. Import the root CA certificate to install it on the CA\.
**Note**  
If you are using AWS CLI version 1\.6\.3 or later, use the prefix `fileb://` when specifying the required input file\. This ensures that ACM Private CA parses the Base64\-encoded data correctly\.

   ```
   $ aws acm-pca import-certificate-authority-certificate \
        --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
        --certificate file://cert.pem
   ```

Inspect the new status of the CA\.

```
$ aws acm-pca describe-certificate-authority \
	--certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
	--output json
```

The status now appears as ACTIVE\.

```
{
    "CertificateAuthority": {
        "Arn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
        "CreatedAt": "2021-03-05T14:24:12.867000-08:00",
        "LastStateChangeAt": "2021-03-08T12:37:14.235000-08:00",
        "Type": "ROOT",
        "Serial": "serial_number",
        "Status": "ACTIVE",
        "NotBefore": "2021-03-08T07:46:27-08:00",
        "NotAfter": "2022-03-08T08:46:27-08:00",
        "CertificateAuthorityConfiguration": {
            "KeyAlgorithm": "RSA_2048",
            "SigningAlgorithm": "SHA256WITHRSA",
            "Subject": {
                "Country": "US",
                "Organization": "Example Corp",
                "OrganizationalUnit": "Sales",
                "State": "WA",
                "CommonName": "www.example.com",
                "Locality": "Seattle"
            }
        },
        "RevocationConfiguration": {
            "CrlConfiguration": {
                "Enabled": true,
                "ExpirationInDays": 7,
                "CustomCname": "alternative.example.com",
                "S3BucketName": "DOC-EXAMPLE-BUCKET1"
            },
            "OcspConfiguration": {
                "Enabled": false
            }
        }
    }
}
```

## If you are installing a subordinate CA certificate hosted by ACM Private CA<a name="InstallSubordinateInternal"></a>

You can use the AWS Management Console to create and install a certificate for your ACM Private CA hosted subordinate CA\.

**To create and install a certificate for your ACM Private CA hosted subordinate CA**

1. Begin as required by the following conditions:
   + If you previously created a subordinate CA and chose **Install CA certificate** from the **Success\!** window, you were sent directly to the **Install subordinate CA certificate** wizard\.
   + If you postponed certificate installation, open the ACM Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\. On the **List certificate authorities** page, choose a subordinate CA with status **Pending Certificate** or **Active**\. Then choose **Actions**, **Install CA Certificate** to open the **Install subordinate CA certificate** wizard\.

1. On the **Install subordinate CA certificate** page, choose **ACM Private CA** to install a certificate that is managed by ACM Private CA\. Then choose **Next**\.

1. On the **Configure CA certificate** page, under **Select parent ACM Private CA**, choose a CA from the **Parent private CA** list\.

1. Under **Specify the subordinate CA certificate parameters**, specify the following certificate parameters:
   + **Validity value** and **Validity type** — Specifies the time until the CA certificate expires\. The value is an integer; the type is **Years**, **Months**, or **Days**\. The ACM Private CA default validity period for a root CA certificate is 10 years\.
**Note**  
The expiration date of the subordinate CA certificate cannot be later than the expiration date of the parent CA certificate\.
   + **Signature algorithm** — This specifies the signing algorithm to use when the subordinate CA issues new certificates\. Only algorithms compatible with the parent CA certificate are offered as options\.
   + **Path length** — The number of trust layers that the subordinate CA can add when signing new certificates\. A path length of zero \(the default\) means that only end\-entity certificates, and not CA certificates, can be created\. A path length of one or more means that the subordinate CA may issue certificates to create additional CAs subordinate to it\.
   + **Template ARN** — The ARN of the configuration template for this CA certificate\. The template changes if you change the specified **Path length**\. If you create a certificate using the CLI [issue\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/issue-certificate.html) command or API [IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html) action, you must specify the ARN manually\. For information about available CA certificate templates, see [Understanding certificate templates](UsingTemplates.md)\.

   Choose **Next**\.

1. On the **Review and generate** page, confirm that your configuration is correct and choose **Confirm and install**\. ACM Private CA exports a CSR for your subordinate CA, issues a CA certificate from the parent CA and template you selected, and imports the CA certificate\. 

## Installing a subordinate CA certificate signed by an external parent CA<a name="InstallSubordinateExternal"></a>

After you create a subordinate private CA as described in [Procedure for creating a CA \(console\)](Create-CA-console.md) or [Procedure for creating a CA \(CLI\) ](Create-CA-CLI.md), you have the option of activating it by installing a CA certificate signed by an external signing authority\. Signing your subordinate CA certificate with an external CA requires that you first set up the external signing authority, or arrange for the use of one\. 

**Note**  
Procedures for creating or obtaining an external CA are outside the scope of this guide\.

When you have the subordinate CA and the external parent CA, you need to complete the following tasks:

1. Obtain a certificate signing request \(CSR\) from ACM Private CA\.

1. Submit the CSR to your external signing authority and obtain a signed CA certificate along with any chain certificates\.

1. Import the CA certificate and chain into ACM Private CA to activate your subordinate CA\.

For detailed procedures, see [Signing private CA certificates with an external CA](PcaExternalRoot.md)\.