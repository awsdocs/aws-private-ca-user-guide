# Creating an Audit Report for Your Private CA<a name="PcaAuditReport"></a>

You can create an audit report to list all of the certificates that your private CA has issued or revoked\. The report is saved in a new or existing S3 bucket that you specify on input\. 

The audit report file has the following path and filename\. \(`CA_ID` is the unique identifier of an issuing CA and `UUID` is the unique identifier of an audit report\.\)

```
bucket-name/audit-report/CA-ID/UUID.[json|csv]
```

You can generate a new report every 30 minutes and download it from your bucket\. The following example shows a CSV\-separated report\.

```
awsAccountId,requestedByServicePrincipal,certificateArn,serial,subject,notBefore,notAfter,issuedAt,revokedAt,revocationReason,templateArn
123456789012,,arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/fedcba9876543210fedcba9876543210,00:11:22:33:44:55:66:77:88:99:aa:bb:cc:dd:ee:ff,"2.5.4.5=#012345678901,2.5.4.44=#0a1b3c4d,2.5.4.65=#0a1b3c4e5f6a,2.5.4.43=#0a1b3c4d5e,2.5.4.42=#0123456789abcdef0123456789abcdef0123,2.5.4.4=#0123456789abcdef01234567,2.5.4.12=#0a1b3c4d5e,2.5.4.46=#0123456789ab,CN=www.example1.com,OU=Sales,O=Example Company,L=Seattle,ST=Washington,C=US",2020-03-02T21:43:57+0000,2020-04-07T22:43:57+0000,2020-03-02T22:43:58+0000,,UNSPECIFIED,arn:aws:acm-pca:::template/EndEntityCertificate/V1
123456789012,acm.amazonaws.com,arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/0123456789abcdef0123456789abcdef,ff:ee:dd:cc:bb:aa:99:88:77:66:55:44:33:22:11:00,"2.5.4.5=#012345678901,2.5.4.44=#0a1b3c4d,2.5.4.65=#0a1b3c4d5e6f,2.5.4.43=#0a1b3c4d5e,2.5.4.42=#0123456789abcdef0123456789abcdef0123,2.5.4.4=#0123456789abcdef01234567,2.5.4.12=#0a1b3c4d5e,2.5.4.46=#0123456789ab,CN=www.example1.com,OU=Sales,O=Example Company,L=Seattle,ST=Washington,C=US",2020-03-02T20:53:39+0000,2020-04-07T21:53:39+0000,2020-03-02T21:53:40+0000,,,arn:aws:acm-pca:::template/EndEntityCertificate/V1
```

The following example shows a JSON\-formatted report\. 

```
[{
  "awsAccountId": "123456789012",
  "certificateArn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/fedcba9876543210fedcba9876543210",
  "serial": "00:11:22:33:44:55:66:77:88:99:aa:bb:cc:dd:ee:ff",
  "subject": "2.5.4.5=#012345678901,2.5.4.44=#0a1b3c4d,2.5.4.65=#0a1b3c4d5e6f,2.5.4.43=#0a1b3c4d5e,2.5.4.42=#0123456789abcdef0123456789abcdef0123,2.5.4.4=#0123456789abcdef01234567,2.5.4.12=#0a1b3c4d5e,2.5.4.46=#0123456789ab,CN=www.example1.com,OU=Sales,O=Example Company,L=Seattle,ST=Washington,C=US",
  "notBefore": "2020-02-26T18:39:57+0000",
  "notAfter": "2021-02-26T19:39:57+0000",
  "issuedAt": "2020-02-26T19:39:58+0000",
  "revokedAt": "2020-02-26T20:00:36+0000",
  "revocationReason": "UNSPECIFIED",
  "templateArn":"arn:aws:acm-pca:::template/EndEntityCertificate/V1"
},
{
  "awsAccountId": "123456789012",
  "requestedByServicePrincipal": "acm.amazonaws.com",
  "certificateArn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/0123456789abcdef0123456789abcdef",
  "serial": "ff:ee:dd:cc:bb:aa:99:88:77:66:55:44:33:22:11:00",
  "subject": "2.5.4.5=#012345678901,2.5.4.44=#0a1b3c4d,2.5.4.65=#0a1b3c4d5e6f,2.5.4.43=#0a1b3c4d5e,2.5.4.42=#0123456789abcdef0123456789abcdef0123,2.5.4.4=#0123456789abcdef01234567,2.5.4.12=#0a1b3c4d5e,2.5.4.46=#0123456789ab,CN=www.example1.com,OU=Sales,O=Example Company,L=Seattle,ST=Washington,C=US",
  "notBefore": "2020-01-22T20:10:49+0000",
  "notAfter": "2021-01-17T21:10:49+0000",
  "issuedAt": "2020-01-22T21:10:49+0000",
  "templateArn":"arn:aws:acm-pca:::template/EndEntityCertificate/V1"
}]
```

You can create a report from the console or the AWS CLI\.

**To create an audit report \(console\)**

1. Sign in to your AWS account and open the ACM Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Choose **Private CAs**\.

1. Choose your private CA from the list\.

1. On the **Actions** menu, choose **Generate audit report**\.

1. For **Create a new S3 bucket**, choose **Yes** and type a unique bucket name or choose **No** and choose an existing bucket from the list\. 

   If you choose **Yes**, ACM Private CA creates and attaches the necessary policy to your bucket\. If you choose **No**, you must attach the following policy to your bucket before you can generate an audit report\. For instructions, see [How Do I Add an S3 Bucket Policy?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)\. 

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "Service": "acm-pca.amazonaws.com"
               },
               "Action": [
                   "s3:PutObject",
                   "s3:PutObjectAcl",
                   "s3:GetBucketAcl",
                   "s3:GetBucketLocation"
               ],
               "Resource": [
                   "arn:aws:s3:::bucket-name/*",
                   "arn:aws:s3:::bucket-name"
               ]
           }
       ]
   }
   ```

1. For **Output format**, choose **JSON** for JavaScript Object Notation or **CSV** for comma\-separated values\. 

1. Choose **Generate audit report**\.

**To create an audit report \(AWS CLI\)**  
Use the `[create\-certificate\-authority\-audit\-report](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/create-certificate-authority-audit-report.html)` command to create the audit report\. You must attach the following policy to the bucket\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "acm-pca.amazonaws.com"
            },
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:GetBucketAcl",
                "s3:GetBucketLocation"
            ],
            "Resource": [
                "arn:aws:s3:::bucket-name/*",
                "arn:aws:s3:::bucket-name"
            ]
        }
    ]
}
```

```
aws acm-pca create-certificate-authority-audit-report \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--s3-bucket-name >your-bucket-name \
--audit-report-response-format JSON
```