# Updating Your Private CA<a name="PCAUpdateCA"></a>

After creating a private CA, you can update its status or change its revocation configuration\. This topic provides details about CA status and the CA lifecycle, along with examples of console and CLI updates to CAs\. For additional information about managed revocation options, see [\(Optional\) Configure revocation](PcaCreateCa.md#PcaCreateRevocation)\.

## Understanding CA Status<a name="PcaUpdateStatus"></a>

The status of a CA that is managed by ACM Private CA results from a user action or, in some cases, from a service action\. For example, a CA status changes when it expires\. The status options available to CA administrators vary depending on the current status of the CA\.

ACM Private CA can report the following status values: 
+ **`CREATING`** – ACM Private CA is creating your private certificate authority\.
+ **`PENDING_CERTIFICATE`** – The CA has been created and now needs a certificate to be operational\. You must use your ACM Private CA\-hosted or on\-premises root or subordinate CA to sign your private CA CSR and then import it into ACM Private CA\. For more information, see [Creating and Installing the Certificate for a Private CA](PCACertInstall.md)\.
+ **`ACTIVE`** – Your private CA is active and can be used to sign and issue certificates\.
+ **`DISABLED`** – A disabled CA cannot issue new certificates\. While disabled, the CA supports revocation for certificates issued previously, and it continues to generate certificate revocation lists \(CRLs\)\.
+ **`EXPIRED`** – If the CA certificate for your private CA expires, ACM Private CA sets the status to `EXPIRED`\.

  Considerations:
  + CA certificates are not automatically renewed\. For information about automating renewal through AWS Certificate Manager, see [Assign Certificate Renewal Permissions to ACM](assign-permissions.md#PcaPermissions)\. 
  + An expired CA no longer generates CRLs\.
  + Audit reports continue to work for an expired CA\.
  + Your account continues to be billed for an expired CA\.
  + You cannot directly change the status of an expired CA\. If you import a new certificate for the CA, its status returns to its state before it expired, either ACTIVE or DISABLED\.
  + If you import a new CA certificate for your private CA, ACM Private CA resets the status to `ACTIVE` unless you set it to `DISABLED` after the CA certificate expired\.
  + If you attempt to issue a new certificate with an expired CA, the `IssueCertificate` API returns `InvalidStateException`\. An expired root CA must self\-sign a new root CA certificate before it can issue new subordinate certificates\.
  + `The ListCertificate Authorities` and `DescribeCertificateAuthority` APIs return a status of `EXPIRED` if the CA certificate is expired, regardless of whether the CA status is set to `ACTIVE` or `DISABLED`\. However, if the expired CA has been set to `DELETED`, the status returned is `DELETED`\.
  + The `UpdateCertificateAuthority` API cannot update the status of an expired CA\.
  + The `RevokeCertificate` API can be used to revoke any expired certificate, including a CA certificate\.
+ **`FAILED`** – The `CreateCertificateAuthority` action failed\. This can occur because of a network outage, back\-end AWS failure, or other errors\. 

  Considerations:
  + A failed CA cannot be recovered\. Delete the CA and create a new one\.
+ **`DELETED` **– Your private CA is within the restoration period\. which can have a length of seven to 30 days\. After this period, it is permanently deleted\.

  Considerations:
  + If you call the `RestoreCertificateAuthority` API on a CA with `DELETED` status and an expired certificate, the CA will be set to `EXPIRED`\.
  + For more information about deleting a CA, see [Deleting Your Private CA](PCADeleteCA.md)\.

**Note**  
For all status values except `DELETED` and `FAILED`, you are billed for the CA\.

The following diagram illustrates the CA lifecycle as an interaction of management actions with CA status\.

 

![\[Interaction of CA management actions and status.\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/status.png)

At the top of the diagram, management actions are applied through the ACM Private CA console, CLI, or API\. The actions take the CA through creation, activation, expiration and renewal\. The CA status changes in response \(as shown by the solid lines\) to manual actions or automated updates\. In most cases, a new status leads to a new possible action \(shown by a dotted line\) that the CA administrator can apply\. The lower\-right inset shows the possible status values permitting delete and restore actions\.

## Updating a CA \(Console\)<a name="ca-update-console"></a>

The following procedures show to update existing CA configurations using the console\.

### Updating CA Status \(Console\)<a name="ca-update-console-status"></a>

You can use the AWS Management Console to update the status of a CA\.

**To update CA status \(console\)**

1. Sign in to your AWS account and open the ACM Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Choose **Private CAs**\.

1. Choose your private CA from the list\.

1. On the **Actions** menu, choose **Disable** to disable a private CA that's currently active or choose **Enable** to set a CA active\. 

### Updating a CA's CRL Configuration \(Console\)<a name="ca-update-console-configuration"></a>

You can update the Certificate Revocation List \(CRL\) configuration for your private CA to change any of the following values:
+ Whether the private CA generates a certificate revocation list \(CRL\)
+ The number of days before a CRL expires\. Note that ACM Private CA begins trying to regenerate the CRL at ½ the number of days you specify\. 
+ The name of the Amazon S3 bucket where your CRL is saved\.
+ An alias to hide the name of your S3 bucket from public view\.

**Important**  
Changing any of the preceding parameters can have negative effects\. Examples include disabling CRL generation, changing the validity period, or changing the S3 bucket after you have placed your private CA in production\. Such changes can break existing certificates that depend on the CRL and the current CRL configuration\. Changing the alias can be done safely as long as the old alias remains linked to the correct bucket\. 

**To update the CRL configuration for a CA \(console\)**

1. Sign in to your AWS account and open the ACM Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Choose **Private CAs**\.

1. Choose your private CA from the list\.

1. On the **Actions** menu, choose **Update CA revocation**\.

1. Select **Enable CRL distribution** to generate certificate revocation lists \(CRLs\)\.

1. For **Create a new S3 bucket**, choose **Yes** and enter a unique bucket name or choose **No** and choose an existing bucket from the list\. 

1. For **Custom CRL Name**, enter an alias to hide your S3 bucket name from public view\.

1. For **Valid for**, enter a validity period in days\.

1. Choose **Update**\.

## Updating a CA \(AWS CLI\)<a name="ca-update-cli"></a>

The following procedures show how to update the configuration of an existing CA using the AWS CLI\.

**To update the status of your private CA \(AWS CLI\)**  
Use the [update\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/update-certificate-authority.html) command\. 

This is useful when you have an existing CA with status `DISABLED` that you want to set to `ACTIVE`\. To begin, confirm the initial status of the CA with the following command:

```
aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123-456789abcdef" \
      --output json
```

This results in output similar to the following:

```
{
    "CertificateAuthority": {
        "Arn": "arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123-456789abcdef",
        "CreatedAt": "2021-03-05T14:24:12.867000-08:00",
        "LastStateChangeAt": "2021-03-08T13:17:40.221000-08:00",
        "Type": "ROOT",
        "Serial": "173039654744616464971164424001052828114",
        "Status": "DISABLED",
        "NotBefore": "2021-03-08T07:46:27-08:00",
        "NotAfter": "2022-03-08T08:46:27-08:00",
        "CertificateAuthorityConfiguration": {
            "KeyAlgorithm": "RSA_2048",
            "SigningAlgorithm": "SHA256WITHRSA",
            "Subject": {
                "Country": "US",
                "Organization": "Example Corp",
                "OrganizationalUnit": "Sales",
                "State": "WA",
                "CommonName": "www.example.com",
                "Locality": "Seattle"
            }
        },
        "RevocationConfiguration": {
            "CrlConfiguration": {
                "Enabled": true,
                "ExpirationInDays": 7,
                "CustomCname": "alternative.example.com",
                "S3BucketName": "my-bucket"
			},
            "OcspConfiguration": {
                "Enabled": false
            }
        }
    }
}
```

The following command sets the status of the private CA to `ACTIVE`\. This is possible only if a valid certificate is installed on the CA\.

```
aws acm-pca update-certificate-authority \
      --certificate-authority-arn arn:aws:acm-pca:region:account:\certificate-authority/01234567-89ab-cdef-0123-456789abcdef \
      --status "ACTIVE"
```

Inspect the new status of the CA:

```
aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/01234567-89ab-cdef-0123-456789abcdef" \
      --output json
```

The status now appears as `ACTIVE`:

```
{
    "CertificateAuthority": {
        "Arn": "arn:aws:acm-pca:<region>:<account>:certificate-authority/01234567-89ab-cdef-0123-456789abcdef",
        "CreatedAt": "2021-03-05T14:24:12.867000-08:00",
        "LastStateChangeAt": "2021-03-08T13:23:09.352000-08:00",
        "Type": "ROOT",
        "Serial": "173039654744616464971164424001052828114",
        "Status": "ACTIVE",
        "NotBefore": "2021-03-08T07:46:27-08:00",
        "NotAfter": "2022-03-08T08:46:27-08:00",
        "CertificateAuthorityConfiguration": {
            "KeyAlgorithm": "RSA_2048",
            "SigningAlgorithm": "SHA256WITHRSA",
            "Subject": {
                "Country": "US",
                "Organization": "Example Corp",
                "OrganizationalUnit": "Sales",
                "State": "WA",
                "CommonName": "www.example.com",
                "Locality": "Seattle"
            }
        },
        "RevocationConfiguration": {
            "CrlConfiguration": {
                "Enabled": true,
                "ExpirationInDays": 7,
                "CustomCname": "alternative.example.com",
                "S3BucketName": "my-bucket"
            },
            "OcspConfiguration": {
                "Enabled": false
            }
        }
    }
}
```

In some cases, you might have an active CA with no revocation mechanism configured\. If you want to begin using a certificate revocation list \(CRL\), use the following procedure\.

**To add a CRL to an existing CA \(AWS CLI\)**

1. Use the following command to inspect the current status of the CA:

   ```
   aws acm-pca describe-certificate-authority 
   	--certificate-authority-arn arn:aws:acm-pca:<region>:<account>:certificate-authority/01234567-89ab-cdef-0123-456789abcdef
   	--output json
   ```

   The output confirms that the CA has status `ACTIVE` but is not configured to use a CRL:

   ```
   {
       "CertificateAuthority": {
           "Arn": "arn:aws:acm-pca:<region>:<account>:certificate-authority/01234567-89ab-cdef-0123-456789abcdef",
           "CreatedAt": "2021-03-08T14:36:26.449000-08:00",
           "LastStateChangeAt": "2021-03-08T14:50:52.224000-08:00",
           "Type": "ROOT",
           "Serial": "326494291123486153192532571949008665597",
           "Status": "ACTIVE",
           "NotBefore": "2021-03-08T13:46:50-08:00",
           "NotAfter": "2022-03-08T14:46:50-08:00",
           "CertificateAuthorityConfiguration": {
               "KeyAlgorithm": "RSA_2048",
               "SigningAlgorithm": "SHA256WITHRSA",
               "Subject": {
                   "Country": "US",
                   "Organization": "Example Corp",
                   "OrganizationalUnit": "Sales",
                   "State": "WA",
                   "CommonName": "www.example.com",
                   "Locality": "Seattle"
               }
           },
           "RevocationConfiguration": {
               "CrlConfiguration": {
                   "Enabled": false
               },
               "OcspConfiguration": {
                   "Enabled": false
               }
           }
       }
   }
   ```

1. Create and save a file with a name such as `revoke_config.txt` to define your CRL configuration parameters:

   ```
   {
      "CrlConfiguration":{
         "Enabled":true,
         "ExpirationInDays":7,
         "S3BucketName":"your-bucket-name"
      }
   }
   ```

1. Use the [update\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/update-certificate-authority.html) command and the revocation configuration file to update the CA\. 

   ```
   aws acm-pca update-certificate-authority \
         --certificate-authority-arn arn:aws:acm-pca:region:account:\certificate-authority/01234567-89ab-cdef-0123-456789abcdef \
         --revocation-configuration file://revoke_config.txt \
   ```

1. Again inspect the status of the CA:

   ```
   aws acm-pca describe-certificate-authority 
   	--certificate-authority-arn arn:aws:acm-pca:<region>:<account>:certificate-authority/01234567-89ab-cdef-0123-456789abcdef
   	--output json
   ```

   The output confirms that CA is now configured to use a CRL:

   ```
   {
       "CertificateAuthority": {
           "Arn": "arn:aws:acm-pca:<region>:<account>:certificate-authority/01234567-89ab-cdef-0123-456789abcdef",
           "CreatedAt": "2021-03-08T14:36:26.449000-08:00",
           "LastStateChangeAt": "2021-03-08T14:50:52.224000-08:00",
           "Type": "ROOT",
           "Serial": "326494291123486153192532571949008665597",
           "Status": "ACTIVE",
           "NotBefore": "2021-03-08T13:46:50-08:00",
           "NotAfter": "2022-03-08T14:46:50-08:00",
           "CertificateAuthorityConfiguration": {
               "KeyAlgorithm": "RSA_2048",
               "SigningAlgorithm": "SHA256WITHRSA",
               "Subject": {
                   "Country": "US",
                   "Organization": "Example Corp",
                   "OrganizationalUnit": "Sales",
                   "State": "WA",
                   "CommonName": "www.example.com",
                   "Locality": "Seattle"
               }
           },
           "RevocationConfiguration": {
               "CrlConfiguration": {
                   "Enabled": true,
                   "ExpirationInDays": 7,
                   "S3BucketName": "my-bucket",
               },
               "OcspConfiguration": {
                   "Enabled": false
               }
           }
       }
   }
   ```

   In some cases, you might want to add OCSP revocation support instead of enabling a CRL as in the previous procedure\. In that case, use the following steps\. 

**To add OCSP support to an existing CA \(AWS CLI\)**

1. Create and save a file with a name such as `revoke_config.txt` to define your OCSP parameters:

   ```
   {
      "OcspConfiguration":{
         "Enabled":true
      }
   }
   ```

1. Use the [update\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/update-certificate-authority.html) command and the revocation configuration file to update the CA\. 

   ```
   aws acm-pca update-certificate-authority \
         --certificate-authority-arn arn:aws:acm-pca:region:account:\certificate-authority/01234567-89ab-cdef-0123-456789abcdef \
         --revocation-configuration file://revoke_config.txt \
   ```

1. Again inspect the status of the CA:

   ```
   aws acm-pca describe-certificate-authority 
   	--certificate-authority-arn arn:aws:acm-pca:<region>:<account>:certificate-authority/01234567-89ab-cdef-0123-456789abcdef
   	--output json
   ```

   The output confirms that CA is now configured to use OCSP:

   ```
   {
       "CertificateAuthority": {
           "Arn": "arn:aws:acm-pca:<region>:<account>:certificate-authority/01234567-89ab-cdef-0123-456789abcdef",
           "CreatedAt": "2021-03-08T14:36:26.449000-08:00",
           "LastStateChangeAt": "2021-03-08T14:50:52.224000-08:00",
           "Type": "ROOT",
           "Serial": "326494291123486153192532571949008665597",
           "Status": "ACTIVE",
           "NotBefore": "2021-03-08T13:46:50-08:00",
           "NotAfter": "2022-03-08T14:46:50-08:00",
           "CertificateAuthorityConfiguration": {
               "KeyAlgorithm": "RSA_2048",
               "SigningAlgorithm": "SHA256WITHRSA",
               "Subject": {
                   "Country": "US",
                   "Organization": "Example Corp",
                   "OrganizationalUnit": "Sales",
                   "State": "WA",
                   "CommonName": "www.example.com",
                   "Locality": "Seattle"
               }
           },
           "RevocationConfiguration": {
               "CrlConfiguration": {
                   "Enabled": false
               },
               "OcspConfiguration": {
                   "Enabled": true
               }
           }
       }
   }
   ```

**Note**  
You can also configure both CRL and OCSP support on a CA\.

## Add Tags to your Private Certificate Authority<a name="PcaCaTagging"></a>

Tags are words or phrases that act as metadata for identifying and organizing AWS resources\. Each tag consists of a **key** and a **value**\. You can use the ACM Private CA console, AWS Command Line Interface \(AWS CLI\), or the PCA API to add, view, or remove tags for private CAs\. 

You can create custom tags that suit your needs\. For example, you could tag private CAs with key\-value pairs like `Environment=Prod` or `Environment=Beta` to identify which environment the CA is intended for\. You can add tags to a CA during creation or any time after\. For more information, see [Create a Private CA](PcaCreateCa.md)

Other AWS resources also support tagging\. You can assign the same tag to different resources to indicate whether those resources are related\. For example, you can assign a tag such as `Website=example.com` to your CA, the Elastic Load Balancing load balancer, and other related resources\. For more information on tagging AWS resources, see [Tagging your Amazon EC2 Resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html) in the [Amazon EC2 User Guide for Linux Instances](https://docs.aws.amazon.com/ec2/index.html#lang/en_us)\.

The following basic restrictions apply to ACM Private CA tags:
+ The maximum number of tags per private CA is 50\.
+ The maximum length of a tag key is 128 characters\.
+ The maximum length of a tag value is 256 characters\.
+ The tag key and value can contain the following characters: A\-Z, a\-z, and \.:\+=@\_%\-\(hyphen\)\.
+ Tag keys and values are case sensitive\.
+ The `aws:` and `rds:` prefixes are reserved for AWS use; you cannot add, edit, or delete tags whose key begins with `aws:` or `rds:`\. Default tags that begin with `aws:` and `rds:` do not count against your tags\-per\-resource quota\.
+ If you plan to use your tagging schema across multiple services and resources, remember that other services may have different restrictions for allowed characters\. Refer to the documentation for that service\. 
+ ACM Private CA tags are not available for use in the AWS Management Console's [Resource Groups and Tag Editor](https://aws.amazon.com/blogs/aws/resource-groups-and-tagging/)\. 

You can tag a private CA from the [ACM Private CA Console](https://console.aws.amazon.com/acm-pca), the [AWS Command Line Interface \(AWS CLI\)](https://aws.amazon.com/cli/), or the [ACM Private CA API](https://docs.aws.amazon.com/acm-pca/latest/APIReference/)\.

**To tag a private CA \(console\)**

1. Sign in to your AWS account and open the ACM Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Choose **Private CAs**\.

1. Choose your private CA from the list\.

1. Choose the **Tags** tab\.

1. Choose **Edit**\.

1. Type a key and value pair\.

1. Choose **Add Tag**\.

**To tag a private CA \(AWS CLI\)**  
Use the [tag\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/tag-certificate-authority.html) command to add tags to your private CA\. 

```
aws acm-pca tag-certificate-authority \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--tags Key=Admin,Value=Alice
```

Use the [list\-tags](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/list-tags.html) command to list the tags for a private CA\. 

```
aws acm-pca list-tags \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/01234567-89ab-cdef-0123456789ab \
--max-results 10
```

Use the [untag\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/untag-certificate-authority.html) command to remove tags from a private CA\. 

```
aws acm-pca untag-certificate-authority \
--certificate-authority-arn arn:aws:acm-pca:region:account:\
certificate-authority/12345678-1234-1234-1234-123456789012 \
--tags Key=Purpose,Value=Website
```