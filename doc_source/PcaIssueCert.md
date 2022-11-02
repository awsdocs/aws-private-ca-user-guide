# Issuing private end\-entity certificates<a name="PcaIssueCert"></a>

With a private CA in place, you can request private end\-entity certificates from either AWS Certificate Manager \(ACM\) or AWS Private CA\. The capabilities of both services are compared in the following table\.


****  

|  Capability  |  ACM  |  AWS Private CA  | 
| --- | --- | --- | 
|  Issue end\-entity certificates  |  ✓ \(using `[RequestCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_RequestCertificate.html)` or the console\)  |  ✓ \(using `[IssueCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_IssueCertificate.html)`\)  | 
|  Association with load balancers and internet\-facing AWS services  |  ✓  |  Not supported  | 
| Managed certificate renewal | ✓ | Indirectly [supported](https://docs.aws.amazon.com/acm/latest/userguide/managed-renewal.html) through ACM | 
|  Console support  |  ✓  |  Not supported  | 
|  API support  |  ✓  |  ✓  | 
|  CLI support  |  ✓  |  ✓  | 

When AWS Private CA creates a certificate, it follows a template that specifies the certificate type and path length\. If no template ARN is supplied to the API or CLI statement creating the certificate, the [EndEntityCertificate/V1](UsingTemplates.md#EndEntityCertificate-V1) template is applied by default\. For more information about available certificate templates, see [Understanding certificate templates](UsingTemplates.md)\.

While ACM certificates are designed around public trust, AWS Private CA serves the needs of your private PKI\. Consequently, you can configure certificates using the AWS Private CA API and CLI in ways not permitted by ACM\. These include the following:
+ Creating a certificate with any Subject name\.
+ Using any of the [supported private key algorithms and key lengths](https://docs.aws.amazon.com/privateca/latest/userguide/supported-algorithms.html)\.
+ Using any of the [supported signing algorithms](https://docs.aws.amazon.com/privateca/latest/userguide/supported-algorithms.html)\.
+ Specifying any validity period for your private [CA](PcaCreateCa.html) and private [certificates](PcaIssueCert.html)\.

After creating a private certificate using AWS Private CA, you can [import](https://docs.aws.amazon.com/acm/latest/userguide/import-certificate-api-cli.html) it into ACM and use it with a supported AWS service\.

**Note**  
Certificates created with the procedure below, using the issue\-certificate command, or with the [IssueCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_RequestCertificate.html) API action, cannot be exported for use outside AWS\. However, you can use your private CA to sign certificates issued through ACM, and those certificates can be exported along with their secret keys\. For more information, see [Requesting a private certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-private.html) and [Exporting a private certificate](https://docs.aws.amazon.com/acm/latest/userguide/export-private.html) in the *ACM User Guide*\.

## Issuing a standard certificate \(AWS CLI\)<a name="IssueCertCli"></a>

You can use the AWS Private CA CLI command [issue\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/issue-certificate.html) or the API action [IssueCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_IssueCertificate.html) to request an end\-entity certificate\. This command requires the Amazon Resource Name \(ARN\) of the private CA that you want to use to issue the certificate\. You must also generate a certificate signing request \(CSR\) using a program such as [OpenSSL](https://www.openssl.org/)\.

If you use the AWS Private CA API or AWS CLI to issue a private certificate, the certificate is unmanaged, meaning that you cannot use the ACM console, ACM CLI, or ACM API to view or export it, and the certificate is not automatically renewed\. However, you can use the PCA [get\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate.html) command to retrieve the certificate details, and if you own the CA, you can create an [audit report](PcaAuditReport.md)\.

**Considerations when creating certificates**
+ In compliance with [RFC 5280](https://datatracker.ietf.org/doc/html/rfc5280), the length of the domain name \(technically, the Common Name\) that you provide cannot exceed 64 octets \(characters\), including periods\. To add a longer domain name, specify it in the Subject Alternative Name field, which supports names up to 253 octets in length\. 
+ If you are using AWS CLI version 1\.6\.3 or later, use the prefix `fileb://` when specifying Base64\-encoded input files such as CSRs\. This ensures that AWS Private CA parses the data correctly\.

The following OpenSSL command generates a CSR and a private key for a certificate:

```
$ openssl req -out csr.pem -new -newkey rsa:2048 -nodes -keyout private-key.pem
```

You can inspect the content of the CSR as follows:

```
$ openssl req -in csr.pem -text -noout
```

The resulting output should resemble the following abbreviated example:

```
Certificate Request:
    Data:
        Version: 0 (0x0)
        Subject: C=US, O=Big Org, CN=example.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:ca:85:f4:3a:b7:5f:e2:66:be:fc:d8:97:65:3d:
                    a4:3d:30:c6:02:0a:9e:1c:ca:bb:15:63:ca:22:81:
                    00:e1:a9:c0:69:64:75:57:56:53:a1:99:ee:e1:cd:
                    ...
                    aa:38:73:ff:3d:b7:00:74:82:8e:4a:5d:da:5f:79:
                    5a:89:52:e7:de:68:95:e0:16:9b:47:2d:57:49:2d:
                    9b:41:53:e2:7f:e1:bd:95:bf:eb:b3:a3:72:d6:a4:
                    d3:63
                Exponent: 65537 (0x10001)
        Attributes:
            a0:00
    Signature Algorithm: sha256WithRSAEncryption
         74:18:26:72:33:be:ef:ae:1d:1e:ff:15:e5:28:db:c1:e0:80:
         42:2c:82:5a:34:aa:1a:70:df:fa:4f:19:e2:5a:0e:33:38:af:
         21:aa:14:b4:85:35:9c:dd:73:98:1c:b7:ce:f3:ff:43:aa:11:
         ....
         3c:b2:62:94:ad:94:11:55:c2:43:e0:5f:3b:39:d3:a6:4b:47:
         09:6b:9d:6b:9b:95:15:10:25:be:8b:5c:cc:f1:ff:7b:26:6b:
         fa:81:df:e4:92:e5:3c:e5:7f:0e:d8:d9:6f:c5:a6:67:fb:2b:
         0b:53:e5:22
```

The following command creates a certificate\. Because no template is specified, a base end\-entity certificate is issued by default\.

```
$ aws acm-pca issue-certificate \
      --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
      --csr fileb://csr.pem \
      --signing-algorithm "SHA256WITHRSA" \
      --validity Value=365,Type="DAYS"
```

The ARN of the issued certificate is returned:

```
{
   "CertificateArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID"
}
```

**Note**  
AWS Private CA immediately returns an ARN with a serial number when it receives the issue\-certificate command\. However, certificate processing happens asynchronously and can still fail\. If this happens, a get\-certificate command using the new ARN will also fail\.

## Issue a certificate with a custom subject name using an APIPassthrough template<a name="custom-subject-1"></a>

In this example, a certificate is issued containing customized subject name elements\. In addition to supplying a CSR like the one in [Issuing a standard certificate \(AWS CLI\)](#IssueCertCli), you pass two additional arguments to the issue\-certificate command: the ARN of an APIPassthrough template, and a JSON configuration file that specifies the custom attributes and their object identifiers \(OIDs\)\. You cannot use `StandardAttributes` in conjunction with `CustomAttributes`\. however, you can pass standard OIDs as part of `CustomAttributes`\. The default subject name OIDs are listed in the following table:


| Subject name | Object ID | 
| --- | --- | 
|  Country  | 2\.5\.4\.6 | 
|  CommonName  | 2\.5\.4\.3 | 
|  DistinguishedNameQualifier  | 2\.5\.4\.46 | 
|  GenerationQualifier  | 2\.5\.4\.44 | 
|  GivenName  | 2\.5\.4\.42 | 
|  Initials  | 2\.5\.4\.43 | 
|  Locality  | 2\.5\.4\.7 | 
|  Organization  | 2\.5\.4\.10 | 
|  OrganizationalUnit  | 2\.5\.4\.11 | 
|  Pseudonym  | 2\.5\.4\.65 | 
|  SerialNumber  | 2\.5\.4\.5 | 
|  State  | 2\.5\.4\.8 | 
|  Surname  | 2\.5\.4\.4 | 
|  Title  | 2\.5\.4\.12 | 

The sample configuration file `api_passthrough_config.txt` contains the following code:

```
{
  "Subject": {
    "CustomAttributes": [
      {
        "ObjectIdentifier": "2.5.4.6",
        "Value": "US"
      },
      {
        "ObjectIdentifier": "1.3.6.1.4.1.37244.1.1",
        "Value": "BCDABCDA12341234"
      },
      {
        "ObjectIdentifier": "1.3.6.1.4.1.37244.1.5",
        "Value": "CDABCDAB12341234"
      }
    ]
  }
}
```

Use the following command to issue the certificate:

```
$ aws acm-pca issue-certificate \
      --validity Type=DAYS,Value=10 
      --signing-algorithm "SHA256WITHRSA" \
      --csr fileb://csr.pem \
      --api-passthrough file://api_passthrough_config.txt \
      --template-arn arn:aws:acm-pca:::template/BlankEndEntityCertificate_APIPassthrough/V1 \
      --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID
```

The ARN of the issued certificate is returned:

```
{
   "CertificateArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID"
}
```

Retrieve the certificate locally as follows:

```
$ aws acm-pca get-certificate \
      --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
      --certificate-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID | \
      jq -r .'Certificate' > cert.pem
```

You can inspect the certificate's contents using OpenSSL:

```
$ openssl x509 -in cert.pem -text -noout
```

**Note**  
It is also possible to create a private CA that passes custom attributes to each certificate it issues\.

## Issue a certificate with custom extensions using an APIPassthrough template<a name="custom-subject-2"></a>

In this example, a certificate is issued that contains customized extensions\. For this you need to pass three arguments to the issue\-certificate command: the ARN of an APIPassthrough template, and a JSON configuration file that specifies the custom extensions, and a CSR like the one shown in [Issuing a standard certificate \(AWS CLI\)](#IssueCertCli)\. 

The sample configuration file `api_passthrough_config.txt` contains the following code:

```
{
  "Extensions": {
    "CustomExtensions": [
      {
        "ObjectIdentifier": "2.5.29.30",
        "Value": "MBWgEzARgg8ucGVybWl0dGVkLnRlc3Q=",
        "Critical": true
      }
    ]
  }
}
```

The customized certificate is issued as follows:

```
$ aws acm-pca issue-certificate \
      --validity Type=DAYS,Value=10 
      --signing-algorithm "SHA256WITHRSA" \
      --csr fileb://csr.pem \
      --api-passthrough file://api_passthrough_config.txt \
      --template-arn arn:aws:acm-pca:::template/EndEntityCertificate_APIPassthrough/V1 \
      --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID
```

The ARN of the issued certificate is returned:

```
{
   "CertificateArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID"
}
```

Retrieve the certificate locally as follows:

```
$ aws acm-pca get-certificate \
      --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
      --certificate-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID | \
      jq -r .'Certificate' > cert.pem
```

You can inspect the certificate's contents using OpenSSL:

```
$ openssl x509 -in cert.pem -text -noout
```