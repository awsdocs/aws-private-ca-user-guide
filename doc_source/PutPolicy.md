# Creating a policy<a name="PutPolicy"></a>

The following CloudTrail example shows the results of a call to the [PutPolicy](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_PutPolicy.html) operation\.

```
{
   "eventVersion":"1.08",
   "userIdentity":{
      "type":"AssumedRole",
      "principalId":"account",
      "arn":"arn:aws:sts::account:assumed-role/role",
      "accountId":"account",
      "accessKeyId":"key_ID",
      "sessionContext":{
         "sessionIssuer":{
            "type":"Role",
            "principalId":"account",
            "arn":"arn:aws:iam::account:role/role",
            "accountId":"account",
            "userName":"name"
         },
         "webIdFederationData":{
            
         },
         "attributes":{
            "mfaAuthenticated":"false",
            "creationDate":"2021-02-26T21:01:38Z"
         }
      },
      "invokedBy":"agent"
   },
   "eventTime":"2021-02-26T21:25:36Z",
   "eventSource":"acm-pca.amazonaws.com",
   "eventName":"PutPolicy",
   "awsRegion":"region",
   "sourceIPAddress":"xx.xx.xx.xx",
   "userAgent":"agent",
   "requestParameters":{
      "resourceArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
      "policy":"{\"Version\":\"2012-10-17\",\"Statement\":[{\"Sid\":\"01234567-89ab-cdef-0123-456789abcdef4-external-principals\",\"Effect\":\"Allow\",\"Principal\":{\"AWS\":\"account\"},\"Action\":\"acm-pca:IssueCertificate\",\"Resource\":\"arn:aws:acm-pca:region:account:certificate-authority/CA_ID\",\"Condition\":{\"StringEquals\":{\"acm-pca:TemplateArn\":\"arn:aws:acm-pca:::template/EndEntityCertificate/V1\"}}},{\"Sid\":\"01234567-89ab-cdef-0123-456789abcdef-external-principals\",\"Effect\":\"Allow\",\"Principal\":{\"AWS\":\"account\"},\"Action\":[\"acm-pca:DescribeCertificateAuthority\",\"acm-pca:GetCertificate\",\"acm-pca:GetCertificateAuthorityCertificate\",\"acm-pca:ListPermissions\",\"acm-pca:ListTags\"],\"Resource\":\"arn:aws:acm-pca:region:account:certificate-authority/CA_ID\"}]}"
   },
   "responseElements":null,
   "requestID":"01234567-89ab-cdef-0123-456789abcdef",
   "eventID":"01234567-89ab-cdef-0123-456789abcdef",
   "readOnly":false,
   "eventType":"AwsApiCall",
   "managementEvent":true,
   "eventCategory":"Management",
   "recipientAccountId":"account"
}
```