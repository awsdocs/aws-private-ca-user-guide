# Best Practices<a name="PcaBestPractices"></a>

Best practices are recommendations that can help you use AWS Certificate Manager Private Certificate Authority \(ACM PCA\) more effectively\. The following best practices are based on real\-world experience from current ACM customers\. 

**Topics**
+ [Turn on AWS CloudTrail](#PcaBestPractices-CT)
+ [Update the CA Private Key](#PcaBestPractices-NewKey)
+ [Delete a CA](#PcaBestPractices-Deletion)

## Turn on AWS CloudTrail<a name="PcaBestPractices-CT"></a>

Turn on CloudTrail logging before you create and start operating a private CA\. With CloudTrail you can retrieve a history of AWS API calls for your account to monitor your AWS deployments\. This history includes API calls made from the AWS Management Console, the AWS SDKs, the AWS Command Line Interface, and higher\-level AWS services\. You can also identify which users and accounts called the PCA API operations, the source IP address the calls were made from, and when the calls occurred\. You can integrate CloudTrail into applications using the API, automate trail creation for your organization, check the status of your trails, and control how administrators turn CloudTrail logging on and off\. For more information, see [Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)\. Go to [Using CloudTrail](PcaCtIntro.md) to see example trails for ACM PCA operations\. 

## Update the CA Private Key<a name="PcaBestPractices-NewKey"></a>

It is a best practice to periodically update the private key for your private CA\. Doing so gives an attacker less time to attempt to crack you key and less time to use the key if it has already been compromised\. You cannot update a key by importing a new CA certificate\. You must replace the private CA with a new CA\. We recommend that you follow the process outlined in [Delete Your Private CA](PCADeleteCA.md)\. 

## Delete a CA<a name="PcaBestPractices-Deletion"></a>

You can permanently delete a private CA\. You might want to do so if you no longer need the CA or if you want to replace it with a CA that has a newer private key\. To safely delete a CA, we recommend that you follow the process outlined in [Delete Your Private CA](PCADeleteCA.md)\.