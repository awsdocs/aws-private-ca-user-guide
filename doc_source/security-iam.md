# Identity and Access Management \(IAM\) for AWS Private Certificate Authority<a name="security-iam"></a>

 Access to AWS Private CA requires credentials that AWS can use to authenticate your requests\. The following topics provide details on how you can use [AWS Identity and Access Management \(IAM\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) to help secure your private certificate authorities \(CAs\) by controlling who can access them\. 

In AWS Private CA, the primary resource that you work with is a *certificate authority \(CA\)*\. Every private CA that you own or control is identified by an Amazon Resource Name \(ARN\), which has the following form\. 

```
arn:aws:acm-pca:region:account:certificate-authority/CA_ID
```

A *resource owner* is the *principal entity* of the AWS account in which an AWS resource is created\. The following examples illustrate how this works\. 
+ If you use the credentials of your AWS account root user to create a private CA, your AWS account owns the CA\. 
+ If you create an IAM user in your AWS account, you can grant that user permission to create a private CA\. However, the account to which that user belongs owns the CA\. 
+ If you create an IAM role in your AWS account and grant it permission to create a private CA, anyone who can assume the role can create the CA\. However, the account to which the role belongs will own the private CA\. 

A *permissions policy* describes who has access to what\. The following discussion explains the available options for creating permissions policies\. 

**Note**  
This documentation discusses using IAM in the context of AWS Private CA\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)\. For information about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html)\. 