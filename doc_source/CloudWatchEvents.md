# Using CloudWatch Events<a name="CloudWatchEvents"></a>

You can use [Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/) to automate your AWS services and respond automatically to system events such as application availability issues or resource changes\. Events from AWS services are delivered to CloudWatch Events in near\-real time\. You can write simple rules to indicate which events are of interest to you and the automated actions to take when an event matches a rule\. CloudWatch Events are published at least once\. For more information, see [Creating a CloudWatch Events Rule That Triggers on an Event](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-Rule.html)\. 

CloudWatch Events are turned into actions using Amazon EventBridge\. With EventBridge, you can use events to trigger targets including AWS Lambda functions, AWS Batch jobs, Amazon SNS topics, and many others\. For more information, see [What Is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/what-is-amazon-eventbridge.html)

## Success or failure when creating a private CA<a name="cwe-issue-CA"></a>

These events are triggered by the [CreateCertificateAuthority](https://docs.aws.amazon.com/privateca/latest/APIReference/API_CreateCertificateAuthority.html) operation\.

**Success**  
On success, the operation returns the ARN of the new CA\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA Creation",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-04T19:14:56Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
   ],
   "detail":{
      "result":"success"
   }
}
```

**Failure**  
On failure, the operation returns an ARN for the CA\. Using the ARN, you can call [DescribeCertificateAuthority](https://docs.aws.amazon.com/privateca/latest/APIReference/API_DescribeCertificateAuthority.html) to determine the status of the CA\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA Creation",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-04T19:14:56Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
   ],
   "detail":{
      "result":"failure"
   }
}
```

## Success or failure when issuing a certificate<a name="cwe-issue-cert"></a>

These events are triggered by the [IssueCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_IssueCertificate.html) operation\.

**Success**  
On success, the operation returns the ARNs of the CA and of the new certificate\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA Certificate Issuance",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-04T19:57:46Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID"
   ],
   "detail":{
      "result":"success"
   }
}
```

**Failure**  
On failure, the operation returns a certificate ARN and the ARN of the CA\. With the certificate ARN, you can call [GetCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_GetCertificate.html) to view the reason for the failure\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA Certificate Issuance",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-04T19:57:46Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID"
   ],
   "detail":{
      "result":"failure"
   }
}
```

## Success when revoking a certificate<a name="cwe-revocation"></a>

This event is triggered by the [RevokeCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_RevokeCertificate.html) operation\.

No event is sent if the revocation fails or if the certificate has already been revoked\.

****Success****  
On success, the operation returns the ARNs of the CA and of the revoked certificate\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA Certificate Revocation",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-05T20:25:19Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/certificate_ID"
   ],
   "detail":{
      "result":"success"
   }
}
```

## Success or failure when generating a CRL<a name="cwe-CRL"></a>

These events are triggered by the [RevokeCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_RevokeCertificate.html) operation, which should result in the creation of a certificate revocation list \(CRL\)\.

**Success**  
On success, the operation returns the ARN of the CA associated with the CRL\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA CRL Generation",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-04T21:07:08Z",
   "region":"region1",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
   ],
   "detail":{
      "result":"success"
   }
}
```

**Failure 1 – CRL could not be saved to Amazon S3 because of a permission error**  
Check your Amazon S3 bucket permissions if this error occurs\. 

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA CRL Generation",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-07T23:01:25Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
   ],
   "detail":{
      "result":"failure",
      "reason":"Failed to write CRL to S3. Check your S3 bucket permissions."
   }
}
```

**Failure 2 – CRL could not be saved to Amazon S3 because of an internal error**  
Retry the operation if this error occurs\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA CRL Generation",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-07T23:01:25Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
   ],
   "detail":{
      "result":"failure",
      "reason":"Failed to write CRL to S3. Internal failure."
   }
}
```

**Failure 3 – AWS Private CA failed to create a CRL**  
To troubleshoot this error, check your [CloudWatch metrics](https://docs.aws.amazon.com/privateca/latest/APIReference/PcaCloudWatch.html)\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA CRL Generation",
   "source":"aws.acm-pca",
   "account":"1account",
   "time":"2019-11-07T23:01:25Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
   ],
   "detail":{
      "result":"failure",
      "reason":"Failed to generate CRL. Internal failure."
   }
}
```

## Success or failure when creating a CA audit report<a name="cwe-audit"></a>

These events are triggered by the [CreateCertificateAuthorityAuditReport](https://docs.aws.amazon.com/privateca/latest/APIReference/API_CreateCertificateAuthorityAuditReport.html) operation\.

**Success**  
On success, the operation returns the ARN of the CA and the ID of the audit report\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA Audit Report Generation",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-04T21:54:20Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
      "audit_report_ID"
   ],
   "detail":{
      "result":"success"
   }
}
```

**Failure**  
An audit report can fail when AWS Private CA lacks `PUT` permissions on your Amazon S3 bucket, when encryption is enabled on the bucket, or for other reasons\.

```
{
   "version":"0",
   "id":"event_ID",
   "detail-type":"ACM Private CA Audit Report Generation",
   "source":"aws.acm-pca",
   "account":"account",
   "time":"2019-11-04T21:54:20Z",
   "region":"region",
   "resources":[
      "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
      "audit_report_ID"
   ],
   "detail":{
      "result":"failure"
   }
}
```