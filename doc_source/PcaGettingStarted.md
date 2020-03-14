# Setting UpACM Private CA<a name="PcaGettingStarted"></a>

Before using ACM Private CA, you might need to complete a couple of tasks\. 

\(Optional\) In addition, you should determine whether your organization prefers to host its private root CA credentials on premises rather than with AWS\. In that case, you need to set up and secure a self\-managed private PKI before using ACM Private CA\. In this scenario, you then create a subordinate CA in ACM Private CA backed by a parent CA outside of ACM Private CA\. For more information, see [Using a Root Authority Outside ACM Private CA](https://docs.aws.amazon.com/acm-pca/latest/userguide/PCACertInstall.html#InstallSubordinateExternal)\.

## Sign Up for AWS<a name="setup-aws"></a>

If you're not already an Amazon Web Services \(AWS\) customer, you must sign up to be able to use ACM Private CA\. Your account automatically has access to all available services, but you are charged only for services that you use\. Also, if you are a new AWS customer, some services are available for free during a limited period\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\. 

**Note**  
ACM Private CA is not available in the free tier\.

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Install the AWS Command Line Interface \(Optional\)<a name="PcaInstallCLI"></a>

If you have not installed the AWS CLI but want to use it, follow the directions at [AWS Command Line Interface](https://aws.amazon.com/cli/)\. If you are using Windows, you can download and run a 64\-bit or 32\-bit Windows installer\. If you are using Linux or macOS, you can install the AWS CLI using pip\. 

If you already have the AWS CLI installed, check the version number by typing `aws --version` in a command window or on the command line\. Compare the version number to the most recent available on [GitHub](https://github.com/aws/aws-cli)\. If your version is old, update the CLI\. 

After you have installed the AWS CLI, you need to configure it\.

**To configure the AWS CLI**  
On the command line, type `aws configure`\. You'll need your access key ID and secret access key to complete the following steps\. For more information, see [Access Keys](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys)\. 
+ Type your access key ID when prompted\.
+ Type your secret access key\.
+ Choose your default Region\. See [Regions](PcaRegions.md) for a list of those available\.
+ Accept `json` as the default output format\.

You can use the AWS CLI for many ACM Private CA tasks\.

**To use the AWS CLI with ACM Private CA**
+ In a command line window, type `aws acm-pca` on the command line followed by the ACM Private CA command that you want to run\. For example, if you want to list all of your private certificate authorities, type the following command\. 

  ```
  aws acm-pca list-certificate-authorities --max-results 10
  ```