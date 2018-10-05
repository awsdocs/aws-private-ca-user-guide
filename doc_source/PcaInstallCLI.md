# Install the AWS Command Line Interface \(Optional\)<a name="PcaInstallCLI"></a>

If you have not installed the AWS CLI but want to use it, follow the directions at [AWS Command Line Interface](https://aws.amazon.com/cli/)\. If you are using Windows, you can download and run a 64\-bit or 32\-bit Windows installer\. If you are using Linux or macOS, you can install the AWS CLI using pip\. 

If you already have the AWS CLI installed, check the version number by typing `aws --version` in a command window or on the command line\. Compare the version number to the most recent available on [GitHub](https://github.com/aws/aws-cli)\. If your version is old, update the CLI\. 

On the command line, type `aws configure`\. You'll need your access key ID and secret access key to complete the following steps\. For more information, see [Access Keys](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys)\. 
+ Type your access key ID when prompted\.
+ Type your secret access key\.
+ Choose your default region\. See [Regions](PcaRegions.md) for a list of those available\.
+ Accept `json` as the default output format\.

Type `aws acm-pca` on the command line followed by the ACM PCA command that you want to run\. For example, if you want to list all of your private certificate authorities, type the following command\. 

```
aws acm-pca list-certificate-authorities --max-results 10
```