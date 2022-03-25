# Updating your private CA<a name="PCAUpdateCA"></a>

After creating a private CA, you can update its status or change its [revocation configuration](revocation-setup.md)\. This topic provides details about CA status and the CA lifecycle, along with examples of console and CLI updates to CAs\.

## Updating CA status<a name="PcaUpdateStatus"></a>

The status of a CA that is managed by ACM Private CA results from a user action or, in some cases, from a service action\. For example, a CA status changes when it expires\. The status options available to CA administrators vary depending on the current status of the CA\.

ACM Private CA can report the following status values\. The table shows the CA capabilities available in each state\.

**Note**  
For all status values except `DELETED` and `FAILED`, you are billed for the CA\.


****  

| Status | Issue certificates | Validate certs with OCSP | Generate CRLs | Generate audits | You can update the CA cert | Certificates can be revoked | You are billed for the CA | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| CREATING – The CA is being created\. | No | No | No | No | No | No | Yes | 
|  `PENDING_CERTIFICATE` – The CA has been created and needs a certificate to be operational\.\*  | No | No | No | No | No | No | Yes | 
| ACTIVE | Yes | Yes | Yes | Yes | Yes | Yes | Yes | 
| DISABLED – You have manually disabled the CA\. | No | Yes | Yes | Yes | No | Yes | Yes | 
| EXPIRED – The CA certificate has expired\.\*\* | No | No | No | No | Yes | No | Yes | 
| FAILED | The CreateCertificateAuthority action failed\. This can occur because of a network outage, backend AWS failure, or other errors\. A failed CA cannot be recovered\. Delete the CA and create a new one\. | No | 
| DELETED | Your CA is within the restoration period, which can have a length of seven to 30 days\. After this period, it is permanently deleted\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/PCAUpdateCA.html) | No | 

\* To complete activation, you need to generate a CSR, get a signed CA certificate from a CA, and import the certificate into ACM Private CA\. The CSR can be submitted either to your new CA \(for self\-signing\), or to an on\-premises root or subordinate CA\. For more information, see [Creating and installing the CA certificate](PCACertInstall.md)\.

\*\* You cannot directly change the status of an expired CA\. If you import a new certificate for the CA, ACM Private CA resets the status to `ACTIVE` unless it was set to `DISABLED` after the certificate expired\.

**Additional considerations about expired CA certificates:**
+ CA certificates are not automatically renewed\. For information about automating renewal through AWS Certificate Manager, see [Assign certificate renewal permissions to ACM](assign-permissions.md#PcaPermissions)\. 
+ If you attempt to issue a new certificate with an expired CA, the `IssueCertificate` API returns `InvalidStateException`\. An expired root CA must self\-sign a new root CA certificate before it can issue new subordinate certificates\.
+ `The ListCertificate Authorities` and `DescribeCertificateAuthority` APIs return a status of `EXPIRED` if the CA certificate is expired, regardless of whether the CA status is set to `ACTIVE` or `DISABLED`\. However, if the expired CA has been set to `DELETED`, the status returned is `DELETED`\.
+ The `UpdateCertificateAuthority` API cannot update the status of an expired CA\.
+ The `RevokeCertificate` API cannot be used to revoke any expired certificate, including a CA certificate\.

### CA status and CA lifecycle<a name="status-and-lifecycle"></a>

The following diagram illustrates the CA lifecycle as an interaction of management actions with CA status\.



![\[Interaction of CA management actions and status.\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/status.png)


**Diagram key**  

|  |  |  |  | 
| --- |--- |--- |--- |
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/rectangle.png) Management action  | ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/parallelogram.png)CA status |  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/arrow-solid.png) Action results in a state change\.  |  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/acm-pca/latest/userguide/images/arrow-dotted.png) New state enables new action\.  | 

At the top of the diagram, management actions are applied through the ACM Private CA console, CLI, or API\. The actions take the CA through creation, activation, expiration and renewal\. The CA status changes in response \(as shown by the solid lines\) to manual actions or automated updates\. In most cases, a new status leads to a new possible action \(shown by a dotted line\) that the CA administrator can apply\. The lower\-right inset shows the possible status values permitting delete and restore actions\.

**Topics**
+ [CA status and CA lifecycle](#status-and-lifecycle)