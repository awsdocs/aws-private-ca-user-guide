# AWS Private CA best practices<a name="ca-best-practices"></a>

Best practices are recommendations that can help you use AWS Private CA effectively\. The following best practices are based on real\-world experience from current AWS Certificate Manager and AWS Private CA customers\. 

## Documenting CA structure and policies<a name="document-ca"></a>

AWS recommends documenting all of your policies and practices for operating your CA\. This might include:
+ Reasoning for your decisions about CA structure
+ A diagram showing your CAs and their relationships
+ Policies on CA validity periods
+ Planning for CA succession
+ Policies on path length
+ Catalog of permissions
+ Description of administrative control structures
+ Security

You can capture this information in two documents, known as Certification Policy \(CP\) and Certification Practices Statement \(CPS\)\. Refer to [RFC 3647](https://www.ietf.org/rfc/rfc3647.txt) for a framework for capturing important information about your CA operations\.

## Minimize use of the root CA if possible<a name="minimize-root-use"></a>

A root CA should in general only be used to issue certificates for intermediate CAs\. This allows the root CA to be stored out of harm's way while the intermediate CAs perform the daily task of issuing end\-entity certificates\.

However, if your organization's current practice is to issue end\-entity certificates directly from a root CA, AWS Private CA can support this workflow while improving security and operational controls\. Issuing end\-entity certificates in this scenario requires an IAM permissions policy that permits your root CA to use an end\-entity certificate template\. For information about IAM policies, see [Identity and Access Management \(IAM\) for AWS Private Certificate Authority](security-iam.md)\.

**Note**  
This configuration imposes limitations that might result in operational challenges\. For example, if your root CA is compromised or lost, you must create a new root CA and distribute it to all of the clients in your environment\. Until this recovery process is complete, you will not be able to issue new certificates\. Issuing certificates directly from a root CA also prevents you from restricting access and limiting the number of certificates issued from your root, which are both considered best practices for managing a root CA\. 

## Give the root CA its own AWS account<a name="isolate-root-account"></a>

Creating a root CA and subordinate CA in two different AWS accounts is a recommended best practice\. Doing so can provide you with additional protection and access controls for your root CA\. You can do so by exporting the CSR from the subordinate CA in one account, and signing it with a root CA in a different account\. The benefit of this approach is that you can separate control of your CAs by account\. The disadvantage is that you cannot use the AWS Management Console wizard to simplify the process of signing the CA certificate of a subordinate CA from your root CA\.

## Separate administrator and issuer roles<a name="role-separation"></a>

The CA administrator role should be separate from users who need only to issue end\-entity certificates\. If your CA administrator and certificate issuer reside in the same AWS account, you can limit issuer permissions by creating an IAM user specifically for that purpose\. 

## Implement managed revocation of certificates<a name="managed-revocation"></a>

Managed revocation automatically provides notice to certificate clients when a certificate has been revoked\. You might need to revoke a certificate if its cryptographic information has been compromised or if it was issued in error\. Clients typically refuse to accept revoked certificates\. AWS Private CA offers two standard options for managed revocation: Online Certificate Status Protocol \(OCSP\), and certificate revocation lists \(CRLs\)\. For more information, see [Setting up a certificate revocation method](revocation-setup.md)\.

## Turn on AWS CloudTrail<a name="use-cloudtrail"></a>

Turn on CloudTrail logging before you create and start operating a private CA\. With CloudTrail, you can retrieve a history of AWS API calls for your account to monitor your AWS deployments\. This history includes API calls made from the AWS Management Console, the AWS SDKs, the AWS Command Line Interface, and higher\-level AWS services\. You can also identify which users and accounts called the PCA API operations, the source IP address the calls were made from, and when the calls occurred\. You can integrate CloudTrail into applications using the API, automate trail creation for your organization, check the status of your trails, and control how administrators turn CloudTrail logging on and off\. For more information, see [Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)\. Go to [Using CloudTrail](PcaCtIntro.md) to see example trails for AWS Private CA operations\. 

## Rotate the CA private key<a name="rotate-keys"></a>

It is a best practice to periodically update the private key for your private CA\. You can update a key by importing a new CA certificate, or you can replace the private CA with a new CA\.

## Delete unused CAs<a name="delete-unused-ca"></a>

You can permanently delete a private CA\. You might want to do so if you no longer need the CA or if you want to replace it with a CA that has a newer private key\. To safely delete a CA, we recommend that you follow the process outlined in [Deleting your private CA](PCADeleteCA.md)\.

**Note**  
AWS bills you for a CA until it has been deleted\.

## Block public access to your CRLs<a name="bpa-crl"></a>

AWS Private CA recommends using the Amazon S3 Block Public Access \(BPA\) feature on buckets that contain CRLs\. This avoids unnecessarily exposing details of your private PKI to potential adversaries\. BPA is an S3 [best practice](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html) and is enabled by default on new buckets\. Additional setup is needed in some cases\. For more information, see [Enabling the S3 Block Public Access \(BPA\) feature](crl-planning.md#s3-bpa)\.

## Amazon EKS application best practices<a name="kubernetes"></a>

When using AWS Private CA to provision Amazon EKS with X\.509 certificates, follow the recommendations for securing multi\-tenant environments in the [Amazon EKS Best Practices Guides](https://aws.github.io/aws-eks-best-practices/security/docs/multitenancy/#kubernetes-as-a-service)\. For general information about integrating AWS Private CA with Kubernetes, see [Securing Kubernetes with AWS Private CA](PcaKubernetes.md)\.