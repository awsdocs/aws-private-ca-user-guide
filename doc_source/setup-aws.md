# Setting up your AWS account and the AWS CLI<a name="setup-aws"></a>

If you're not already an Amazon Web Services \(AWS\) customer, you must sign up to be able to use AWS Private CA\. Your account automatically has access to all available services, but you are charged only for services that you use\. 

**Note**  
AWS Private CA is not available in the [AWS Free Tier](https://aws.amazon.com/free/)\.

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html#aws_tasks-that-require-root)\.

## Install the AWS Command Line Interface<a name="cli"></a>

If you have not installed the AWS CLI but want to use it, follow the directions at [AWS Command Line Interface](https://aws.amazon.com/cli/)\. In this guide, we assume that you have [configured](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) your endpoint, Region, and authentication details, and we omit these parameters from the sample commands\.