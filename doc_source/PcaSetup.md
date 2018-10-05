# Setting Up<a name="PcaSetup"></a>

Before you can use AWS Certificate Manager Private Certificate Authority, you must have a certificate authority \(CA\) available to sign your private CA certificate\. This can be a commercial CA or one that you set up and run on premises\. Best practices also include creating an IAM administrative group and one or more administrators within that group to run your private CAs\. You can optionally install the AWS CLI if you want to interact with ACM PCA and ACM on the command line\. 

**Topics**
+ [Install the CLI \(Optional\)](PcaInstallCLI.md)
+ [Create an Administrator](PcaIamAdmin.md)
+ [Set Up a CA](PcaOnPremises.md)