# Creating a private CA<a name="create-CA"></a>

You can use the procedures in this section to create either root CAs or subordinate CAs, resulting in an auditable hierarchy of trust relationships that matches your organizational needs\. You can create a CA using the AWS Management Console, the PCA portion of the AWS CLI, or AWS CloudFormation\.

For information about updating the configuration of a CA that you have already created, see [Updating your private CA](PCAUpdateCA.md)\.

For information about using a CA to sign end\-entity certificates for your users, devices, and applications, see [Issuing a private end\-entity certificate](PcaIssueCert.md)\.

**Note**  
Your account is charged a monthly price for each private CA starting from the time that you create it\.

**Topics**
+ [Procedure for creating a CA \(console\)](Create-CA-console.md)
+ [Procedure for creating a CA \(CLI\)](Create-CA-CLI.md)
+ [Using AWS CloudFormation to create a CA](Create-CA-CFN.md)