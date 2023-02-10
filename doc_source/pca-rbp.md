# Resource\-based policies<a name="pca-rbp"></a>

Resource\-based policies are permissions policies that you create and manually attach to a resource \(in this case, a private CA\) rather than to a user identity or role\. Using RAM to apply a resource\-based policy, an AWS Private CA administrator can share access to a CA with a user in a different AWS account directly or through AWS Organizations\. Alternatively, an AWS Private CA administrator can use the PCA APIs [PutPolicy](https://docs.aws.amazon.com/privateca/latest/APIReference/API_PutPolicy.html), [GetPolicy](https://docs.aws.amazon.com/privateca/latest/APIReference/API_GetPolicy.html), and [DeletePolicy](https://docs.aws.amazon.com/privateca/latest/APIReference/API_DeletePolicy.html), or the corresponding AWS CLI commands [put\-policy](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/put-policy.html), [get\-policy](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/get-policy.html), and [delete\-policy](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/delete-policy.html), to apply and manage resource\-based policies\.

For general information about resource\-based policies, see [Identity\-Based Policies and Resource\-Based Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html) and [Controlling Access Using Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_controlling.html)\.

AWS Certificate Manager \(ACM\) users with cross\-account shared access to a private CA can issue managed certificates that are signed by the CA\. Cross\-account issuers are constrained by a resource\-based policy and have access only to the following end\-entity certificate templates:
+ [EndEntityCertificate/V1](UsingTemplates.md#EndEntityCertificate-V1)
+ [EndEntityClientAuthCertificate/V1](UsingTemplates.md#EndEntityClientAuthCertificate-V1)
+ [EndEntityServerAuthCertificate/V1](UsingTemplates.md#EndEntityServerAuthCertificate-V1)
+ [BlankEndEntityCertificate\_APIPassthrough/V1](UsingTemplates.md#BlankEndEntityCertificate_APIPassthrough)
+ [BlankEndEntityCertificate\_APICSRPassthrough/V1](UsingTemplates.md#BlankEndEntityCertificate_APICSRPassthrough)
+ [SubordinateCACertificate\_PathLen0/V1](UsingTemplates.md#SubordinateCACertificate_PathLen0-V1)

## Policy examples<a name="rbp-policy-examples"></a>

This section provides example cross\-account policies for various needs\. In all cases, the following command pattern is used to apply a policy:

```
$ aws acm-pca put-policy \
   --region region \
   --resource-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
   --policy file:///[path]/policyN.json
```

In addition to specifying the ARN of a CA, the administrator provides an AWS user account ID or an AWS Organizations ID that will be granted access to the CA\. The JSON of each of the following polices is formatted as a file for readability, but can also be supplied as an inline CLI arguments\.

**Note**  
The structure of the JSON resource\-based polices shown below must be followed precisely\. Only the ID fields for the principals \(the AWS account number or the AWS Organizations ID\) and the CA ARNs can be configured by customers\.

1. **File: policy1\.json – Sharing access to a CA with a user in a different account** 

   ```
    {                        
      "Version":"2012-10-17",
      "Statement":[
         {    
            "Sid":"1",
            "Effect":"Allow",         
            "Principal":{                                                                                                                                               
               "AWS":"account"                                                                                
            },
            "Action":[
               "acm-pca:DescribeCertificateAuthority",
               "acm-pca:GetCertificate",
               "acm-pca:GetCertificateAuthorityCertificate",
               "acm-pca:ListPermissions",
               "acm-pca:ListTags"                                                                                   
            ],                                                                                              
            "Resource":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID"
         },
         {
            "Sid":"1",  
            "Effect":"Allow",
            "Principal":{
               "AWS":"account"
            },
            "Action":[
               "acm-pca:IssueCertificate"
            ],
            "Resource":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
            "Condition":{
               "StringEquals":{
                  "acm-pca:TemplateArn":"arn:aws:acm-pca:::template/EndEntityCertificate/V1"
               }
            }
         }
      ]
   }
   ```

1. **File: policy2\.json – Sharing access to a CA through AWS Organizations**

   ```
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Sid":"1",
            "Effect":"Allow",
            "Principal":"*",
            "Action":"acm-pca:IssueCertificate",
            "Resource":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
            "Condition":{
               "StringEquals":{
                  "acm-pca:TemplateArn":"arn:aws:acm-pca:::template/EndEntityCertificate/V1",
                  "aws:PrincipalOrgID":"o-1b2c3d4z5"
               }
            }
         },
         {
            "Sid":"1",
            "Effect":"Allow",
            "Principal":"*",
            "Action":[
               "acm-pca:DescribeCertificateAuthority",
               "acm-pca:GetCertificate",
               "acm-pca:GetCertificateAuthorityCertificate",
               "acm-pca:ListPermissions",
               "acm-pca:ListTags"
            ],
            "Resource":"arn:aws:acm-pca:region:account:certificate-authority/CA_ID",
            "Condition":{
               "StringEquals":{
                  "aws:PrincipalOrgID":"o-a1b2c3d4z5"
               }
            }
         }
      ]
   }
   ```