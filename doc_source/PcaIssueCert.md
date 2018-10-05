# Issue a Private Certificate<a name="PcaIssueCert"></a>

You can request a private certificate by using ACM or by using the ACM PCA API or AWS CLI\. For more information about using ACM, see [Request a Private Certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-private.html)\. ACM has many benefits: 
+ You can use the AWS Management Console to request certificates\.
+ You to use the console to deploy your private certificates with integrated services\.
+ ACM stores the CA private keys in an AWS hardware security module \(HSM\) and uses AWS KMS to manage the keys\.
+ ACM can renew private certificates that it manages\.

You can use the ACM PCA API the AWS CLI to issue a certificate\. Private certificates that you issue by using the ACM PCA API or AWS CLI are not subject to the same restrictions as private certificates that issued by ACM\. You can use ACM PCA to do the following:
+ Create a certificate with any subject name\.
+ Use any of the supported private key algorithms and key lengths\.
+ Use any of the signing algorithms that are currently supported\.
+ Specify any validity period for your private CA and private certificates\.
+ Import your private certificates into ACM and IAM\.

**Topics**
+ [Creating a certificate signing request \(CSR\)](#IssueCertCreateCsr)
+ [Issuing a Certificate \(AWS CLI\)](#IssueCertCli)

## Creating a certificate signing request \(CSR\)<a name="IssueCertCreateCsr"></a>

Before you can issue a private certificate, you must have a certificate signing request \(CSR\)\. If you are using the ACM PCA API and AWS CLI, you can use the following OpenSSL command to create one\. The command asks for a passphrase for the private key and for details about your organization\. 

```
openssl req –new –newkey rsa:2048 –days 365 –keyout my_private_key.pem –out my_csr.csr
```

```
Generating a 2048 bit RSA private key
.......................+++
..............+++
writing new private key to 'private/my_private_key.pem'
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:US
State or Province Name (full name) []:Washington
Locality Name (eg, city) [Default City]:Seattle
Organization Name (eg, company) [Default Company Ltd]:Example
Organizational Unit Name (eg, section) []:Corporate Offices
Common Name (eg, your name or your server's hostname) []:Example Company
Email Address []:corp@www.example.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

## Issuing a Certificate \(AWS CLI\)<a name="IssueCertCli"></a>

You can use the [issue\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/issue-certificate.html) command to request a private certificate\. This command requires the Amazon Resource Name \(ARN\) of the private CA that you want to use to issue the certificate\. It also requires the CSR for the certificate that you want to issue\. You can also use the [IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html) operation\. 

**Note**  
If you use the ACM PCA API and AWS CLI to issue a private certificate, you cannot use the ACM console, CLI, or API to view or export it\. You already have the private key because you needed one to create the CSR \(see the previous discussion\)\. You can use the [get\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate.html) command to retrieve the certificate details\. You can also create an [audit report](PcaAuditReport.md) to make sure that your certificate was issued\. 

```
aws acm-pca issue-certificate \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--csr file://C:\cert_1.csr \
--signing-algorithm "SHA256WITHRSA" \
--validity Value=365,Type="DAYS" \
--idempotency-token 1234
```

This command outputs the ARN of the issued certificate\.

```
{
  "CertificateArn": "arn:aws:acm-pca:region:account:\
    certificate-authority/12345678-1234-1234-1234-123456789012/certificate/a2b95975ec6e1cd85a21c2104c5be129"
}
```