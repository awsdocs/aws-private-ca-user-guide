# DescribeCertificateAuthorityAuditReport<a name="JavaApi-DescribeCertificateAuthorityAuditReport"></a>

The following Java sample shows how to use the [DescribeCertificateAuthorityAuditReport](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DescribeCertificateAuthorityAuditReport.html) function\.

The function lists information about a specific audit report that you created by calling the [CreateCertificateAuthorityAuditReport](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_CreateCertificateAuthorityAuditReport.html) function\. Audit information is created every time the certificate authority \(CA\) private key is used\. The private key is used when you issue a certificate, sign a CRL, or revoke a certificate\. 

```
package com.amazonaws.samples;

import java.util.Date;

import com.amazonaws.auth.AWSCredentials;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.client.builder.AwsClientBuilder;
import com.amazonaws.client.builder.AwsClientBuilder.EndpointConfiguration;
import com.amazonaws.auth.AWSStaticCredentialsProvider;

import com.amazonaws.services.acmpca.AWSACMPCA;
import com.amazonaws.services.acmpca.AWSACMPCAClientBuilder;

import com.amazonaws.services.acmpca.model.DescribeCertificateAuthorityAuditReportRequest;
import com.amazonaws.services.acmpca.model.DescribeCertificateAuthorityAuditReportResult;

import com.amazonaws.AmazonClientException;
import com.amazonaws.services.acmpca.model.InvalidArgsException;
import com.amazonaws.services.acmpca.model.ResourceNotFoundException;

public class DescribeCertificateAuthorityAuditReport {

   public static void main(String[] args) throws Exception {

      // Retrieve your credentials from the C:\Users\name\.aws\credentials file
      // in Windows or the .aws/credentials file in Linux.
      AWSCredentials credentials = null;
      try{
         credentials = new ProfileCredentialsProvider("default").getCredentials();
      }  catch (Exception e) {
         throw new AmazonClientException("Cannot load your credentials from file.", e);
      }

      // Define the endpoint for your sample.
      String endpointProtocol = "https://acm-pca.region.amazonaws.com/";
      String endpointRegion = "region";
      EndpointConfiguration endpoint =
            new AwsClientBuilder.EndpointConfiguration(endpointProtocol, endpointRegion);

      // Create a client that you can use to make requests.
      AWSACMPCA client = AWSACMPCAClientBuilder.standard()
         .withEndpointConfiguration(endpoint)
         .withCredentials(new AWSStaticCredentialsProvider(credentials))
         .build();

      // Create a request object.
      DescribeCertificateAuthorityAuditReportRequest req =
            new DescribeCertificateAuthorityAuditReportRequest();

      // Set the certificate authority ARN.
      req.withCertificateAuthorityArn("arn:aws:acm-pca:region:account:" +
          "certificate-authority/12345678-1234-1234-1234-123456789012");

      // Set the audit report ID.
      req.withAuditReportId("11111111-2222-3333-4444-555555555555");
      
      

      // Create a result object.
      DescribeCertificateAuthorityAuditReportResult result = null;
      try {
         result = client.describeCertificateAuthorityAuditReport(req);
      } catch(ResourceNotFoundException  ex) {
         throw ex;
      } catch(InvalidArgsException ex) {
         throw ex;
      }

      String status = result.getAuditReportStatus();
      String S3Bucket = result.getS3BucketName();
      String S3Key = result.getS3Key();
      Date createdAt = result.getCreatedAt();

      System.out.println(status);
      System.out.println(S3Bucket);
      System.out.println(S3Key);
      System.out.println(createdAt);

   }
}
```

Your output should be similar to the following\.

```
SUCCESS
your-audit-report-bucket-name
audit-report/a4119411-8153-498a-a607-2cb77b858043/25211c3d-f2fe-479f-b437-fe2b3612bc45.json
Tue Jan 16 13:07:58 PST 2018
```