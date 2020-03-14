# Issuing a Private End\-Entity Certificate<a name="PcaIssueCert"></a>

You can create private end\-entity certificates from either AWS Certificate Manager \(ACM\) or ACM Private CA\. The capabilities of both are compared in the following table\.


****  

| Capability | ACM | ACM Private CA | 
| --- | --- | --- | 
| Create private CA certificates | Not supported | ✓ \(using [IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html)\) | 
| Import private CA certificates | ✓ | ✓ | 
| Create end\-entity certificates | ✓ \(using [RequestCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_RequestCertificate.html) \+ [GetCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_GetCertificate.html)\) | ✓ \(using [IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html)\) | 
| Console support | ✓ | ✓ \(CA certificates only\) | 
| API support | ✓ | ✓ | 
| CLI support | ✓ | ✓ | 
| User\-configurable templates | Not supported | ✓ | 

Thus the ACM Private CA API and the AWS CLI can issue any certificate type\. The ACM Private CA console can issue CA certificates\. And the ACM service can issue end\-entity certificates using its console, API, or the AWS CLI\. For more information about using ACM, see [Request a Private Certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-private.html)\.

When ACM Private CA creates a certificate, it applies a user\-definable template that specifies the certificate type and path length\. If no template ARN is supplied to the API or CLI statement creating the certificate, the end\-entity template is applied by default\. For more information about available certificate templates, see [Using Templates](UsingTemplates.md)\.

Private certificates that you issue from the ACM Private CA API or AWS CLI are not subject to the same restrictions as private certificates that ACM issues\. You can use ACM Private CA to do the following:
+ Create a certificate with any subject name\.
+ Use any of the supported private key algorithms and key lengths\.
+ Use any of the signing algorithms that are currently supported\.
+ Specify any validity period for your private CA and private certificates\.
+ Import your private certificates into ACM and IAM\.

## Issuing a Certificate \(AWS CLI\)<a name="IssueCertCli"></a>

You can use the [issue\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/issue-certificate.html) command to request a private certificate\. This command requires the Amazon Resource Name \(ARN\) of the private CA that you want to use to issue the certificate\. It also requires the certificate signing request \(CSR\) for the certificate that you want to issue\. You can also use the [IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html) operation\. 

**Note**  
You use the ACM Private CA API or AWS CLI to issue a private certificate\. But if you do, you cannot use the ACM console, CLI, or API to view or export it\. You already have the private key because you needed one to create the CSR \(see the previous discussion\)\. You can use the [get\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate.html) command to retrieve the certificate details\. You can also create an [audit report](PcaAuditReport.md) to make sure that your certificate was issued\. 

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