# Managing Access to Your Private Certificate Authority\.<a name="auth-overview"></a>

Every AWS resource belongs to an AWS account, and permissions to create or access the resources are defined in permissions policies in that account\. An account administrator can attach permissions policies to IAM identities \(users, groups, and roles\)\. Some services \(including ACM PCA\) also support attaching permissions policies to resources\. 

**Note**  
An *account administrator* \(or administrator user\) is a user with administrator permissions\. For more information, see [Creating an Admin User and Group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide*\. 

When managing permissions, you decide who gets the permissions, the resources they get permissions for, and the specific actions they are allowed to perform\.

**Topics**
+ [ACM PCA Resources and Operations](#pca-resources-operations)
+ [Authentication](#authentication)
+ [Understanding Resource Ownership](#understand-resource-ownership)
+ [Managing Access to Private CAs](#managing-access)

## ACM PCA Resources and Operations<a name="pca-resources-operations"></a>

In ACM PCA, the primary resource that you work with is a *private certificate authority \(PCA\)*\. Every private CA you own or control is identified by an Amazon Resource Name \(ARN\), which has the following form\. 

```
arn:aws:acm:AWS_Region:AWS_Account:certificate-authority/12345678-1234-1234-1234-123456789012
```

## Authentication<a name="authentication"></a>

You can access AWS as any of the following types of identities:
+ **AWS account root user** – When you first create an AWS account, you begin with a single sign\-in identity that has complete access to all AWS services and resources in the account\. This identity is called the AWS account *root user* and is accessed by signing in with the email address and password that you used to create the account\. We strongly recommend that you do not use the root user for your everyday tasks, even the administrative ones\. Instead, adhere to the [best practice of using the root user only to create your first IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#create-iam-users)\. Then securely lock away the root user credentials and use them to perform only a few account and service management tasks\.
+ **IAM user** – An [IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) is an identity within your AWS account that has specific custom permissions \(for example, permissions to create a directory in ACM PCA\)\. You can use an IAM user name and password to sign in to secure AWS webpages like the [AWS Management Console](https://console.aws.amazon.com/), [AWS Discussion Forums](https://forums.aws.amazon.com/), or the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

   

  In addition to a user name and password, you can also generate [access keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) for each user\. You can use these keys when you access AWS services programmatically, either through [one of the several SDKs](https://aws.amazon.com/tools/) or by using the [AWS Command Line Interface \(CLI\)](https://aws.amazon.com/cli/)\. The SDK and CLI tools use the access keys to cryptographically sign your request\. If you don’t use AWS tools, you must sign the request yourself\. ACM PCA supports *Signature Version 4*, a protocol for authenticating inbound API requests\. For more information about authenticating requests, see [Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\.

   
+ **IAM role** – An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an IAM identity that you can create in your account that has specific permissions\. It is similar to an *IAM user*, but it is not associated with a specific person\. An IAM role enables you to obtain temporary access keys that can be used to access AWS services and resources\. IAM roles with temporary credentials are useful in the following situations:

   
  + **Federated user access** – Instead of creating an IAM user, you can use existing user identities from AWS Directory Service, your enterprise user directory, or a web identity provider\. These are known as *federated users*\. AWS assigns a role to a federated user when access is requested through an [identity provider](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers.html)\. For more information about federated users, see [Federated Users and Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html#intro-access-roles) in the *IAM User Guide*\. 

     
  + **AWS service access** – You can use an IAM role in your account to grant an AWS service permissions to access your account’s resources\. For example, you can create a role that allows Amazon Redshift to access an Amazon S3 bucket on your behalf and then load data from that bucket into an Amazon Redshift cluster\. For more information, see [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\. 

      
  + **Applications running on Amazon EC2** – You can use an IAM role to manage temporary credentials for applications that are running on an EC2 instance and making AWS API requests\. This is preferable to storing access keys within the EC2 instance\. To assign an AWS role to an EC2 instance and make it available to all of its applications, you create an instance profile that is attached to the instance\. An instance profile contains the role and enables programs that are running on the EC2 instance to get temporary credentials\. For more information, see [Using an IAM Role to Grant Permissions to Applications Running on Amazon EC2 Instances](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html) in the *IAM User Guide*\. 

## Understanding Resource Ownership<a name="understand-resource-ownership"></a>

A *resource owner* is the *principal entity* of the AWS account in which an AWS resource is created\. The following examples illustrate how this works\. 
+ If you use the credentials of your AWS account root user to create a private CA, your AWS account owns the CA\. 
+ If you create an IAM user in your AWS account, you can grant that user permission to create a private CA\. However, the account to which that user belongs owns the CA\. 
+ If you create an IAM role in your AWS account and grant it permission to create a private CA, anyone who can assume the role will be able to create the CA\. However, the account to which the role belongs will own the private CA\. 

## Managing Access to Private CAs<a name="managing-access"></a>

A *permissions policy* describes who has access to what\. This section explains the available options for creating permissions policies\. 

**Note**  
This documentation discusses using IAM in the context of ACM PCA\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)\. For information about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html)\. 

 You can use IAM to create policies that apply permissions to IAM users, groups, and roles\. These are called *identity–based policies*\. IAM offers the following types of identity–based policies: 
+  [Customer Managed Policies](auth-custmanagedpolicies.md) are policies that you create and manage in your AWS account and which you can attach to multiple users, groups, and roles\. You have more precise control when using customer managed policies than you have when using AWS managed policies\.
+  [Inline Policies](auth-inlinepolicies.md) are policies that you create and manage and which you embed directly into a single user, group, or role\.