# Create an Audit Report for Your Private CA<a name="PcaAuditReport"></a>

You can create an audit report to list all of the certificates that your private CA has issued or revoked\. The report is saved in a new or existing S3 bucket that you specify on input\. The file is named in the following manner\. 

```
bucket-name/audit-report/CA-ARN/file-ARN.[json|csv]
```

You can generate a new report every 30 minutes and download it from your bucket\. The following example shows a JSON\-formatted report\. 

```
[{
  "awsAccountId": "123456789012",
  "certificateArn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/e8cbd2bedb122329f97706bcfec990f8",
  "serial": "e8:cb:d2:be:db:12:23:29:f9:77:06:bc:fe:c9:90:f8",
  "subject": "1.2.840.113549.1.9.1=#161173616c6573406578616d706c652e636f6d,CN=www.example1.com,OU=Sales,O=Example Company,L=Seattle,ST=Washington,C=US",
  "notBefore": "2018-02-26T18:39:57+0000",
  "notAfter": "2019-02-26T19:39:57+0000",
  "issuedAt": "2018-02-26T19:39:58+0000",
  "revokedAt": "2018-02-26T20:00:36+0000",
  "revocationReason": "KEY_COMPROMISE"
},
{
  "awsAccountId": "123456789012",
  "certificateArn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID/certificate/2bae9a75d71b42b4e41e36f8b4b488fc",
  "serial": "2b:ae:9a:75:d7:1b:42:b4:e4:1e:36:f8:b4:b4:88:fc",
  "subject": "1.2.840.113549.1.9.1=#161970726f64407777772e70616c6f75736573616c65732e636f6d,CN=www.example3.com.com,OU=Sales,O=Example Company,L=Seattle,ST=Washington,C=US",
  "notBefore": "2018-01-22T20:10:49+0000",
  "notAfter": "2019-01-17T21:10:49+0000",
  "issuedAt": "2018-01-22T21:10:49+0000"
}]
```

You can create a report from the console or the AWS CLI\.
+ [Creating a report \(console\)](#AuditReportConsole)
+ [Creating a report \(AWS CLI\)](#AuditReportCLI)

**To create an audit report \(console\)**

1. Sign in to your AWS account and open the ACM PCA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Choose **Private CAs**\.

1. Choose your private CA from the list\.

1. On the **Actions** menu, choose **Generate audit report**\.

1. For **Create a new S3 bucket**, choose **Yes** and type a unique bucket name or choose **No** and choose an existing bucket from the list\. 

   If you choose **Yes**, ACM PCA creates and attaches the necessary policy to your bucket\. If you choose **No**, you must attach the following policy to your bucket before you can generate an audit report\. For instructions, see [How Do I Add an S3 Bucket Policy?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)\. 

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
Use the [create\-certificate\-authority\-audit\-report](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/create-certificate-authority-audit-report.html) command to create the audit report\. You must attach the following policy to the bucket\. 

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