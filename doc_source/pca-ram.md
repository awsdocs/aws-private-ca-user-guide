# Attach a Policy for Cross\-Account Access<a name="pca-ram"></a>

When the CA administrator and the certificate issuer reside in different AWS accounts, the CA administrator must share CA access\. This is accomplished by attaching a resource\-based policy to the CA\. The policy grants issuance permissions to a specific principal, which may be an AWS account owner, an IAM user, an AWS Organizations ID, or an organizational unit ID\. 

Sharing can be done in the management console using AWS Resource Access Manager \(RAM\), which is a standard method for sharing AWS resources across accounts\. When you share a CA resource in AWS RAM with a principal in another account, the required resource\-based policy is attached to the CA automatically\. For more information about RAM, see the [AWS RAM User Guide](https://docs.aws.amazon.com/ram/latest/userguide/)\.

Alternatively, the CA administrator can attach a policy to a CA manually\. This is done using the PCA APIs [PutPolicy](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_PutPolicy.html), [GetPolicy](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_GetPolicy.html), and [DeletePolicy](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_DeletePolicy.html), or the corresponding AWS CLI commands [put\-policy](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/put-policy.html), [get\-policy](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-policy.html), and [delete\-policy](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/delete-policy.html)\.

**Cross\-Account Case 1: Issuing a managed certificate using the console**  
In this case, the CA administrator uses RAM to share CA access with another AWS account, enabling that account to issue managed ACM certificates\. The diagram shows that RAM can share the CA directly with the account, or indirectly through an AWS Organizations ID in which the account is a member\.

![\[Cross-account issuance with the console\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/ca_access_2_accounts_console.png)

After RAM shares a resource through AWS Organizations, the recipient principal must accept the share for it to take effect\. The recipient can configure AWS Organizations to accept offered shares automatically\.

**Note**  
The recipient account is responsible for configuring auto\-renewal in ACM\. Typically, on the first occasion a shared CA is used, ACM installs a service\-linked role that enables it to make unattended certificate calls on ACM Private CA\. If this fails \(usually due to a missing permission\), certificates from the CA will not renew automatically, and only the ACM user can resolve the problem, not the CA administrator\. For more information, see [Using a Service Linked Role \(SLR\) with ACM](https://docs.aws.amazon.com/acm/latest/userguide/acm-slr.html)\.

**Cross\-Account Case 2: Issuing managed and unmanaged certificates using the API/CLI**  
This second case demonstrates the sharing and issuance options that are possible using the AWS Certificate Manager and ACM Private CA APIs\. All of these operations can also be carried out using the corresponding AWS CLI commands\.

![\[Cross-account issuance using the APIs\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/ca_access_2_accounts_api_options.png)

Because the APIs are being used directly in this example, the certificate issuer has a choice of two APIs to issue a certificate\. The PCA API action `IssueCertificate` results in an unmanaged certificate that will not be automatically renewed and must be exported and manually installed\. The ACM API action [RequestCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_RequestCertificate.html) results in a managed certificate that can be easily installed on ACM\-integrated services and renews automatically\. 

**Note**  
The recipient account is responsible for configuring auto\-renewal in ACM\. Typically, on the first occasion a shared CA is used, ACM installs a service\-linked role that allows it to make unattended certificate calls on ACM Private CA\. If this fails \(usually due to a missing permission\), certificates from the CA will not renew automatically, and only the ACM user can resolve the problem, not the CA administrator\. For more information, see [Using a Service Linked Role \(SLR\) with ACM](https://docs.aws.amazon.com/acm/latest/userguide/acm-slr.html)\.
