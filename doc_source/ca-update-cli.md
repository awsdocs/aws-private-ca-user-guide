# Updating a CA \(CLI\)<a name="ca-update-cli"></a>

The following procedures show how to update the status and [revocation configuration](revocation-setup.md) of an existing CA using the AWS CLI\.

**Note**  
Changes to the revocation configuration of a CA do not affect certificates that were already issued\. For managed revocation to work, older certificates must be re\-issued\.

**To update the status of your private CA \(AWS CLI\)**  
Use the [update\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/update-certificate-authority.html) command\. 

This is useful when you have an existing CA with status `DISABLED` that you want to set to `ACTIVE`\. To begin, confirm the initial status of the CA with the following command\.

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/CA_ID" \
      --output json
```

This results in output similar to the following\.

```
{
    "CertificateAuthority": {
        "Arn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
        "CreatedAt": "2021-03-05T14:24:12.867000-08:00",
        "LastStateChangeAt": "2021-03-08T13:17:40.221000-08:00",
        "Type": "ROOT",
        "Serial": "serial_number",
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
                "S3BucketName": "DOC-EXAMPLE-BUCKET1"
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
$ aws acm-pca update-certificate-authority \
      --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
      --status "ACTIVE"
```

Inspect the new status of the CA\.

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/CA_ID" \
      --output json
```

The status now appears as `ACTIVE`\.

```
{
    "CertificateAuthority": {
        "Arn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
        "CreatedAt": "2021-03-05T14:24:12.867000-08:00",
        "LastStateChangeAt": "2021-03-08T13:23:09.352000-08:00",
        "Type": "ROOT",
        "Serial": "serial_number",
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
                "S3BucketName": "DOC-EXAMPLE-BUCKET1"
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

1. Use the following command to inspect the current status of the CA\.

   ```
   $ aws acm-pca describe-certificate-authority 
   	--certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID
   	--output json
   ```

   The output confirms that the CA has status `ACTIVE` but is not configured to use a CRL\.

   ```
   {
       "CertificateAuthority": {
           "Arn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
           "CreatedAt": "2021-03-08T14:36:26.449000-08:00",
           "LastStateChangeAt": "2021-03-08T14:50:52.224000-08:00",
           "Type": "ROOT",
           "Serial": "serial_number",
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

1. Create and save a file with a name such as `revoke_config.txt` to define your CRL configuration parameters\.

   ```
   {
      "CrlConfiguration":{
         "Enabled":true,
         "ExpirationInDays":7,
         "S3BucketName":"DOC-EXAMPLE-BUCKET"
      }
   }
   ```

1. Use the [update\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/update-certificate-authority.html) command and the revocation configuration file to update the CA\. 

   ```
   $ aws acm-pca update-certificate-authority \
         --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
         --revocation-configuration file://revoke_config.txt
   ```

1. Again inspect the status of the CA\.

   ```
   $ aws acm-pca describe-certificate-authority 
   	--certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID
   	--output json
   ```

   The output confirms that CA is now configured to use a CRL\.

   ```
   {
       "CertificateAuthority": {
           "Arn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
           "CreatedAt": "2021-03-08T14:36:26.449000-08:00",
           "LastStateChangeAt": "2021-03-08T14:50:52.224000-08:00",
           "Type": "ROOT",
           "Serial": "serial_numbner",
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
                   "S3BucketName": "DOC-EXAMPLE-BUCKET1",
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

1. Create and save a file with a name such as `revoke_config.txt` to define your OCSP parameters\.

   ```
   {
      "OcspConfiguration":{
         "Enabled":true
      }
   }
   ```

1. Use the [update\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/update-certificate-authority.html) command and the revocation configuration file to update the CA\. 

   ```
   $ aws acm-pca update-certificate-authority \
         --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
         --revocation-configuration file://revoke_config.txt
   ```

1. Again inspect the status of the CA\.

   ```
   $ aws acm-pca describe-certificate-authority 
   	--certificate-authority-arnarn:aws:acm-pca:region:account:certificate-authority/CA_ID
   	--output json
   ```

   The output confirms that CA is now configured to use OCSP\.

   ```
   {
       "CertificateAuthority": {
           "Arn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
           "CreatedAt": "2021-03-08T14:36:26.449000-08:00",
           "LastStateChangeAt": "2021-03-08T14:50:52.224000-08:00",
           "Type": "ROOT",
           "Serial": "serial_number",
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