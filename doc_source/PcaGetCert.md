# Retrieving a private certificate<a name="PcaGetCert"></a>

You can use the ACM Private CA API and AWS CLI to issue a private certificate\. If you do, you can use the AWS CLI or ACM Private CA API to retrieve that certificate\. If you used ACM to create your private CA and to request certificates, you must use ACM to export the certificate and the encrypted private key\. For more information, see [Exporting a Private Certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-export-private.html)\. 

**To retrieve an end\-entity certificate**  
Use the [get\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate.html) AWS CLI command to retrieve a private end\-entity certificate\. You can also use the [GetCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificate.html) API operation\. We recommend formatting the output with [jq](https://stedolan.github.io/jq/), a sed\-like parser\.

**Note**  
If you want to revoke a certificate, you can use the get\-certificate command to retrieve the serial number in hexadecimal format\. You can also create an audit report to retrieve the hex serial number\. For more information, see [Using audit reports with your private CA](PcaAuditReport.md)\. 

```
$ aws acm-pca get-certificate \
      --certificate-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID \
      --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
      --region us-west-2 \
      | jq -r '.Certificate, .CertificateChain'
```

This command outputs the certificate and certificate chain in the following standard format\.

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

**To retrieve a CA certificate**  
You can use the ACM Private CA API and AWS CLI to retrieve the certificate authority \(CA\) certificate for your private CA\. Run the `[get\-certificate\-authority\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-certificate-authority-certificate.html)` command\. You can also call the `[GetCertificateAuthorityCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificateAuthorityCertificate.html)` operation\. We recommend formatting the output with [jq](https://stedolan.github.io/jq/), a sed\-like parser\. 

```
$ aws acm-pca get-certificate-authority-certificate \
     --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
     | jq -r '.Certificate'
```

This command outputs the CA certificate in the following standard format\.

```
-----BEGIN CERTIFICATE-----
...Base64-encoded certificate...
-----END CERTIFICATE----
```