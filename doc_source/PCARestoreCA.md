# Restoring a private CA<a name="PCARestoreCA"></a>

You can restore a private CA that has been deleted as long as the CA remains within the restoration period that you specified upon deletion\. The restoration period is from 7\-30 days\. At the end of that period, the private CA is permanently deleted\. For more information, see [Deleting your private CA](PCADeleteCA.md)\. You cannot restore a private CA that has been permanently deleted\. 

**Note**  
You are not charged for a private CA after it has been deleted\. However, if a deleted CA is restored, you are charged for the time between deletion and restoration\. For more information, see [Pricing](PcaPricing.md)\.

## Restoring a private CA \(console\)<a name="RestoreCAConsole"></a>

You can use the AWS Management Console to restore a private CA\.

**To restore a private CA \(console\)**

1. Sign in to your AWS account and open the AWS Private CA console at [https://console\.aws\.amazon\.com/acm\-pca/home](https://console.aws.amazon.com/acm-pca/home)\. 

1. On the **Private certificate authorities** page, choose your deleted private CA from the list\.

1. On the **Actions** menu, choose **Restore**\. 

1. On the **Restore CA** page, choose **Restore** again\.

1. If successful, the status of the private CA is set to its pre\-deletion state\. Choose **Actions**, **Enable**, and **Enable** again to change its status to `ACTIVE`\. If the private CA was in the `PENDING_CERTIFICATE` state at the time of deletion, you must import a CA certificate into the private CA before you can activate it\.

## Restoring a private CA \(AWS CLI\)<a name="RestoreCli"></a>

Use the [restore\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/restore-certificate-authority.html) command to restore a deleted private CA that is in the `DELETED` state\. The following steps discuss the entire process required to delete, restore, and then reactivate a private CA\. 

**To delete, restore, and reactivate a private CA \(AWS CLI\)**

1. Delete the private CA\.

   Run the [delete\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/delete-certificate-authority.html) command to delete the private CA\. If the private CA's status is `DISABLED` or `PENDING_CERTIFICATE`, you can set the `--permanent-deletion-time-in-days` parameter to specify the private CA's restoration period from 7 \-30 days\. If you do not specify a restoration period, the default is 30 days\. If successful, this command sets the status of the private CA to `DELETED`\.
**Note**  
To be restorable, the private CA's status at the time of deletion must be `DISABLED` or `PENDING_CERTIFICATE`\.

   ```
   $ aws acm-pca delete-certificate-authority \
        --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
        --permanent-deletion-time-in-days 16
   ```

1. Restore the private CA\.

   Run the [restore\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/restore-certificate-authority.html) command to restore the private CA\. You must run the command before the restoration period that you set with the delete\-certificate\-authority command expires\. If successful, the command sets the status of the private CA to its pre\-deletion status\.

   ```
   $ aws acm-pca restore-certificate-authority \
        --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID
   ```

1. Make the private CA `ACTIVE`\.

   Run the [update\-certificate\-authority](https://docs.aws.amazon.com/cli/latest/reference/acm-pca/update-certificate-authority.html) command to change the status of the private CA to `ACTIVE`\.

   ```
   $ aws acm-pca update-certificate-authority \
        --certificate-authority-arn arn:aws:acm-pca:region:account:certificate-authority/CA_ID \
        --status ACTIVE
   ```