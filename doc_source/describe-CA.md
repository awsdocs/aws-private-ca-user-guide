# Viewing a private CA<a name="describe-CA"></a>

You can use the ACM console or the AWS CLI to view detailed metadata about a private CA and change several of the values as needed\. For detailed information about updating CAs, see [Updating your private CA](PCAUpdateCA.md)\.

**To view CA details in the console**

1. Sign in to your AWS account and open the AWS Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\.

1. Review the **Private certificate authorities** list\. You can navigate through multiple pages of CAs using the page numbers at upper\-right\. 

1. To show detailed metadata for a listed CA, choose the radio button by the CA that you want to inspect\. This opens a details pane with the following tabbed views:
   + **Subject** tab– Information about the distinguished name for the CA\. For more information, see [Subject distinguished name options](Create-CA-console.md#PcaCreateCaName)\. The fields displayed include:
     + **Subject** – Summary of provided name information fields
     + **Organization \(O\)** – For example, a company name
     + **Organization Unit \(OU\)** – For example, a division within a company
     + **Country name \(C\)** – A two\-letter country code
     + **State or province name** – Full name of a state or province
     + **Locality name** – The name of a city
     + **Common Name \(CN\)** – A fully qualified domain name \(FQDN\)
   + **CA certificate** tab – Information about the validity of the CA certificate
     + **Valid until** – The date and time until the CA certificate is valid
     + **Expires in** – The number of days until expiration
   + **Revocation configuration** tab – Your current selections for certificate revocation options\. Choose **Edit** to update\.
     + **Certificate Revocation List \(CRL\) distribution** – Status of **Enabled** or **Disabled**
     + **Online Certificate Status Protocol \(OCSP\)** – Status of **Enabled** or **Disabled**
   + **Permissions** tab – Your current selection of certificate renewal permisisons for this CA through AWS Certificate Manager \(ACM\)\. Choose **Edit** to update\.
   + **ACM authorization for renewals** – Status of authorized or unauthorized
   + **Tags** tab – Your current assignment of customizable labels for this CA\. Choose the **Manage tags** to update\. 
   + **Resource shares** tab – Your current assignment of resource shares for this CA through AWS Resource Access Manager \(RAM\)\. Choose **Manage resource shares** to update\.
     + **Name** – Name of the resource share
     + **Status** – status of the resource share 

1. Choose the **ID** field of the CA that you want to inspect to open the **General** pane\. The CA's 32\-byte hexadecimal unique identifier appears at the top\. The pane provides the following additional information:
   + **Status** – CA status\. Possible values are **Creating**, **Pending certificate**, **Active**, **Deleted**, **Disabled**, **Expired**, and **Failed**\.
   + **ARN** – The [Amazon Resource Name](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) for the CA\.
   + **Owner** – The AWS account that owns the CA\. This may be your account \(**Self**\) or an account that has delegated CA management permissions to you\.
   + **CA type** – The type of CA\. Possible values are **Root** and **Subordinate**\.
   + **Created at** – The date and time when the CA was created\.
   + **Expiration date** – The date and time when the CA certificate expires\.
   + **Mode** – The expiration mode of certificate issued by the CA\. Possible values are **General\-purpose **\(certificates that can be configured with any expiration date\) and **Short\-lived certificate** \(certificates with a maximum validity period of seven days\)\. A short validity period can substitute in some cases for a revocation mechanism\. The default is **General\-purpose**\.
   + **Key algorithm** – The public key algorithm supported by the CA\. Possible values are **RSA 2048**, **RSA 4096**, **ECDSA P2567**, and **ECDSA P384**\.
   + **Signing algorithm** – The algorithm that the CA uses to sign certificate requests\. \(Not to be confused with the `SigningAlgorithm` parameter used to sign certificates when they are issued\.\) Possible values are **SHA256 ECDSA**, **SHA384 ECDSA**, **SHA512 ECDSA**, **SHA256 RSA**, **SHA384 RSA**, and **SHA512 RSA**
   + **Key storage security standard** – Level of Federal Information Processing Standards conformance\. Possible values are **FIPS 140\-2 level 3 or higher** and **FIPS 140\-2 level 3 or higher**\. This parameter varies by AWS Region\.

**To view and modify CA details using the AWS CLI**

Use the [https://docs.aws.amazon.com/cli/latest/reference/acm-pca/describe-certificate-authority.html](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/describe-certificate-authority.html) command in the AWS CLI to display details about a CA, as shown in the following command:

```
$ aws acm-pca describe-certificate-authority --certificate-authority-arn arn:aws:acm:region:account:certificate-authority/CA_ID
```

The command returns information similar to the following:

```
{
   "CertificateAuthority":{
      "Arn":"arn:aws:acm:region:account:certificate-authority/CA_ID",
      "CreatedAt":"2022-05-02T11:59:02.022000-07:00",
      "LastStateChangeAt":"2022-05-02T11:59:18.498000-07:00",
      "Type":"ROOT",
      "Serial":"serial_number",
      "Status":"ACTIVE",
      "NotBefore":"2022-05-02T10:59:17-07:00",
      "NotAfter":"2031-05-02T11:59:17-07:00",
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
}
```

For information about updating a private CA from the command line, see [Updating a CA \(CLI\)](ca-update-cli.md)\.