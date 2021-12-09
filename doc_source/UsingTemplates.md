# Understanding certificate templates<a name="UsingTemplates"></a>

ACM Private CA uses configuration templates to issue both CA certificates and end\-entity certificates\. When you issue a CA certificate from the PCA console, the appropriate root or subordinate CA certificate template is applied automatically\. 

If you use the CLI or API to issue a certificate, you can supply a template ARN as a parameter to the `IssueCertificate` action\. If you provide no ARN, then the `EndEntityCertificate/V1` template is applied by default\. For more information, see the [IssueCertificate](https://docs.aws.amazon.com/acm-pca/latest/APIReference/API_IssueCertificate.html) API and [issue\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/issue-certificate.html) command documentation\.

**Note**  
AWS Certificate Manager \(ACM\) users with cross\-account shared access to a private CA can issue managed certificates that are signed by the CA\. Cross\-account issuers are constrained by a resource\-based policy and have access only to the following end\-entity certificate templates:  
[EndEntityCertificate/V1](#EndEntityCertificate-V1)
[EndEntityClientAuthCertificate/V1](#EndEntityClientAuthCertificate-V1)
[EndEntityServerAuthCertificate/V1](#EndEntityServerAuthCertificate-V1)
[BlankEndEntityCertificate\_APICSRPassthrough](#BlankEndEntityCertificate_APICSRPassthrough)
For more information, see [Resource\-based policies](pca-rbp.md)\.

**Topics**
+ [Template varieties](#template-varieties)
+ [Template order of operations](#template-order-of-operations)
+ [Template definitions](#template-definitions)

## Template varieties<a name="template-varieties"></a>

ACM Private CA supports four varieties of template\.
+ **Base templates**

  Pre\-defined templates in which no passthrough parameters are allowed\.
+ **CSRPassthrough templates**

  Templates that extend their corresponding base template versions by allowing CSR passthrough\. Extensions in the CSR that is used to issue the certificate are copied over to the issued certificate\. In cases where the CSR contains extension values that conflict with the template definition, the template definition will always have the higher priority\. For more details about priority, see [Template order of operations](#template-order-of-operations)\.
+ **APIPassthrough templates**

  Templates that extend their corresponding base template versions by allowing API passthrough\. Dynamic values that are known to the administrator or other intermediate systems may not be known by the entity requesting the certificate, may be impossible to define in a template, and may not be available in the CSR\. The CA administrator, however, can retrieve additional information from another data source, such as an Active Directory, to complete the request\. For example, if a machine doesn't know what organization unit it belongs to, the administrator can look up the information in Active Directory and add it to the certificate request by including the information in a JSON structure\.

  Values in the `ApiPassthrough` parameter of the `IssueCertificate` action ``are copied over to the issued certificate\. In cases where the `ApiPassthrough` parameter contains information that conflicts with the template definition, the template definition will always have the higher priority\. For more details about priority, see [Template order of operations](#template-order-of-operations)\. 
+ **APICSRPassthrough templates**

  Templates that extend their corresponding base template versions by allowing both API and CSR passthrough\. Extensions in the CSR used to issue the certificate are copied over to the issued certificate, and values in the `ApiPassthrough` parameter of the `IssueCertificate` action are also copied over \. In cases where the template definition, API passthrough values, and CSR passthrough extensions exhibit a conflict, the template definition has highest priority, followed by the API passthrough values, followed by the CSR passthrough extensions\. For more details about priority, see [Template order of operations](#template-order-of-operations)\.

The tables below list all of the template types supported by ACM Private CA with links to their definitions\.

**Note**  
For information about template ARNs in GovCloud regions, see [AWS Certificate Manager Private Certificate Authority](https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/using-govcloud-arns.html#using-govcloud-arn-syntax-acmpca) in the *AWS GovCloud \(US\) User Guide*\.


**Base templates**  

|  Template Name  |  Template ARN  |  Certificate Type  | 
| --- | --- | --- | 
|  [CodeSigningCertificate/V1](#CodeSigningCertificate-V1)  |  `arn:aws:acm-pca:::template/CodeSigningCertificate/V1`  |  Code signing  | 
|  [EndEntityCertificate/V1](#EndEntityCertificate-V1)  |  `arn:aws:acm-pca:::template/EndEntityCertificate/V1`  |  End\-entity  | 
|  [EndEntityClientAuthCertificate/V1](#EndEntityClientAuthCertificate-V1)  |  `arn:aws:acm-pca:::template/EndEntityClientAuthCertificate/V1`  |  End\-entity  | 
|  [EndEntityServerAuthCertificate/V1](#EndEntityServerAuthCertificate-V1)  |  `arn:aws:acm-pca:::template/EndEntityServerAuthCertificate/V1`  |  End\-entity  | 
|  [OCSPSigningCertificate/V1](#OCSPSigningCertificate-V1)  |  `arn:aws:acm-pca:::template/OCSPSigningCertificate/V1`  |  OCSP signing  | 
|  [RootCACertificate/V1](#RootCACertificate-V1)  |  `arn:aws:acm-pca:::template/RootCACertificate/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen0/V1](#SubordinateCACertificate_PathLen0-V1)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen0/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen1/V1](#SubordinateCACertificate_PathLen1-V1)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen1/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen2/V1](#SubordinateCACertificate_PathLen2-V1)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen2/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen3/V1](#SubordinateCACertificate_PathLen3-V1)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen3/V1`  |  CA  | 


**CSRPassthrough templates**  

|  Template Name  |  Template ARN  |  Certificate Type  | 
| --- | --- | --- | 
|  [BlankEndEntityCertificate\_CSRPassthrough](#BlankEndEntityCertificate_CSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankEndEntityCertificate_CSRPassthrough/V1`  | End\-entity | 
|  [CodeSigningCertificate\_CSRPassthrough/V1](#CodeSigningCertificate_CSRPassthrough-V1)  |  `arn:aws:acm-pca:::template/CodeSigningCertificate_CSRPassthrough/V1`  |  Code signing  | 
|  [EndEntityCertificate\_CSRPassthrough/V1](#EndEntityCertificate_CSRPassthrough-V1)  |  `arn:aws:acm-pca:::template/EndEntityCertificate_CSRPassthrough/V1`  |  End\-entity  | 
|  [EndEntityClientAuthCertificate\_CSRPassthrough/V1](#EndEntityClientAuthCertificate_CSRPassthrough-V1)  |  `arn:aws:acm-pca:::template/EndEntityClientAuthCertificate_CSRPassthrough/V1`  |  End\-entity  | 
|  [EndEntityServerAuthCertificate\_CSRPassthrough/V1](#EndEntityServerAuthCertificate_CSRPassthrough-V1)  |  `arn:aws:acm-pca:::template/EndEntityServerAuthCertificate_CSRPassthrough/V1`  | End\-entity | 
|  [OCSPSigningCertificate\_CSRPassthrough/V1](#OCSPSigningCertificate_CSRPassthrough-V1)  |  `arn:aws:acm-pca:::template/OCSPSigningCertificate_CSRPassthrough/V1`  |  OCSP signing  | 
|  [SubordinateCACertificate\_PathLen0\_CSRPassthrough/V1](#SubordinateCACertificate_PathLen0_CSRPassthrough-V1)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen0_CSRPassthrough/V1`  | CA | 
|  [BlankSubordinateCACertificate\_PathLen0\_CSRPassthrough](#BlankSubordinateCACertificate_PathLen0_CSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen0_CSRPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen1\_CSRPassthrough/V1](#SubordinateCACertificate_PathLen1_CSRPassthrough-V1)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen1_CSRPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen1\_CSRPassthrough](#BlankSubordinateCACertificate_PathLen1_CSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen1_CSRPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen1\_APICSRPassthrough](#BlankSubordinateCACertificate_PathLen1_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen1_APICSRPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen2\_CSRPassthrough/V1](#SubordinateCACertificate_PathLen2_CSRPassthrough-V1)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen2_CSRPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen2\_CSRPassthrough](#BlankSubordinateCACertificate_PathLen2_CSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen2_CSRPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen3\_CSRPassthrough/V1](#SubordinateCACertificate_PathLen3_CSRPassthrough-V1)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen3_CSRPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen3\_CSRPassthrough](#BlankSubordinateCACertificate_PathLen3_CSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen3_CSRPassthrough/V1`  |  CA  | 


**APIPassthrough templates**  

|  Template Name  |  Template ARN  |  Certificate Type  | 
| --- | --- | --- | 
|  [BlankEndEntityCertificate\_APIPassthrough](#BlankEndEntityCertificate_APIPassthrough)  |  `arn:aws:acm-pca:::template/BlankEndEntityCertificate_APIPassthrough/V1`  |  End\-entity  | 
|  [CodeSigningCertificate\_APIPassthrough](#CodeSigningCertificate_APIPassthrough)  |  `arn:aws:acm-pca:::template/CodeSigningCertificate_APIPassthrough/V1`  |  Code signing  | 
|  [EndEntityCertificate\_APIPassthrough](#EndEntityCertificate_APIPassthrough)  |  `arn:aws:acm-pca:::template/EndEntityCertificate\_APIPassthrough/V1`  |  End\-entity  | 
|  [EndEntityClientAuthCertificate\_APIPassthrough](#EndEntityClientAuthCertificate_APIPassthrough)  |  `arn:aws:acm-pca:::template/EndEntityClientAuthCertificate_APIPassthrough/V1`  |  End\-entity  | 
|  [EndEntityServerAuthCertificate\_APIPassthrough](#EndEntityServerAuthCertificate_APIPassthrough)  |  `arn:aws:acm-pca:::template/EndEntityServerAuthCertificate_APIPassthrough/V1`  |  End\-entity  | 
|  [OCSPSigningCertificate\_APIPassthrough](#OCSPSigningCertificate_APIPassthrough)  |  `arn:aws:acm-pca:::template/OCSPSigningCertificate_APIPassthrough/V1`  |  OCSP signing  | 
|  [RootCACertificate\_APIPassthrough](#RootCACertificate_APIPassthrough)  |  `arn:aws:acm-pca:::template/RootCACertificate_APIPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen0\_APIPassthrough](#SubordinateCACertificate_PathLen0_APIPassthrough)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen0_APIPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen0\_APIPassthrough](#BlankSubordinateCACertificate_PathLen0_APIPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen0_APIPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen1\_APIPassthrough](#SubordinateCACertificate_PathLen1_APIPassthrough)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen1_APIPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen1\_APIPassthrough](#BlankSubordinateCACertificate_PathLen1_APIPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen1_APIPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen2\_APIPassthrough](#SubordinateCACertificate_PathLen2_APIPassthrough)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen2_APIPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen2\_APIPassthrough](#BlankSubordinateCACertificate_PathLen2_APIPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen2_APIPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen3\_APIPassthrough](#SubordinateCACertificate_PathLen3_APIPassthrough)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen3_APIPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen3\_APIPassthrough](#BlankSubordinateCACertificate_PathLen3_APIPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen3_APIPassthrough/V1`  |  CA  | 


**APICSRPassthrough templates**  

|  Template Name  |  Template ARN  |  Certificate Type  | 
| --- | --- | --- | 
|  [BlankEndEntityCertificate\_APICSRPassthrough](#BlankEndEntityCertificate_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankEndEntityCertificate_APICSRPassthrough/V1`  |  End\-entity  | 
|  [CodeSigningCertificate\_APICSRPassthrough](#CodeSigningCertificate_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/CodeSigningCertificate_APICSRPassthrough/V1`  |  Code signing  | 
|  [EndEntityCertificate\_APICSRPassthrough](#EndEntityCertificate_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/EndEntityCertificate_APICSRPassthrough/V1`  |  End\-entity  | 
|  [EndEntityClientAuthCertificate\_APICSRPassthrough](#EndEntityClientAuthCertificate_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/EndEntityClientAuthCertificate_APICSRPassthrough/V1`  |  End\-entity  | 
|  [EndEntityServerAuthCertificate\_APICSRPassthrough](#EndEntityServerAuthCertificate_APICSRPassthrough)  | arn:aws:acm\-pca:::template/EndEntityServerAuthCertificate\_APICSRPassthrough/V1 |  End\-entity  | 
|  [OCSPSigningCertificate\_APICSRPassthrough](#OCSPSigningCertificate_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/OCSPSigningCertificate_APICSRPassthrough/V1`  |  OCSP signing  | 
|  [SubordinateCACertificate\_PathLen0\_APICSRPassthrough](#SubordinateCACertificate_PathLen0_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen0_APICSRPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen0\_APICSRPassthrough](#BlankSubordinateCACertificate_PathLen0_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen0_APICSRPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen1\_APICSRPassthrough](#SubordinateCACertificate_PathLen1_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen1_APICSRPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen1\_APICSRPassthrough](#BlankSubordinateCACertificate_PathLen1_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen1_APICSRPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen2\_APICSRPassthrough](#SubordinateCACertificate_PathLen2_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate\_PathLen2\_APICSRPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen2\_APICSRPassthrough](#BlankSubordinateCACertificate_PathLen2_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen2_APICSRPassthrough/V1`  |  CA  | 
|  [SubordinateCACertificate\_PathLen3\_APICSRPassthrough](#SubordinateCACertificate_PathLen3_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/SubordinateCACertificate_PathLen3_APICSRPassthrough/V1`  |  CA  | 
|  [BlankSubordinateCACertificate\_PathLen3\_APICSRPassthrough](#BlankSubordinateCACertificate_PathLen3_APICSRPassthrough)  |  `arn:aws:acm-pca:::template/BlankSubordinateCACertificate_PathLen3_APICSRPassthrough/V1`  |  CA  | 

## Template order of operations<a name="template-order-of-operations"></a>

Information contained in an issued certificate can come from four sources: the template definition, API passthrough, CSR passthrough, and the CA configuration\.

API passthrough values are only respected when you use an API passthrough or APICSR passthrough template\. CSR passthrough is only respected when you use a CSRPassthrough or APICSR passthrough template\. When these sources of information are in conflict, a general rule usually applies: For each extension value, the template definition has highest priority, followed by API passthrough values, followed by CSR passthrough extensions\.

**Examples**

1. The template definition for [EndEntityClientAuthCertificate\_APIPassthrough](#EndEntityClientAuthCertificate_APIPassthrough) defines the ExtendedKeyUsage extension with a value of "TLS web server authentication, TLS web client authentication"\. If ExtendedKeyUsage is defined in the CSR or in the `IssueCertificate` `ApiPassthrough` parameter, the `ApiPassthrough` value for ExtendedKeyUsage will be ignored because the template definition takes priority, and the CSR value for ExtendedKeyUsage value will be ignored because the template is not a CSR passthrough variety\.
**Note**  
The template definition nonetheless copies over other values from the CSR, such as Subject and Subject Alternative Name\. These values are still taken from the CSR even though the template is not a CSR passthrough variety, because the template definition always takes highest priority\.

1. The template definition for [EndEntityClientAuthCertificate\_APICSRPassthrough](#EndEntityClientAuthCertificate_APICSRPassthrough) defines the Subject Alternative Name \(SAN\) extension as being copied from the API or CSR\. If the SAN extension is defined in the CSR and provided in the `IssueCertificate`` ApiPassthrough` parameter, the API passthrough value will take priority because API passthrough values take priority over CSR passthrough values\.

## Template definitions<a name="template-definitions"></a>

The following sections provide configuration details about supported ACM Private CA certificate templates\. 

### BlankEndEntityCertificate\_APIPassthrough/V1 definition<a name="BlankEndEntityCertificate_APIPassthrough"></a>

With blank end\-entity certificate templates, you can issue end\-entity certificates with only X\.509 Basic constraints present\. This is the simplest end\-entity certificate that ACM Private CA can issue, but it can be customized using the API structure\. The Basic constraints extension defines whether or not the certificate is a CA certificate\. A blank end\-entity certificate template enforces a value of FALSE for Basic constraints to ensure that an end\-entity certificate is issued and not a CA certificate\.

Blank passthrough templates are useful for issuing smart card certificates that require specific values for Key usage \(KU\) and Extended key usage \(EKU\)\. For example, Extended key usage may require Client Authentication and Smart Card Logon, and Key usage may require Digital Signature, Non Repudiation, and Key Encipherment\. Unlike other passthrough templates, blank end\-entity certificate templates allow the configuration of KU and EKU extensions, where KU can be any of the nine supported values \(digitalSignature, nonRepudiation, keyEnciphermet, dataEncipherment, keyAgreement, keyCertSign, cRLSign, encipherOnly, and decipherOnly\) and EKU can be any of the supported values \(serverAuth, clientAuth, codesigning, emailProtection, timestamping, and OCSPSigning\) plus custom extensions\.


**BlankEndEntityCertificate\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  CA:FALSE  | 
|  Authority key identifier  | \[SKI from CA certificate\] | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankEndEntityCertificate\_CSRPassthrough/V1 definition<a name="BlankEndEntityCertificate_CSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankEndEntityCertificate\_CSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  CA:FALSE  | 
|  Authority key identifier  | \[SKI from CA certificate\] | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankEndEntityCertificate\_APICSRPassthrough/V1 definition<a name="BlankEndEntityCertificate_APICSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankEndEntityCertificate\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  CA:FALSE  | 
|  Authority key identifier  | \[SKI from CA certificate\] | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen0\_CSRPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen0_CSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen0\_CSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 0`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen0\_APICSRPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen0_APICSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen0\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 0`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen0\_APIPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen0_APIPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen0\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 0`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

### BlankSubordinateCACertificate\_PathLen1\_APIPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen1_APIPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen1\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 1`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen1\_CSRPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen1_CSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen1\_CSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 1`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen1\_APICSRPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen1_APICSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen1\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 1`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen2\_APIPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen2_APIPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen2\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 2`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen2\_CSRPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen2_CSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen2\_CSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 2`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen2\_APICSRPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen2_APICSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen2\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 2`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen3\_APIPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen3_APIPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen3\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 3`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen3\_CSRPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen3_CSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen3\_CSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 3`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### BlankSubordinateCACertificate\_PathLen3\_APICSRPassthrough/V1 definition<a name="BlankSubordinateCACertificate_PathLen3_APICSRPassthrough"></a>

For general information about blank templates, see [BlankEndEntityCertificate\_APIPassthrough/V1 definition](#BlankEndEntityCertificate_APIPassthrough)\.


**BlankSubordinateCACertificate\_PathLen3\_APICSRPassthrough**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 3`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### CodeSigningCertificate/V1 definition<a name="CodeSigningCertificate-V1"></a>

This template is used to create certificates for code signing\. You can use code\-signing certificates from ACM Private CA with any code\-signing solution that is based on a private CA infrastructure\. For example, customers using Code Signing for AWS IoT can generate a code\-signing certificate with ACM Private CA and import it to AWS Certificate Manager\. For more information, see [What Is Code Signing for AWS IoT?](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html) and [Obtain and Import a Code Signing Certificate](https://docs.aws.amazon.com/signer/latest/developerguide/gs-cs-cert.html)\.


**CodeSigningCertificate/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  `CA:FALSE`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature  | 
|  Extended key usage  |  Critical, code signing  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### CodeSigningCertificate\_APICSRPassthrough/V1 definition<a name="CodeSigningCertificate_APICSRPassthrough"></a>

This template extends CodeSigningCertificate/V1 to support API and CSR passthrough values\.


**CodeSigningCertificate\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  `CA:FALSE`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature  | 
|  Extended key usage  |  Critical, code signing  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### CodeSigningCertificate\_APIPassthrough/V1 definition<a name="CodeSigningCertificate_APIPassthrough"></a>

This template is identical to the `CodeSigningCertificate` template with one difference: In this template, ACM Private CA passes additional extensions through the API to the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the API\.


**CodeSigningCertificate\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  `CA:FALSE`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature  | 
|  Extended key usage  |  Critical, code signing  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### CodeSigningCertificate\_CSRPassthrough/V1 definition<a name="CodeSigningCertificate_CSRPassthrough-V1"></a>

This template is identical to the `CodeSigningCertificate` template with one difference: In this template, ACM Private CA passes additional extensions from the certificate signing request \(CSR\) into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the CSR\.


**CodeSigningCertificate\_CSRPassthrough/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  `CA:FALSE`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature  | 
|  Extended key usage  |  Critical, code signing  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityCertificate/V1 definition<a name="EndEntityCertificate-V1"></a>

This template is used to create certificates for end entities such as operating systems or web servers\. 


**EndEntityCertificate/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web server authentication, TLS web client authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityCertificate\_APICSRPassthrough/V1 definition<a name="EndEntityCertificate_APICSRPassthrough"></a>

This template extends EndEntityCertificate/V1 to support API and CSR passthrough values\.


**EndEntityCertificate\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web server authentication, TLS web client authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityCertificate\_APIPassthrough/V1 definition<a name="EndEntityCertificate_APIPassthrough"></a>

This template is identical to the `EndEntityCertificate` template with one difference: In this template, ACM Private CA passes additional extensions through the API to the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the API\.


**EndEntityCertificate\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web server authentication, TLS web client authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityCertificate\_CSRPassthrough/V1 definition<a name="EndEntityCertificate_CSRPassthrough-V1"></a>

This template is identical to the `EndEntityCertificate` template with one difference: In this template, ACM Private CA passes additional extensions from the certificate signing request \(CSR\) into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the CSR\.


**EndEntityCertificate\_CSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web server authentication, TLS web client authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityClientAuthCertificate/V1 definition<a name="EndEntityClientAuthCertificate-V1"></a>

This template differs from the `EndEntityCertificate` only in the Extended key usage value, which restricts it to TLS web client authentication\.


**EndEntityClientAuthCertificate/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web client authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityClientAuthCertificate\_APICSRPassthrough/V1 definition<a name="EndEntityClientAuthCertificate_APICSRPassthrough"></a>

This template extends EndEntityClientAuthCertificate/V1 to support API and CSR passthrough values\.


**EndEntityClientAuthCertificate\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web client authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityClientAuthCertificate\_APIPassthrough/V1 definition<a name="EndEntityClientAuthCertificate_APIPassthrough"></a>

This template is identical to the `EndEntityClientAuthCertificate` template with one difference\. In this template, ACM Private CA passes additional extensions through the API into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the API\.


**EndEntityClientAuthCertificate\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web client authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityClientAuthCertificate\_CSRPassthrough/V1 definition<a name="EndEntityClientAuthCertificate_CSRPassthrough-V1"></a>

This template is identical to the `EndEntityClientAuthCertificate` template with one difference\. In this template, ACM Private CA passes additional extensions from the certificate signing request \(CSR\) into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the CSR\.


**EndEntityClientAuthCertificate\_CSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
| Key usage |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web client authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityServerAuthCertificate/V1 definition<a name="EndEntityServerAuthCertificate-V1"></a>

This template differs from the `EndEntityCertificate` only in the Extended key usage value, which restricts it to TLS web server authentication\.


**EndEntityServerAuthCertificate/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web server authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityServerAuthCertificate\_APICSRPassthrough/V1 definition<a name="EndEntityServerAuthCertificate_APICSRPassthrough"></a>

This template extends EndEntityServerAuthCertificate/V1 to support API and CSR passthrough values\.


**EndEntityServerAuthCertificate\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web server authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityServerAuthCertificate\_APIPassthrough/V1 definition<a name="EndEntityServerAuthCertificate_APIPassthrough"></a>

This template is identical to the `EndEntityServerAuthCertificate` template with one difference\. In this template, ACM Private CA passes additional extensions through the API into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the API\.


**EndEntityServerAuthCertificate\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web server authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### EndEntityServerAuthCertificate\_CSRPassthrough/V1 definition<a name="EndEntityServerAuthCertificate_CSRPassthrough-V1"></a>

This template is identical to the `EndEntityServerAuthCertificate` template with one difference\. In this template, ACM Private CA passes additional extensions from the certificate signing request \(CSR\) into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the CSR\.


**EndEntityServerAuthCertificate\_CSRPassthrough/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  CA:`FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, key encipherment  | 
|  Extended key usage  |  TLS web server authentication  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### OCSPSigningCertificate/V1 definition<a name="OCSPSigningCertificate-V1"></a>

This template is used to create certificates for signing OCSP responses\. The template is identical to the `CodeSigningCertificate` template, except that the Extended key usage value specifies OCSP signing instead of code signing\.


**OCSPSigningCertificate/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  `CA:FALSE`  | 
| Authority key identifier |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  | Critical, digital signature | 
|  Extended key usage  |  Critical, OCSP signing  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### OCSPSigningCertificate\_APICSRPassthrough/V1 definition<a name="OCSPSigningCertificate_APICSRPassthrough"></a>

This template extends the OCSPSigningCertificate/V1 to support API and CSR passthrough values\.


**OCSPSigningCertificate\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  `CA:FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  | Critical, digital signature | 
|  Extended key usage  |  Critical, OCSP signing  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### OCSPSigningCertificate\_APIPassthrough/V1 definition<a name="OCSPSigningCertificate_APIPassthrough"></a>

This template is identical to the `OCSPSigningCertificate` template with one difference\. In this template, ACM Private CA passes additional extensions through the API into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the API\.


**OCSPSigningCertificate\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  `CA:FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  | Critical, digital signature | 
|  Extended key usage  |  Critical, OCSP signing  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### OCSPSigningCertificate\_CSRPassthrough/V1 definition<a name="OCSPSigningCertificate_CSRPassthrough-V1"></a>

This template is identical to the `OCSPSigningCertificate` template with one difference\. In this template, ACM Private CA passes additional extensions from the certificate signing request \(CSR\) into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the CSR\.


**OCSPSigningCertificate\_CSRPassthrough/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  `CA:FALSE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  | Critical, digital signature | 
|  Extended key usage  |  Critical, OCSP signing  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### RootCACertificate/V1 definition<a name="RootCACertificate-V1"></a>

This template is used to issue self\-signed root CA certificates\. CA certificates include a critical Basic constraints extension with the CA field set to `TRUE` to designate that the certificate can be used to issue CA certificates\. This template does not specify a path length because the path length constrains the maximum length of the CA chain \(CA certification depth\)\. A constrained chain length could inhibit future expansion of the hierarchy\. Extended key usage is excluded to prevent use of the CA certificate as a TLS client or server certificate\. No CRL information is specified because a self\-signed certificate cannot be revoked\.


**RootCACertificate/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, keyCertSign, CRL sign  | 
|  CRL distribution points  |  N/A  | 

### RootCACertificate\_APIPassthrough/V1 definition<a name="RootCACertificate_APIPassthrough"></a>

This template extends RootCACertificate/V1 to support API passthrough values\.


**RootCACertificate\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`  | 
|  Authority key identifier  |  \[SKI from CA certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, keyCertSign, CRL sign  | 
|  CRL distribution points\*  |  N/A  | 

### SubordinateCACertificate\_PathLen0/V1 definition<a name="SubordinateCACertificate_PathLen0-V1"></a>

This template is used to issue subordinate CA certificates with a path length of 0\. CA certificates include a critical Basic constraints extension with the CA field set to `TRUE` to designate that the certificate can be used to issue CA certificates\. Extended key usage is not included, which prevents the CA certificate from being used as a TLS client or server certificate\.

For more information about certification paths, see [Setting Length Constraints on the Certification Path](https://docs.aws.amazon.com/acm-pca/latest/userguide/ca-hierarchy.html#length-constraints)\.


**SubordinateCACertificate\_PathLen0/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 0`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\*CRL distribution points are included in certificates issued with this template only if the CA is configured with CRL generation enabled\.

### SubordinateCACertificate\_PathLen0\_APICSRPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen0_APICSRPassthrough"></a>

This template extends SubordinateCACertificate\_PathLen0/V1 to support API and CSR passthrough values\.


**SubordinateCACertificate\_PathLen0\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 0`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### SubordinateCACertificate\_PathLen0\_APIPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen0_APIPassthrough"></a>

This template extends SubordinateCACertificate\_PathLen0/V1 to support API passthrough values\.


**SubordinateCACertificate\_PathLen0\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 0`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### SubordinateCACertificate\_PathLen0\_CSRPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen0_CSRPassthrough-V1"></a>

This template is identical to the `SubordinateCACertificate_PathLen0` template with one difference: In this template, ACM Private CA passes additional extensions from the certificate signing request \(CSR\) into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the CSR\.

**Note**  
A CSR that contains custom additional extensions must be created outside of ACM Private CA\.


**SubordinateCACertificate\_PathLen0\_CSRPassthrough/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 0`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in certificates issued with this template only if the CA is configured with CRL generation enabled\.

### SubordinateCACertificate\_PathLen1/V1 definition<a name="SubordinateCACertificate_PathLen1-V1"></a>

This template is used to issue subordinate CA certificates with a path length of 0\. CA certificates include a critical Basic constraints extension with the CA field set to `TRUE` to designate that the certificate can be used to issue CA certificates\. Extended key usage is not included, which prevents the CA certificate from being used as a TLS client or server certificate\.

For more information about certification paths, see [Setting Length Constraints on the Certification Path](https://docs.aws.amazon.com/acm-pca/latest/userguide/ca-hierarchy.html#length-constraints)\.


**SubordinateCACertificate\_PathLen1/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 1`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\*CRL distribution points are included in certificates issued with this template only if the CA is configured with CRL generation enabled\.

### SubordinateCACertificate\_PathLen1\_APICSRPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen1_APICSRPassthrough"></a>

This template extends SubordinateCACertificate\_PathLen1/V1 to support API and CSR passthrough values\.


**SubordinateCACertificate\_PathLen1\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 1`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### SubordinateCACertificate\_PathLen1\_APIPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen1_APIPassthrough"></a>

This template extends SubordinateCACertificate\_PathLen0/V1 to support API passthrough values\.


**SubordinateCACertificate\_PathLen1\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 1`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### SubordinateCACertificate\_PathLen1\_CSRPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen1_CSRPassthrough-V1"></a>

This template is identical to the `SubordinateCACertificate_PathLen1` template with one difference: In this template, ACM Private CA passes additional extensions from the certificate signing request \(CSR\) into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the CSR\.

**Note**  
A CSR that contains custom additional extensions must be created outside of ACM Private CA\.


**SubordinateCACertificate\_PathLen1\_CSRPassthrough/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 1`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in certificates issued with this template only if the CA is configured with CRL generation enabled\.

### SubordinateCACertificate\_PathLen2/V1 definition<a name="SubordinateCACertificate_PathLen2-V1"></a>

This template is used to issue subordinate CA certificates with a path length of 2\. CA certificates include a critical Basic constraints extension with the CA field set to `TRUE` to designate that the certificate can be used to issue CA certificates\. Extended key usage is not included, which prevents the CA certificate from being used as a TLS client or server certificate\.

For more information about certification paths, see [Setting Length Constraints on the Certification Path](https://docs.aws.amazon.com/acm-pca/latest/userguide/ca-hierarchy.html#length-constraints)\.


**SubordinateCACertificate\_PathLen2/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 2`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\*CRL distribution points are included in certificates issued with this template only if the CA is configured with CRL generation enabled\.

### SubordinateCACertificate\_PathLen2\_APICSRPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen2_APICSRPassthrough"></a>

This template extends SubordinateCACertificate\_PathLen2/V1 to support API and CSR passthrough values\.


**SubordinateCACertificate\_PathLen2\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 2`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### SubordinateCACertificate\_PathLen2\_APIPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen2_APIPassthrough"></a>

This template extends SubordinateCACertificate\_PathLen2/V1 to support API passthrough values\.


**SubordinateCACertificate\_PathLen2\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 2`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### SubordinateCACertificate\_PathLen2\_CSRPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen2_CSRPassthrough-V1"></a>

This template is identical to the `SubordinateCACertificate_PathLen2` template with one difference: In this template, ACM Private CA passes additional extensions from the certificate signing request \(CSR\) into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the CSR\.

**Note**  
A CSR that contains custom additional extensions must be created outside of ACM Private CA\.


**SubordinateCACertificate\_PathLen2\_CSRPassthrough/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 2`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in certificates issued with this template only if the CA is configured with CRL generation enabled\.

### SubordinateCACertificate\_PathLen3/V1 definition<a name="SubordinateCACertificate_PathLen3-V1"></a>

This template is used to issue subordinate CA certificates with a path length of 3\. CA certificates include a critical Basic constraints extension with the CA field set to `TRUE` to designate that the certificate can be used to issue CA certificates\. Extended key usage is not included, which prevents the CA certificate from being used as a TLS client or server certificate\.

For more information about certification paths, see [Setting Length Constraints on the Certification Path](https://docs.aws.amazon.com/acm-pca/latest/userguide/ca-hierarchy.html#length-constraints)\.


**SubordinateCACertificate\_PathLen3/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 3`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\*CRL distribution points are included in certificates issued with this template only if the CA is configured with CRL generation enabled\.

### SubordinateCACertificate\_PathLen3\_APICSRPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen3_APICSRPassthrough"></a>

This template extends SubordinateCACertificate\_PathLen3/V1 to support API and CSR passthrough values\.


**SubordinateCACertificate\_PathLen3\_APICSRPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 3`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### SubordinateCACertificate\_PathLen3\_APIPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen3_APIPassthrough"></a>

This template extends SubordinateCACertificate\_PathLen3/V1 to support API passthrough values\.


**SubordinateCACertificate\_PathLen3\_APIPassthrough/V1**  

|  X509v3 Parameter  | Value | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from API or CSR\]  | 
|  Subject  |  \[Passthrough from API or CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 3`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration\]  | 

\* CRL distribution points are included in the template only if the CA is configured with CRL generation enabled\. 

### SubordinateCACertificate\_PathLen3\_CSRPassthrough/V1 definition<a name="SubordinateCACertificate_PathLen3_CSRPassthrough-V1"></a>

This template is identical to the `SubordinateCACertificate_PathLen3` template with one difference: In this template, ACM Private CA passes additional extensions from the certificate signing request \(CSR\) into the certificate if the extensions are not specified in the template\. Extensions specified in the template always override extensions in the CSR\.

**Note**  
A CSR that contains custom additional extensions must be created outside of ACM Private CA\.


**SubordinateCACertificate\_PathLen3\_CSRPassthrough/V1**  

|  X509v3 Parameter  |  Value  | 
| --- | --- | 
|  Subject alternative name  |  \[Passthrough from CSR\]  | 
|  Subject  |  \[Passthrough from CSR\]  | 
|  Basic constraints  |  Critical, `CA:TRUE`, `pathlen: 3`  | 
|  Authority key identifier  |  \[SKI from CA Certificate\]  | 
|  Subject key identifier  |  \[Derived from CSR\]  | 
|  Key usage  |  Critical, digital signature, `keyCertSign`, CRL sign  | 
|  CRL distribution points\*  |  \[Passthrough from CA configuration or CSR\]  | 

\*CRL distribution points are included in certificates issued with this template only if the CA is configured with CRL generation enabled\.