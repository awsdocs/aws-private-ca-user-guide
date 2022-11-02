# Inline policies<a name="auth-InlinePolicies"></a>

Inline policies are policies that you create and manage and embed directly into a user, group, or role\. The following policy examples show how to assign permissions to perform AWS Private CA actions\. For general information about inline policies, see [Working with Inline Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_inline-using.html) in the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\. You can use the AWS Management Console, the AWS Command Line Interface \(AWS CLI\), or the IAM API to create and embed inline policies\. 

**Topics**
+ [Listing private CAs](#policy-list-pcas)
+ [Retrieving a private CA certificate](#policy-retrieve-pca)
+ [Importing a private CA certificate](#policy-import-pca-cert)
+ [Deleting a private CA](#policy-delete-pca)
+ [Attaching tags to a CA at the time of creation](#policy-tag-ca)
+ [Read\-only access to AWS Private CA](#policy-pca-read-only)
+ [Full access to AWS Private CA](#policy-pca-full-access)
+ [Administrator access to all AWS resources](#policy-aws-administrator)

## Listing private CAs<a name="policy-list-pcas"></a>

 The following policy allows a user to list all of the private CAs in an account\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"acm-pca:ListCertificateAuthorities",
         "Resource":"*"
      }
   ]
}
```

## Retrieving a private CA certificate<a name="policy-retrieve-pca"></a>

 The following policy allows a user to retrieve a specific private CA certificate\. 

```
{
   "Version":"2012-10-17",
   "Statement":{
      "Effect":"Allow",
      "Action":"acm-pca:GetCertificateAuthorityCertificate",
      "Resource":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
   }
}
```

## Importing a private CA certificate<a name="policy-import-pca-cert"></a>

The following policy allows a user to import a private CA certificate\. 

```
{
   "Version":"2012-10-17",
   "Statement":{
      "Effect":"Allow",
      "Action":"acm-pca:ImportCertificateAuthorityCertificate",
      "Resource":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
   }
}
```

## Deleting a private CA<a name="policy-delete-pca"></a>

The following policy allows a user to delete a specific private CA\.

```
{
   "Version":"2012-10-17",
   "Statement":{
      "Effect":"Allow",
      "Action":"acm-pca:DeleteCertificateAuthority",
      "Resource":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
   }
}
```

## Attaching tags to a CA at the time of creation<a name="policy-tag-ca"></a>

The following policy allows a user to add tags to a CA\.

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Action": [
            "acm-pca:CreateCertificateAuthority",
            "acm-pca:TagCertificateAuthority"
         ],
         "Effect": "Allow",
         "Resource": "*"
      }
   ]  
}
```

## Read\-only access to AWS Private CA<a name="policy-pca-read-only"></a>

 The following policy allows a user to describe and list private certificate authorities and to retrieve the private CA certificate and certificate chain\. 

```
{
   "Version":"2012-10-17",
   "Statement":{
      "Effect":"Allow",
      "Action":[
         "acm-pca:DescribeCertificateAuthority",
         "acm-pca:DescribeCertificateAuthorityAuditReport",
         "acm-pca:ListCertificateAuthorities",
         "acm-pca:ListTags",
         "acm-pca:GetCertificateAuthorityCertificate",
         "acm-pca:GetCertificateAuthorityCsr",
         "acm-pca:GetCertificate"
      ],
      "Resource":"*"
   }
}
```

## Full access to AWS Private CA<a name="policy-pca-full-access"></a>

 The following policy allows a user to perform any AWS Private CA action\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "acm-pca:*"
         ],
         "Resource":"*"
      }
   ]
}
```

## Administrator access to all AWS resources<a name="policy-aws-administrator"></a>

 The following policy allows a user to perform any action on any AWS resource\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"*",
         "Resource":"*"
      }
   ]
}
```