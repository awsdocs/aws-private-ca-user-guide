# Inline Policies<a name="auth-inlinepolicies"></a>

Inline policies are policies that you create and manage and embed directly into a user, group, or role\. The following policy examples show how to assign permissions to perform ACM PCA actions\. For general information about inline policies, see [Working with Inline Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_inline-using.html) in the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\. You can use the AWS Management Console, the AWS Command Line Interface \(AWS CLI\), or the IAM API to create and embed inline policies\. 

**Topics**
+ [Listing Private CAs](#policy-list-pcas)
+ [Retrieving a Private CA Certificate](#policy-retrieve-pca)
+ [Importing a Private CA Certificate](#policy-import-pca-cert)
+ [Deleting a Private CA](#policy-delete-pca)
+ [Read\-Only Access to ACM PCA](#policy-pca-read-only)
+ [Full Access to ACM PCA](#policy-pca-full-access)
+ [Administrator Access to All AWS Resources](#policy-aws-administrator)

## Listing Private CAs<a name="policy-list-pcas"></a>

 The following policy allows a user to list all of the private CAs in an account\. 

```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "acm-pca:ListCertificateAuthorities",
    "Resource": "*"
  }]
}
```

## Retrieving a Private CA Certificate<a name="policy-retrieve-pca"></a>

 The following policy allows a user to retrieve a specific private CA certificate\. 

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "acm-pca:GetCertificateAuthorityCertificate",
    "Resource": "arn:aws:acm:AWS_Region:AWS_Account:certificate-authority/12345678-1234-1234-1234-123456789012"
  }
}
```

## Importing a Private CA Certificate<a name="policy-import-pca-cert"></a>

The following policy allows a user to import a private CA certificate\. 

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "acm-pca:ImportCertificateAuthorityCertificate",
    "Resource": "arn:aws:acm:AWS_Region:AWS_Account:certificate/12345678-1234-1234-1234-123456789012"
  }
}
```

## Deleting a Private CA<a name="policy-delete-pca"></a>

 The following policy allows a user to delete a specific private CA\. 

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "acm-pca:DeleteCertificateAuthority",
    "Resource": "arn:aws:acm:AWS_Region:AWS_Account:certificate/12345678-1234-1234-1234-123456789012"
  }
}
```

## Read\-Only Access to ACM PCA<a name="policy-pca-read-only"></a>

 The following policy allows a user to describe and list private certificate authorities and to retrieve the private CA Certificate and certificate chain\. 

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": [
      "acm-pca:DescribeCertificateAuthority",
      "acm-pca:DescribeCertificateAuthorityAuditReport",
      "acm-pca:ListCertificateAuthorities",
      "acm-pca:ListTags",
      "acm-pca:GetCertificateAuthorityCertificate",
      "acm-pca:GetCertificateAuthorityCsr",
      "acm-pca:GetCertificate"
    ],
    "Resource": "*"
  }
}
```

## Full Access to ACM PCA<a name="policy-pca-full-access"></a>

 The following policy allows a user to perform any ACM PCA action\. 

```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": ["acm-pca:*"],
    "Resource": "*"
  }]
}
```

## Administrator Access to All AWS Resources<a name="policy-aws-administrator"></a>

 The following policy allows a user to perform any action on any AWS resource\. 

```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }]
}
```