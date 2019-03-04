# ACM PCA API Permissions: Actions and Resources Reference<a name="auth-apipermissions"></a>

 When you are setting up access control and writing permissions policies that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The first column in the table lists each ACM PCA API operation\. You specify actions in a policy's `Action` element\. The remaining columns provide the additional information\. 

 You can use the IAM policy elements in your ACM PCA policies to express conditions\. For a complete list, see [Available Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. 

**Note**  
To specify an action, use the `acm-pca:` prefix followed by the API operation name \(for example, `acm-pca:IssueCertificate`\)\. 

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.


**ACM PCA API Operations and Permissions**  

| ACM PCA API Operations | Required Permissions \(API Actions\) | Resources | 
| --- | --- | --- | 
|  [CreateCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_CreateCertificateAuthority.html)  |  `acm-pca:CreateCertificateAuthority`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [CreateCertificateAuthorityAuditReport](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_CreateCertificateAuthorityAuditReport.html)  |  `acm-pca:CreateCertificateAuthorityAuditReport`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [DeleteCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DeleteCertificateAuthority.html)  |  `acm-pca:DeleteCertificateAuthority`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [DescribeCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DescribeCertificateAuthority.html)  |  `acm-pca:DescribeCertificateAuthority`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [DescribeCertificateAuthorityAuditReport](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DescribeCertificateAuthorityAuditReport.html)  |  `acm-pca:DescribeCertificateAuthorityAuditReport`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [GetCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificate.html)  |  `acm-pca:GetCertificate`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [GetCertificateAuthorityCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificateAuthorityCertificate.html)  |  `acm-pca:GetCertificateAuthorityCertificate`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [GetCertificateAuthorityCsr](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetCertificateAuthorityCsr.html)  |  `acm-pca:GetCertificateAuthorityCsr`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [ImportCertificateAuthorityCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_ImportCertificateAuthorityCertificate.html)  |  `acm-pca:ImportCertificateAuthorityCertificate`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html)  |  `acm-pca:IssueCertificate`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [ListCertificateAuthorities](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_ListCertificateAuthorities.html)  |  `acm-pca:ListCertificateAuthorities`  |  N/A  | 
|  [ListTags](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_ListTags.html)  |  `acm-pca:ListTags`  |  N/A  | 
|  [RevokeCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_RevokeCertificate.html)  |  `acm-pca:RevokeCertificate`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [TagCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_TagCertificateAuthority.html)  |  `acm-pca:TagCertificateAuthority`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [UntagCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_UntagCertificateAuthority.html)  |  `acm-pca:UntagCertificateAuthority`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 
|  [UpdateCertificateAuthority](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_UpdateCertificateAuthority.html)  |  `acm-pca:UpdateCertificateAuthority`  |  `arn:aws:acm-pca:AWS_Region:AWS_Account:certificate-authority/certificate_ID`  | 