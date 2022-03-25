# Procedure for creating a CA \(CLI\)<a name="Create-CA-CLI"></a>

Use the [create\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/create-certificate-authority.html) command to create a private CA\. \(If you want to modify an existing CA using the AWS CLI, see [Updating a CA \(CLI\)](ca-update-cli.md)\.\) You must specify the CA configuration, the revocation configuration if you plan to use OCSP and/or a CRL, and the CA type\. This information is contained in two files that you supply as arguments to the command\. Optionally, you can also supply tags and an idempotency token\.

If you are configuring a CRL, you must have a secured Amazon S3 bucket in place *before* you issue the create\-certificate\-authority command\. For more information, see [Access policies for CRLs in Amazon S3 ](crl-planning.md#s3-policies)\.

The CA configuration file specifies the following information:
+ The name of the algorithm
+ The key size to be used to create the CA private key
+ The type of signing algorithm that the CA uses to sign
+ X\.500 subject information

The revocation configuration for OCSP defines an `OcspConfiguration` object with the following information:
+ The `Enabled` flag set to "true"\.
+ \(Optional\) A custom CNAME declared as a value for `OcspCustomCname`\.

The revocation configuration for a CRL defines a `CrlConfiguration` object with the following information:
+ The `Enabled` flag set to "true"\.
+ The CRL expiration period in days \(the validity period of the CRL\)\.
+ The Amazon S3 bucket that will contain the CRL\.
+ \(Optional\) An [https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_S3ObjectAcl.html](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_S3ObjectAcl.html) value that determines whether the CRL is publicly accessible\. In the example presented here, public access is blocked\. For more information, see [Enabling the S3 Block Public Access \(BPA\) feature](crl-planning.md#s3-bpa)\.
+ \(Optional\) A CNAME alias for the S3 bucket that is included in certificates issued by the CA\. If the CRL is not publicly accessible, this will point to a distribution mechanism such as Amazon CloudFront\.

**Note**  
You can enable both revocation mechanisms on the same CA by defining both an `OcspConfiguration` object and a `CrlConfiguration` object\. If you supply no \-\-revocation\-configuration parameter, both mechanisms are disabled by default\. If you need revocation validation support later, see [Updating a CA \(CLI\)](ca-update-cli.md)\.

The following examples assume that you have set up your `.aws` configuration directory with a valid default Region, endpoint, and credentials\. For information about configuring your AWS CLI environment, see [Configuration and credential file settings](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)\. For readability, we supply the CA configuration and revocation input as JSON files in the example commands\. Modify the example files as needed for your use\. 

All of the examples use the same `ca_config.txt`:

**File: ca\_config\.txt**

```
{
   "KeyAlgorithm":"RSA_2048",
   "SigningAlgorithm":"SHA256WITHRSA",
   "Subject":{
      "Country":"US",
      "Organization":"Example Corp",
      "OrganizationalUnit":"Sales",
      "State":"WA",
      "Locality":"Seattle",
      "CommonName":"www.example.com"
   }
}
```

## Example 1: Create a CA with OCSP enabled<a name="example_3"></a>

In this example, the revocation file enables default OCSP support, which uses the ACM Private CA responder to check certificate status\.

**File: revoke\_config\.txt for OCSP**

```
{
   "OcspConfiguration":{
      "Enabled":true
   }
}
```

**Command**

```
$ aws acm-pca create-certificate-authority \
     --certificate-authority-configuration file://ca_config.json \
     --revocation-configuration file://revoke_config.json \
     --certificate-authority-type "ROOT" \
     --idempotency-token 01234567 \
     --tags  Key=Name,Value=MyPCA
```

If successful, this command outputs the Amazon Resource Name \(ARN\) of the new CA\.

```
{
	"CertificateAuthorityArn":"arn:aws:acm-pca:region:account:
       certificate-authority/CA_ID"

}
```

**Command**

```
$ aws acm-pca create-certificate-authority \
	--certificate-authority-configuration file://ca_config.txt \
	--revocation-configuration file://revoke_config.txt \
	--certificate-authority-type "ROOT" \
	--idempotency-token 01234567 \
	--tags Key=Name,Value=MyPCA-2
```

If successful, this command outputs the Amazon Resource Name \(ARN\) of the CA\.

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
}
```

Use the following command to inspect the configuration of your CA\.

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/CA_ID" \
      --output json
```

This description should contain the following section\.

```
"RevocationConfiguration": {
   ...
   "OcspConfiguration": {
      "Enabled": true
   }
   ...
}
```

## Example 2: Create a CA with OCSP and a custom CNAME enabled<a name="Create-CA-OSCP-custom-CNAME"></a>

In this example, the revocation file enables customized OCSP support\. The `OcspCustomCname` parameter takes a fully qualified domain name \(FQDN\) as its value\. 

When you supply an FQDN in this field, ACM Private CA inserts the FQDN into the *Authority Information Access* extension of each issued certificate in place of the default URL for the AWS OCSP responder\. When an endpoint receives a certificate containing the custom FQDN, it queries that address for an OCSP response\. For this mechanism to work, you need to take two additional actions:
+ Use a proxy server to forward traffic that arrives at your custom FQDN to the AWS OCSP responder\.
+ Add a corresponding CNAME record to your DNS database\. 

**Tip**  
For more information about implementing a complete OCSP solution using a custom CNAME, see [Configuring a Custom URL for ACM Private CA OCSP](ocsp-customize.md)\.

For example, here is a CNAME record for customized OCSP as it would appear in Amazon Route 53: 


****  

| Record name | Type | Routing policy | Differentiator | Value/Route traffic to | 
| --- | --- | --- | --- | --- | 
|  alternative\.example\.com  | CNAME | Simple | \- | proxy\.example\.com | 

**Note**  
The value of the CNAME must not include a protocol prefix such as "http://" or "https://"\.

**File: revoke\_config\.txt for OCSP**

```
{
   "OcspConfiguration":{
      "Enabled":true,
      "OcspCustomCname":"alternative.example.com"
   }
}
```

**Command**

```
$ aws acm-pca create-certificate-authority \
	--certificate-authority-configuration file://ca_config.txt \
	--revocation-configuration file://revoke_config.txt \
	--certificate-authority-type "ROOT" \
	--idempotency-token 01234567 \
	--tags Key=Name,Value=MyPCA-3
```

If successful, this command outputs the Amazon Resource Name \(ARN\) of the CA\.

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
}
```

Use the following command to inspect the configuration of your CA\.

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/CA_ID" \
      --output json
```

This description should contain the following section\.

```
"RevocationConfiguration": {
   ...
   "OcspConfiguration": {
      "Enabled": true,
      "OcspCustomCname": "alternative.example.com"
   }
   ...
}
```

## Example 3: Create a CA with an attached CRL<a name="example_1"></a>

In this example, the revocation configuration defines CRL parameters\.

**File: revoke\_config\.txt**

```
{
   "CrlConfiguration":{
      "Enabled":true,
      "ExpirationInDays":7,
      "S3BucketName":"DOC-EXAMPLE-BUCKET"
   }
}
```

**Command**

```
$ aws acm-pca create-certificate-authority \
      --certificate-authority-configuration file://ca_config.txt \
      --revocation-configuration file://revoke_config.txt \
      --certificate-authority-type "ROOT" \
      --idempotency-token 01234567 \
      --tags Key=Name,Value=MyPCA-1
```

If successful, this command outputs the Amazon Resource Name \(ARN\) of the CA\.

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
}
```

Use the following command to inspect the configuration of your CA\.

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/CA_ID" \
      --output json
```

This description should contain the following section\.

```
"RevocationConfiguration": {
   ...
   "CrlConfiguration": {
      "Enabled": true,
      "ExpirationInDays": 7,
      "S3BucketName": "DOC-EXAMPLE-BUCKET"
   },
   ...
}
```

## Example 4: Create a CA with an attached CRL and a custom CNAME enabled<a name="example_2"></a>

In this example, the revocation configuration defines CRL parameters that include a custom CNAME\.

**File: revoke\_config\.txt**

```
{
   "CrlConfiguration":{
      "Enabled":true,
      "ExpirationInDays":7,
      "CustomCname": "alternative.example.com",
      "S3BucketName":"DOC-EXAMPLE-BUCKET"
   }
}
```

**Command**

```
$ aws acm-pca create-certificate-authority \
      --certificate-authority-configuration file://ca_config.txt \
      --revocation-configuration file://revoke_config.txt \
      --certificate-authority-type "ROOT" \
      --idempotency-token 01234567 \
      --tags Key=Name,Value=MyPCA-1
```

If successful, this command outputs the Amazon Resource Name \(ARN\) of the CA\.

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
}
```

Use the following command to inspect the configuration of your CA\.

```
$ aws acm-pca describe-certificate-authority \
      --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/CA_ID" \
      --output json
```

This description should contain the following section\.

```
"RevocationConfiguration": {
   ...
   "CrlConfiguration": {
      "Enabled": true,
      "ExpirationInDays": 7,
      "CustomCname": "alternative.example.com",
      "S3BucketName": "DOC-EXAMPLE-BUCKET",
   ...
   }
}
```

## Example 5: Create a CA with a custom subject name<a name="example_4"></a>

In this example, we create a CA with a customized subject name\. The defined name and associated object identifier \(OID\) is declared in the following configuration file\.

**File: ca\_config\.txt**

```
{
  "KeyAlgorithm": "RSA_2048",
  "SigningAlgorithm": "SHA256WITHRSA",
  "Subject": {
    "CustomAttributes": [
      {
        "ObjectIdentifier": "1.3.6.1.4.1.37244.1.3",
        "Value": "ABCDABCD12341234"
      }
    ]
  }
}
```

**Command**

```
$ aws acm-pca create-certificate-authority \
      --certificate-authority-configuration fileb://ca_config.txt \
      --revocation-configuration file://revoke_config.txt \
      --certificate-authority-type ROOT
```

If successful, this command outputs the Amazon Resource Name \(ARN\) of the CA\.

```
{
    "CertificateAuthorityArn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
}
```

View the CA, which contains a `CustomAttributes` section\.

```
$ aws acm-pca describe-certificate-authority --certificate-authority-arn "arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
```

```
{
    "CertificateAuthority": {
        "Status": "PENDING_CERTIFICATE", 
        "LastStateChangeAt": 1234567891.234, 
        "KeyStorageSecurityStandard": "FIPS_140_2_LEVEL_3_OR_HIGHER", 
        "OwnerAccount": "account", 
        "Arn": "arn:aws:acm-pca:region:account:certificate-authority/CA_ID", 
        "CertificateAuthorityConfiguration": {
            "KeyAlgorithm": "RSA_2048", 
            "SigningAlgorithm": "SHA256WITHRSA", 
            "Subject": {
                "CustomAttributes": [
                    {
                        "ObjectIdentifier": "1.3.6.1.4.1.37244.1.3", 
                        "Value": "ABCDABCD12341234"
                    }
                ]
            }
        }, 
        "Type": "ROOT", 
        "RevocationConfiguration": {
            "CrlConfiguration": {
                "Enabled": false
            }, 
            "OcspConfiguration": {
                "Enabled": false
            }
        }, 
        "CreatedAt": 1234567891.234
    }
}
```