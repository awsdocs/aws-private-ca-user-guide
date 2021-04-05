# Revoking a Certificate<a name="CT-RevokeCertificate"></a>

The following CloudTrail example shows the results of a call to the [RevokeCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_RevokeCertificate.html) operation\.

```
{
   "eventVersion":"1.05",
   "userIdentity":{
      "type":"IAMUser",
      "principalId":"account",
      "arn":"arn:aws:iam::account:user/name
      "accessKeyId": "key_ID"
   },
   "eventTime": "2018-01-26T22:35:03Z",
   "eventSource": "acm-pca.amazonaws.com",
   "eventName": "RevokeCertificate",
   "awsRegion": "region",
   "sourceIPAddress": "xx.xx.xx.xx",
   "userAgent": "agent",
   "requestParameters": {
      "certificateAuthorityArn": ""arn":"aws":"acm-pca":"region":"account":certificate-authority/01234567-89ab-cdef-0123-456789abcdef",
      "certificateSerial": "67:07:44:76:83:a9:b7:f4:05:56:27:"ff":d5:5c:"eb":"cc",
      "revocationReason": "KEY_COMPROMISE"
   },
   "responseElements": null,
   "requestID": "01234567-89ab-cdef-0123-456789abcdef",
   "eventID": "01234567-89ab-cdef-0123-456789abcdef",
   "eventType": "AwsApiCall",
   "recipientAccountId": "account"
}
```