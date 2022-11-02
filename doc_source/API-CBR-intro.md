# Using the AWS Private CA API to implement the Matter standard \(Java examples\)<a name="API-CBR-intro"></a>

You can use the AWS Private Certificate Authority API to create certificates that conform to the [Matter connectivity standard](https://github.com/project-chip/connectedhomeip)\. Matter specifies certificate configurations that improve the security and consistency of internet of things \(IoT\) devices across multiple engineering platforms\. For more information about Matter, see [buildwithmatter\.com](https://buildwithmatter.com)\. 

The Java examples in this section interact with the service by sending HTTP requests\. The service returns HTTP responses\. For more information see [AWS Private Certificate Authority API Reference](https://docs.aws.amazon.com/privateca/latest/APIReference/)\. 

In addition to the HTTP API, you can use the AWS SDKs and command line tools to interact with AWS Private CA\. This is recommended over the HTTP API\. For more information, see [Tools for Amazon Web Services](https://aws.amazon.com/tools/)\. The following topics show you how to use the [AWS SDK for Java](https://aws.amazon.com/sdk-for-java/) to program the AWS Private CA API\. 

The [GetCertificateAuthorityCsr](JavaApi-GetCertificateAuthorityCsr.md), [GetCertificate](JavaApi-GetCertificate.md), and [DescribeCertificateAuthorityAuditReport](JavaApi-DescribeCertificateAuthorityAuditReport.md) operations support waiters\. You can use waiters to control the progression of your code based on the presence or state of certain resources\. For more information, see the following topics, as well as [Waiters in the AWS SDK for Java](https://aws.amazon.com/blogs/developer/waiters-in-the-aws-sdk-for-java/) in the [AWS Developer Blog](https://aws.amazon.com/blogs/developer/)\.

**Topics**
+ [Create a device attestation certificate](JavaApiCBC-DeviceAttestationCertificate.md)
+ [Activate a Matter\-compliant intermediate CA](JavaApiCBC-IntermediateCAActivation.md)
+ [Create a node operating certificate](JavaApiCBC-NodeOperatingCertificate.md)
+ [Activate a product attestation authority](JavaApiCBC-ProductAttestationAuthorityActivation.md)
+ [Activate an intermediate product attestation authority](JavaApiCBC-ProductAttestationIntermediateActivation.md)
+ [Activate a Matter\-compliant root CA](JavaApiCBC-ActivateRootCA.md)