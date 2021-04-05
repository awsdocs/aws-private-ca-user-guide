# Deleting a Certificate Authority<a name="CT-DeleteCA"></a>

The following CloudTrail example shows the results of a call to the [DeleteCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DeleteCertificateAuthority.html) operation\. In this example, the certificate authority cannot be deleted because it is in the `ACTIVE` state\. 

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
   "eventTime":"2018-01-26T22:01:11Z",
   "eventSource":"acm-pca.amazonaws.com",
   "eventName":"DeleteCertificateAuthority",
   "awsRegion":"region",
   "sourceIPAddress":"xx.xx.xx.xx",
   "userAgent":"agent",
   "errorCode":"InvalidStateException",
   "errorMessage":"The certificate authority is not in a valid state for deletion.",
   "requestParameters":{
      "certificateAuthorityArn":"arn:aws:acm-pca:rregion:account:certificate-authority/01234567-89ab-cdef-0123-456789abcdef"
   },
   "responseElements":null,
   "requestID":"01234567-89ab-cdef-0123-456789abcdef",
   "eventID":"01234567-89ab-cdef-0123-456789abcdef",
   "eventType":"AwsApiCall",
   "recipientAccountId":"account"
}
```