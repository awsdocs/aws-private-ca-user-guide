# AWS Private CA API operations and permissions<a name="api-permissions"></a>

When you set up access control and permissions policies that you plan to attach to an IAM identity \(identity\-based policies\), use the following table as a reference\. The first column in the table lists each AWS Private CA API operation\. You specify actions in a policy's `Action` element\. The remaining columns provide the additional information\.


| AWS Private CA API operations | Required permissions | Resources | 
| --- | --- | --- | 
|  [CreateCertificateAuthority](https://docs.aws.amazon.com/privateca/latest/APIReference/API_CreateCertificateAuthority.html)  |  `acm-pca:CreateCertificateAuthority` `acm-pca:TagCertificateAuthority` \(Only required when creating a CA with tags\.\)  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [CreateCertificateAuthorityAuditReport](https://docs.aws.amazon.com/privateca/latest/APIReference/API_CreateCertificateAuthorityAuditReport.html)  |  `acm-pca:CreateCertificateAuthorityAuditReport`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
| [CreatePermission](https://docs.aws.amazon.com/privateca/latest/APIReference/API_CreatePermission.html) | acm\-pca:CreatePermission | arn:aws:acm\-pca:region:account:certificate\-authority/CA\_ID | 
|  [DeleteCertificateAuthority](https://docs.aws.amazon.com/privateca/latest/APIReference/API_DeleteCertificateAuthority.html)  |  `acm-pca:DeleteCertificateAuthority`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
| [DeletePermission](https://docs.aws.amazon.com/privateca/latest/APIReference/API_DeletePermission.html) | acm\-pca:DeletePermission | arn:aws:acm\-pca:region:account:certificate\-authority/CA\_ID | 
| [DeletePolicy](https://docs.aws.amazon.com/privateca/latest/APIReference/API_DeletePolicy.html) | acm\-pca:DeletePolicy | arn:aws:acm\-pca:region:account:certificate\-authority/CA\_ID | 
|  [DescribeCertificateAuthority](https://docs.aws.amazon.com/privateca/latest/APIReference/API_DescribeCertificateAuthority.html)  |  `acm-pca:DescribeCertificateAuthority`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [DescribeCertificateAuthorityAuditReport](https://docs.aws.amazon.com/privateca/latest/APIReference/API_DescribeCertificateAuthorityAuditReport.html)  |  `acm-pca:DescribeCertificateAuthorityAuditReport`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [GetCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_GetCertificate.html)  |  `acm-pca:GetCertificate`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [GetCertificateAuthorityCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_GetCertificateAuthorityCertificate.html)  |  `acm-pca:GetCertificateAuthorityCertificate`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [GetCertificateAuthorityCsr](https://docs.aws.amazon.com/privateca/latest/APIReference/API_GetCertificateAuthorityCsr.html)  |  `acm-pca:GetCertificateAuthorityCsr`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
| [GetPolicy](https://docs.aws.amazon.com/privateca/latest/APIReference/API_GetPolicy.html) | acm\-pca:GetPolicy | arn:aws:acm\-pca:region:account:certificate\-authority/CA\_ID | 
|  [ImportCertificateAuthorityCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_ImportCertificateAuthorityCertificate.html)  |  `acm-pca:ImportCertificateAuthorityCertificate`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [IssueCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_IssueCertificate.html)  |  `acm-pca:IssueCertificate`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [ListCertificateAuthorities](https://docs.aws.amazon.com/privateca/latest/APIReference/API_ListCertificateAuthorities.html)  |  `acm-pca:ListCertificateAuthorities`  |  N/A  | 
| [ListPermissions](https://docs.aws.amazon.com/privateca/latest/APIReference/API_ListPermissions.html) | acm\-pca:ListPermissions | arn:aws:acm\-pca:region:account:certificate\-authority/CA\_ID | 
|  [ListTags](https://docs.aws.amazon.com/privateca/latest/APIReference/API_ListTags.html)  |  `acm-pca:ListTags`  |  N/A  | 
| [PutPolicy](https://docs.aws.amazon.com/privateca/latest/APIReference/API_PutPolicy.html) | acm\-pca:PutPolicy | arn:aws:acm\-pca:region:account:certificate\-authority/CA\_ID | 
|  [RevokeCertificate](https://docs.aws.amazon.com/privateca/latest/APIReference/API_RevokeCertificate.html)  |  `acm-pca:RevokeCertificate`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [TagCertificateAuthority](https://docs.aws.amazon.com/privateca/latest/APIReference/API_TagCertificateAuthority.html)  |  `acm-pca:TagCertificateAuthority`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [UntagCertificateAuthority](https://docs.aws.amazon.com/privateca/latest/APIReference/API_UntagCertificateAuthority.html)  |  `acm-pca:UntagCertificateAuthority`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 
|  [UpdateCertificateAuthority](https://docs.aws.amazon.com/privateca/latest/APIReference/API_UpdateCertificateAuthority.html)  |  `acm-pca:UpdateCertificateAuthority`  |  `arn:aws:acm-pca:region:account:certificate-authority/CA_ID`  | 

You can use IAM to create policies that apply permissions to IAM users, groups, and roles\. These are called *identity\-based policies*\. IAM offers the following types of identity\-based policies: 
+ [AWS managed policies](auth-AwsManagedPolicies.md) are policies available by default with AWS Private CA\. These policies cover basic user roles\.
+ [Customer managed policies](auth-CustManagedPolicies.md) are policies that you create and manage in your AWS account and which you can attach to multiple users, groups, and roles\. You have more precise control when using customer managed policies than you have when using AWS managed policies\.
+ [Inline policies](auth-InlinePolicies.md) are policies that you create and manage and which you embed directly into a single user, group, or role\.
+ [Resource\-based policies](pca-rbp.md) are used by AWS Private CA to enable cross\-account access to private CAs\.