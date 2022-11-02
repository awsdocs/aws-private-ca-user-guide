# Services integrated with AWS Private Certificate Authority<a name="PcaIntegratedServices"></a>

If you use AWS Certificate Manager to request a private certificate, you can associate that certificate with any service that is integrated with ACM\. This applies both to certificates chained to a AWS Private CA root and to certificates chained to an external root\. For more information, see [Integrated Services](https://docs.aws.amazon.com/acm/latest/userguide/acm-services.html) in the AWS Certificate Manager User Guide\. 

You can also integrate private CAs into Amazon Elastic Kubernetes Service to provide certificate issuance inside a Kubernetes cluster\. For more information, see [Securing Kubernetes with AWS Private CA](PcaKubernetes.md)\.

**Note**  
Amazon Elastic Kubernetes Service is not an ACM integrated service\.

If you use the AWS Private CA API or AWS CLI to issue a certificate or to export a private certificate from ACM, you can install the certificate anywhere you want\. 