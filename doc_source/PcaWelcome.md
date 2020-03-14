# What Is ACM Private CA?<a name="PcaWelcome"></a>

AWS Certificate Manager Private Certificate Authority is a managed private CA service that extends ACM certificate management to private certificates\. With private certificates you can authenticate resources inside an organization\. Private certificates allow entities like users, web servers, VPN users, internal API endpoints, and IoT devices to prove their identity and establish encrypted communications channels\. With ACM Private CA, you can create complete CA hierarchies, including root and subordinate CAs, without the investment and maintenance costs of operating your own certificate authority\. 

With a private CA, you avoid many of the constraints that are imposed on public certificates and CAs\. Using the ACM Private CA console and API operations, you can do the following:
+ Create certificates with any subject name you want\.
+ Create certificates with any expiration date you want\.
+ Use any supported private key algorithm and key length\.
+ Use any supported signing algorithm\.
+ Configure certificates in bulk using templates\.

This section provides an overview of ACM Private CA service provisions and constraints\.