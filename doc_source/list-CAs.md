# Listing private CAs<a name="list-CAs"></a>

You can use the AWS Private CA console or AWS CLI to list private CAs that you own or have access to\.

**To list available CAs using the console**

1. Sign in to your AWS account and open the AWS Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Review the information in the **Private certificate authorities** list\. You can navigate through multiple pages of CAs using the page numbers at upper\-right\. Each CA occupies a row with some or all of the following columns displayed for each one: 
+ **Subject** – Summary of distinguished name information for the CA\.
+ **Id** – 32\-byte hexadecimal unique identifier of the CA\.
+ **Status** – CA status\. Possible values are **Creating**, **Pending certificate**, **Active**, **Deleted**, **Disabled**, **Expired**, and **Failed**\.
+ **Type** – The type of CA\. Possible values are **Root** and **Subordinate**\.
+ **Mode** – The mode of the CA\. Possible values are **General\-purpose** \(issues certificates that can be configured with any expiration date\) and **Short\-lived certificate** \(issues certificates with a maximum validity period of seven days\)\. A short validity period can substitute in some cases for a revocation mechanism\. The default is **General\-purpose**\.
+ **Owner** – The AWS account that owns the CA\. This may be your account or an account that has delegated CA management permissions to you\.
+ **Key algorithm** – The public key algorithm supported by the CA\. Possible values are **RSA\_2048**, **RSA\_4096**, **EC\_prime256v1**, and **EC\_secp384r1**\.
+ **Signing algorithm** – The algorithm that the CA uses to sign certificate requests\. \(Not to be confused with the `SigningAlgorithm` parameter used to sign certificates when they are issued\.\) Possible values are **SHA256WITHECDSA**, **SHA384WITHECDSA**, **SHA512WITHECDSA**, **SHA256WITHRSA**, **SHA384WITHRSA**, and **SHA512WITHRSA**\.

**Note**  
You can customize the columns that you want to display, as well as other settings, by choosing the gear icon in the upper\-right corner of the console\. 

**To list available CAs using the AWS CLI**

Use the [list\-certificate\-authorities](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/list-certificate-authorities.html) command to list available CAs as shown in the following example:

```
$ aws acm-pca list-certificate-authorities --max-items 10
```

The command returns information similar to the following:

```
{
   "CertificateAuthorities":[
      {
         "Arn":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
         "CreatedAt":"2022-05-02T11:59:02.022000-07:00",
         "LastStateChangeAt":"2022-05-02T11:59:18.498000-07:00",
         "Type":"ROOT",
         "Serial":"serial_number",
         "Status":"ACTIVE",
         "NotBefore":"2022-05-02T10:59:17-07:00",
         "NotAfter":"2032-05-02T11:59:17-07:00",
         "CertificateAuthorityConfiguration":{
            "KeyAlgorithm":"RSA_2048",
            "SigningAlgorithm":"SHA256WITHRSA",
            "Subject":{
               "Organization":"testing_com"
            }
         },
         "RevocationConfiguration":{
            "CrlConfiguration":{
               "Enabled":false
            }
         }
      }
	...
   ]
}
```