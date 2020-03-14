# Handling Other Exceptions and Failures<a name="PCATsExceptions"></a>

An ACM Private CA command might fail for several reasons\. For information on each exception and recommendations for resolving them, see the table below\.


**ACM Private CA Failures**  

|  Failure Name  |  Exception Returned by ACM Private CA  | Description | Recommendation | 
| --- | --- | --- | --- | 
|  `PCA_LIMIT_EXCEEDED`  | LimitExceededException | Each certificate authority \(CA\) has a quota of certificates that it can issue\. The private CA that is associated with the designated certificate has reached its quota\. For more information, see [Quotas](PcaLimits.md)\. | Contact the [AWS Support Center](https://aws.amazon.com/premiumsupport/) to request a quota increase\. | 
|  `PCA_INVALID_ARGS`  |  `InvalidArgsException`  | A certificate creation or renewal request was made with invalid parameters\. | Check the command's individual documentation to make sure that your input parameters are valid\. If you are creating a new certificate, make sure that the requested signing algorithm can be used with the CA's key type\. | 
|  `PCA_INVALID_STATE`  |  `InvalidStateException`  | The associated private CA cannot renew the certificate because it is not in the ACTIVE state\. | Attempt to [restore the private CA](PCARestoreCA.md)\. If the private CA is outside of its restoration period, the CA cannot be restored and the certificate cannot be renewed\. | 
|  `PCA_REQUEST_FAILED`  |  `RequestFailedException`  |  A networking problem in your AWS environment caused the request to fail\.  |  Retry the request\. If the failure persists, check your [Amazon VPC \(VPC\) configuration](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)\.  | 
|  `PCA_RESOURCE_NOT_FOUND`  |  `ResourceNotFoundException`  |  The private CA that issued the certificate was deleted and no longer exists\.  |  Request a new certificate from another active CA\.  | 
|  `PCA_VALIDATION_EXCEPTION`  |  `ValidationException`  |  The request's input parameters were incorrectly formatted, or the validity period of the root certificate ends before the validity period of the requested certificate\.  |  Check the syntax requirements of the command's input parameters as well as the validity period of your CA's root certificate\. For information about changing the validity period, see [Updating Your Private CA](PCAUpdateCA.md)\.  | 
| PCA\_MALFORMED\_CSR | MalformedCSRException | The certificate signing request \(CSR\) that was submitted to ACM Private CA cannot be verified or validated\. | Confirm that your CSR was properly generated and configured\.  | 
|  `PCA_ACCESS_DENIED`  |  `AccessDeniedException`  |  The permissions required to use the given command have not been delegated by a private CA to the calling account\.  |  For information on delegating permissions in ACM Private CA, see [Assigning Certificate Renewal Permissions to ACM](PcaPermissions.md)\.  | 
|  `OTHER`  |  `OtherException`  |  An internal error has caused the request to fail\.  |  Attempt to run the command again\. If the problem persists, contact the [AWS Support Center](https://aws.amazon.com/premiumsupport/)\.  | 