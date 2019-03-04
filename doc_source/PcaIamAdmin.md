# Create an IAM Administrator<a name="PcaIamAdmin"></a>

As a best practice, don't use your AWS account root user to interact with AWS, including ACM PCA\. Instead use AWS Identity and Access Management \(IAM\) to create an IAM user, IAM role, or federated user\. Create an administrator group and add yourself to it\. Then log in as an administrator\. Add additional users to the group as needed\. 

Another best practice is to create a customer managed policy that you can assign to users\. Users can perform only the ACM PCA actions that the policy allows\. 

**Topics**
+ [Sign Up for AWS](#setup-aws)
+ [Create an Administrator](#create-iam-admin)
+ [Create a Customer Managed Policy](#restrict-permissions)

## Sign Up for AWS<a name="setup-aws"></a>

If you're not already an Amazon Web Services \(AWS\) customer, you must sign up to be able to use ACM PCA\. Your account is automatically signed up for all available services, but you are charged for only the services that you use\. Also, if you are a new AWS customer, some services are available for free during a limited period\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\. 

**Note**  
ACM PCA is not available in the free tier\.

**To sign up for an AWS account**

1. Go to [https://aws\.amazon\.com/](https://aws.amazon.com/) and choose **Sign Up**\. 

1. Follow the on\-screen instructions\.

**Note**  
Part of the sign\-up procedure includes receiving an automated telephone call and entering the supplied PIN on the telephone keypad\. You must also supply a credit card number\. 

## Create an Administrator<a name="create-iam-admin"></a>

You can use the IAM console to create users and groups with the permissions they need to work with ACM PCA\.

**To create an IAM user for yourself and add the user to an Administrators group**

1. Use your AWS account email address and password to sign in as the *[AWS account root user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)* to the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane of the console, choose **Users**, and then choose **Add user**\.

1. For **User name**, type **Administrator**\.

1. Select the check box next to **AWS Management Console access**, select **Custom password**, and then type the new user's password in the text box\. You can optionally select **Require password reset** to force the user to create a new password the next time the user signs in\.

1. Choose **Next: Permissions**\.

1. On the **Set permissions** page, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** type **Administrators**\.

1. For **Filter policies**, select the check box for **AWS managed \- job function**\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags** to add metadata to the user by attaching tags as key\-value pairs\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users, and to give your users access to your AWS account resources\. To learn about using policies to restrict users' permissions to specific AWS resources, go to [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

You can create multiple administrators in your account and add each to the Administrators group\. To sign in to the AWS Management Console, each user needs an AWS account ID or alias\. To get these, see [Your AWS Account ID and Its Alias](https://docs.aws.amazon.com/IAM/latest/UserGuide/console_account-alias.html) in the *IAM User Guide*\. 

## Create a Customer Managed Policy<a name="restrict-permissions"></a>

The following sample [customer managed policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#create-managed-policy-console) allows a user to create a CA audit report\. This is a sample only\. You can choose any ACM PCA operations you want\. For more examples, see [Inline Policies](auth-inlinepolicies.md)\. 

**To create a customer managed policy**

1. Sign in to the IAM console using the credentials of an AWS administrator\.

1. In the navigation pane of the console, choose **Policies**\.

1. Choose **Create policy**\.

1. Choose the **JSON** tab\.

1. Copy the following policy into the editor\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [{
       "Effect": "Allow",
       "Action": "acm-pca:CreateCertificateAuthorityAuditReport",
       "Resource": "*"
     }]
   }
   ```

1. Choose **Review policy**\.

1. For **Name**, type `PcaListPolicy`\.

1. \(Optional\) Type a description\.

1. Choose **Create policy**\.

An administrator can assign the policy to any IAM user to limit what ACM PCA actions the user can perform\.