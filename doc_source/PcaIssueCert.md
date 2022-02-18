# Issuing a private end\-entity certificate<a name="PcaIssueCert"></a>

With a private CA in place, you can request private end\-entity certificates from either AWS Certificate Manager \(ACM\) or ACM Private CA\. The capabilities of both services are compared in the following table\.


****  

|  Capability  |  ACM  |  ACM Private CA  | 
| --- | --- | --- | 
|  Issue end\-entity certificates  |  ✓ \(using `[RequestCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_RequestCertificate.html)` \+ `[GetCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_GetCertificate.html)` or the console\)  |  ✓ \(using `[IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html)`\)  | 
|  Association with internet\-facing AWS services  |  ✓  |  Not supported  | 
| Managed certificate renewal | ✓ | Indirectly [supported](https://docs.aws.amazon.com/acm/latest/userguide/managed-renewal.html) though ACM | 
|  Console support  |  ✓  |  Not supported  | 
|  API support  |  ✓  |  ✓  | 
|  CLI support  |  ✓  |  ✓  | 

When ACM Private CA creates a certificate, it follows a template that specifies the certificate type and path length\. If no template ARN is supplied to the API or CLI statement creating the certificate, the [EndEntityCertificate/V1](UsingTemplates.md#EndEntityCertificate-V1) template is applied by default\. For more information about available certificate templates, see [Understanding certificate templates](UsingTemplates.md)\.

While ACM certificates are designed around public trust, ACM Private CA serves the needs of your private PKI\. Consequently, you can configure certificates using the ACM Private CA API and CLI in ways not permitted by ACM\. These include the following:
+ Creating a certificate with any Subject name\.
+ Using any of the [supported private key algorithms and key lengths](https://docs.aws.amazon.com/acm-pca/latest/userguide/supported-algorithms.html)\.
+ Using any of the [supported signing algorithms](https://docs.aws.amazon.com/acm-pca/latest/userguide/supported-algorithms.html)\.
+ Specifying any validity period for your private [CA](PcaCreateCa.html) and private [certificates](PcaIssueCert.html)\.

After creating a private certificate using ACM Private CA, you can [import](https://docs.aws.amazon.com/acm/latest/userguide/import-certificate-api-cli.html) it into ACM and use it with a supported AWS service\.

**Note**  
Certificates created with the procedure below, using the issue\-certificate command, or with the [IssueCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_RequestCertificate.html) API action, cannot be exported for use outside AWS\. However, you can use your private CA to sign certificates issued through ACM, and those certificates can be exported along with their secret keys\. For more information, see [Requesting a private certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-private.html) and [Exporting a private certificate](https://docs.aws.amazon.com/acm/latest/userguide/export-private.html) in the *ACM User Guide*\.

## Issuing a certificate \(AWS CLI\)<a name="IssueCertCli"></a>

You can use the ACM Private CA CLI command [issue\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/issue-certificate.html) or the API action [IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html) to request an end\-entity certificate\. This command requires the Amazon Resource Name \(ARN\) of the private CA that you want to use to issue the certificate\. You must also generate a certificate signing request \(CSR\) using a program such as [OpenSSL](https://www.openssl.org/)\.

If you use the ACM Private CA API or AWS CLI to issue a private certificate, the certificate is unmanaged, meaning that you cannot use the ACM console, ACM CLI, or ACM API to view or export it, and the certificate is not automatically renewed\. However, you can use the PCA [get\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate.html) command to retrieve the certificate details, and if you own the CA, you can create an [audit report](PcaAuditReport.md)\.

**Considerations when creating certificates**
+ In compliance with [RFC 5280](https://datatracker.ietf.org/doc/html/rfc5280), the length of the domain name \(technically, the Common Name\) that you provide cannot exceed 64 octets \(characters\), including periods\. To add a longer domain name, specify it in the Subject Alternative Name field, which supports names up to 253 octets in length\. 
+ If you are using AWS CLI version 1\.6\.3 or later, use the prefix `fileb://` when specifying the required input file\. This ensures that ACM Private CA parses the Base64\-encoded data correctly\.

The following command specifies no template, so an end\-entity certificate is issued by default\.

```
$ aws acm-pca issue-certificate \
--certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
--csr fileb://cert_1.csr \
--signing-algorithm "SHA256WITHRSA" \
--validity Value=365,Type="DAYS" \
--idempotency-token 1234
```

The ARN of the issued certificate is returned:

```
{
   "CertificateArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID"
}
```

**Note**  
ACM Private CA immediately returns an ARN with a serial number when it receives the issue\-certificate command\. However, certificate processing happens asynchronously and can still fail\. If this happens, a get\-certificate command using the new ARN will also fail\.