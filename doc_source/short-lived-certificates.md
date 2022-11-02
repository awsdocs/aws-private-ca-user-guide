# Certificate authority modes<a name="short-lived-certificates"></a>

AWS Private CA supports the creation of a CA in either of two modes\. The modes, GENERAL\_PURPOSE and SHORT\_LIVED\_CERTIFICATE, affect the allowed validity period of the certificates issued by the CA\.

**Note**  
AWS Private CA does not perform validity checks on root CA certificates\.

## GENERAL\_PURPOSE \(default\)<a name="standard"></a>

This mode permits the CA to issue certificates with any validity period\. Most applications use certificates of this type\. Typically, the CA also specifies a revocation mechanism\.

## SHORT\_LIVED\_CERTIFICATE<a name="short"></a>

This mode defines a CA that exclusively issues certificates with a maximum validity period of seven days\. These short\-lived certificates expire so quickly that they can be deployed without a revocation mechanism in place\. For some applications, it makes more sense to frequently deploy short\-lived certificates than to incur the network and processing overhead of revocation\. 

CAs with SHORT\_LIVED\_CERTIFICATE mode cost less than general\-purpose CAs\. For more informtion, see [AWS Private Certificate Authority Pricing](https://aws.amazon.com/private-ca/pricing/)\.

To create a CA that issues short\-lived certificates, set the `UsageMode` parameter to SHORT\_LIVED\_CERTIFICATE using the [AWS CLI](Create-CA-CLI.md) procedure for creating a CA\. 

**Note**  
AWS Certificate Manager cannot issue short\-lived certificates\.