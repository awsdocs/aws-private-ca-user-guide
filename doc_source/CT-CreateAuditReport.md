# Creating an Audit Report<a name="CT-CreateAuditReport"></a>

The following CloudTrail example shows the results of a call to the [CreateCertificateAuthorityAuditReport](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_CreateCertificateAuthorityAuditReport.html) operation\.

```
{
   "eventVersion":"1.05",
   "userIdentity":{
      "type":"IAMUser",
      "principalId":"account",
      "arn":"arn:aws:iam::account:user/name",
      "accountId":"account",
      "accessKeyId":"key_ID"
   },
   "eventTime":"2018-01-26T21:56:00Z",
   "eventSource":"acm-pca.amazonaws.com",
   "eventName":"CreateCertificateAuthorityAuditReport",
   "awsRegion":"region",
   "sourceIPAddress":"xx.xx.xx.xx",
   "userAgent":"agent",
   "requestParameters":{
      "certificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123-456789abcdef",
      s3BucketName:"your-bucket-name",
      "auditReportResponseFormat":"JSON"
   },
   "responseElements":{
      "auditReportId":"01234567-89ab-cdef-0123-456789abcdef",
      s3Key:"audit-report/01234567-89ab-cdef-0123-456789abcdef/01234567-89ab-cdef-0123-456789abcdef.json"
   },
   "requestID":"01234567-89ab-cdef-0123-456789abcdef",
   "eventID":"01234567-89ab-cdef-0123-456789abcdef",
   "eventType":"AwsApiCall",
   "recipientAccountId":"account"
}
```