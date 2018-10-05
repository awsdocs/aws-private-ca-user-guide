# Supported CloudWatch Metrics<a name="PcaCloudWatch"></a>

Amazon CloudWatch is a monitoring service for AWS resources\. You can use CloudWatch to collect and track metrics, set alarms, and automatically react to changes in your AWS resources\. ACM PCA supports the following CloudWatch metrics\. 


****  

| Metric | Description | 
| --- | --- | 
| CRLGenerated | A certificate revocation list \(CRL\) was generated\. This metric applies only to a private CA\. | 
| MisconfiguredCRLBucket | The S3 bucket specified for the CRL is not correctly configured\. Check the bucket policy\. This metric applies only to a private CA\. | 
| Time | The time at which the certificate was issued\. This metric applies only to the [****IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html) operation\.  | 
| Success | Specifies whether a certificate was successfully issued\. This metric applies only to the IssueCertificate operation\. | 
| Failure | Indicates that an operation failed\. This metric applies only to the IssueCertificate operation\. | 

For more information about CloudWatch metrics, see the following topics:
+ [Using Amazon CloudWatch Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)
+ [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)