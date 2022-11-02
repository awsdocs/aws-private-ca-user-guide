# Terms and Concepts<a name="PcaTerms"></a>

The following terms and concepts can help you as you work with AWS Private Certificate Authority \(AWS Private CA\)\.

**Topics**
+ [Trust](#terms-trust)
+ [TLS server certificates](#terms-tlscert)
+ [Certificate signature](#terms-signing)
+ [Certificate authority](#terms-ca)
+ [Root CA](#terms-rootca)
+ [CA certificate](#terms-ca-cert)
+ [Root CA certificate](#terms-root)
+ [End\-entity certificate](#terms-endentity)
+ [Self\-signed certificates](#terms-selfsignedcert)
+ [Private certificate](#terms-pca-cert)
+ [Certificate path](#terms-certpath)
+ [Path length constraint](#terms-pathlength)

## Trust<a name="terms-trust"></a>

In order for a web browser to trust the identity of a website, the browser must be able to verify the website's certificate\. Browsers, however, trust only a small number of certificates known as CA root certificates\. A trusted third party, known as a certificate authority \(CA\), validates the identity of the website and issues a signed digital certificate to the website's operator\. The browser can then check the digital signature to validate the identity of the website\. If validation is successful, the browser displays a lock icon in the address bar\.

## TLS server certificates<a name="terms-tlscert"></a>

HTTPS transactions require server certificates to authenticate a server\. A server certificate is an X\.509 v3 data structure that binds the public key in the certificate to the subject of the certificate\. A TLS certificate is signed by a certificate authority \(CA\)\. It contains the name of the server, the validity period, the public key, the signature algorithm, and more\. 

## Certificate signature<a name="terms-signing"></a>

A digital signature is an encrypted hash over a certificate\. A signature is used to affirm the integrity of the certificate data\. Your private CA creates a signature by using a cryptographic hash function such as SHA256 over the variable\-sized certificate content\. This hash function produces an effectively unforgeable fixed\-size data string\. This string is called a hash\. The CA then encrypts the hash value with its private key and concatenates the encrypted hash with the certificate\.

## Certificate authority<a name="terms-ca"></a>

A certificate authority \(CA\) issues and if necessary revokes digital certificates\. The most common type of certificate is based on the ISO X\.509 standard\. An X\.509 certificate affirms the identity of the certificate subject and binds that identity to a public key\. The subject can be a user, an application, a computer, or other device\. The CA signs a certificate by hashing the contents and then encrypting the hash with the private key related to the public key in the certificate\. A client application such as a web browser that needs to affirm the identity of a subject uses the public key to decrypt the certificate signature\. It then hashes the certificate contents and compares the hashed value to the decrypted signature to determine whether they match\. For information about certificate signing, see [Certificate signature](#terms-signing)\. 

You can use AWS Private CA to create a private CA and use the private CA to issue certificates\. Your private CA issues only private SSL/TLS certificates for use within your organization\. For more information, see [Private certificate](#terms-pca-cert)\. Your private CA also requires a certificate before you can use it\. For more information, see [CA certificate](#terms-ca-cert)\. 

## Root CA<a name="terms-rootca"></a>

A cryptographic building block and root of trust upon which certificates can be issued\. It comprises a private key for signing \(issuing\) certificates and a root certificate that identifies the root CA and binds the private key to the name of the CA\. The root certificate is distributed to the trust stores of each entity in an environment\. Administrators construct trust stores to include only the CAs that they trust\. Administrators update or build the trust stores into the operating systems, instances, and host machine images of entities in their environment\. When resources attempt to connect with one another, they check the certificates that each entity presents\. A client checks the certificates for validity and whether a chain exists from the certificate to a root certificate installed in the trust store\. If those conditions are met, a “handshake" is accomplished between the resources\. This handshake cryptographically proves the identity of each entity to the other and creates an encrypted communication channel \(TLS/SSL\) between them\.

## CA certificate<a name="terms-ca-cert"></a>

A certificate authority \(CA\) certificate affirms the identity of the CA and binds it to the public key that is contained in the certificate\. 

You can use AWS Private CA to create a private root CA or a private subordinate CA, each backed by a CA certificate\. Subordinate CA certificates are signed by another CA certificate higher in a chain of trust\. But in the case of a root CA, the certificate is self\-signed\. You can also establish an external root authority \(hosted on premises, for example\)\. You can then use your root authority to sign a subordinate root CA certificate hosted by AWS Private CA\.

The following example shows the typical fields contained in an AWS Private CA X\.509 CA certificate\. Note that for a CA certificate, the `CA:` value in the `Basic Constraints` field is set to `TRUE`\. 

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 4121 (0x1019)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=US, ST=Washington, L=Seattle, O=Example Company Root CA, OU=Corp, CN=www.example.com/emailAddress=corp@www.example.com
        Validity
            Not Before: Feb 26 20:27:56 2018 GMT
            Not After : Feb 24 20:27:56 2028 GMT
        Subject: C=US, ST=WA, L=Seattle, O=Examples Company Subordinate CA, OU=Corporate Office, CN=www.example.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:c0: ... a3:4a:51
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                F8:84:EE:37:21:F2:5E:0B:6C:40:C2:9D:C6:FE:7E:49:53:67:34:D9
            X509v3 Authority Key Identifier:
                keyid:0D:CE:76:F2:E3:3B:93:2D:36:05:41:41:16:36:C8:82:BC:CB:F8:A0

            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Key Usage: critical
                Digital Signature, CRL Sign
    Signature Algorithm: sha256WithRSAEncryption
         6:bb:94: ... 80:d8
```

## Root CA certificate<a name="terms-root"></a>

A certificate authority \(CA\) typically exists within a hierarchical structure that contains multiple other CAs with clearly defined parent–child relationships between them\. Child or subordinate CAs are certified by their parent CAs, creating a certificate chain\. The CA at the top of the hierarchy is referred to as the root CA, and its certificate is called the root certificate\. This certificate is typically self\-signed\. 

## End\-entity certificate<a name="terms-endentity"></a>

An end\-entity certificate identifies a resource, such as a server, instance, container or device\. Unlike CA certificates, end\-entity certificates cannot be used to issue certificates\. Other common terms for end\-entity certificate are "client" or "subscriber" certificate\. 

## Self\-signed certificates<a name="terms-selfsignedcert"></a>

A certificate signed by the issuer instead of a higher CA\. Unlike certificates issued from a secure root maintained by a CA, self\-signed certificates act as their own root, and as a result they have significant limitations: They can be used to provide on the wire encryption but not to verify identity, and they cannot be revoked\. They are unacceptable from a security perspective\. But organizations use them nonetheless because they are easy to generate, require no expertise or infrastructure, and many applications accept them\. There are no controls in place for issuing self\-signed certificates\. Organizations that use them incur greater risk of outages caused by certificate expirations because they have no way to track expiration dates\.

## Private certificate<a name="terms-pca-cert"></a>

AWS Private CA certificates are private SSL/TLS certificates that you can use within your organization, but are untrusted on the public internet\. Use them to identify resources such as clients, servers, applications, services, devices, and users\. When establishing a secure encrypted communications channel, each resource uses a certificate like the following as well as cryptographic techniques to prove its identity to another resource\. Internal API endpoints, web servers, VPN users, IoT devices, and many other applications use private certificates to establish encrypted communication channels that are necessary for their secure operation\. By default, private certificates are not publicly trusted\. An internal administrator must explicitly configure applications to trust private certificates and distribute the certificates\. 

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            e8:cb:d2:be:db:12:23:29:f9:77:06:bc:fe:c9:90:f8
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=US, ST=WA, L=Seattle, O=Example Company CA, OU=Corporate, CN=www.example.com
        Validity
            Not Before: Feb 26 18:39:57 2018 GMT
            Not After : Feb 26 19:39:57 2019 GMT
        Subject: C=US, ST=Washington, L=Seattle, O=Example Company, OU=Sales, CN=www.example.com/emailAddress=sales@example.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00...c7
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Basic Constraints:
                CA:FALSE
            X509v3 Authority Key Identifier:
                keyid:AA:6E:C1:8A:EC:2F:8F:21:BC:BE:80:3D:C5:65:93:79:99:E7:71:65

            X509v3 Subject Key Identifier:
                C6:6B:3C:6F:0A:49:9E:CC:4B:80:B2:8A:AB:81:22:AB:89:A8:DA:19
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage:
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 CRL Distribution Points:

                Full Name:
                  URI:http://NA/crl/12345678-1234-1234-1234-123456789012.crl

    Signature Algorithm: sha256WithRSAEncryption
         58:32:...:53
```

## Certificate path<a name="terms-certpath"></a>

A client that relies on a certificate validates that a path exists from the end\-entity certificate, possibly through a chain of intermediate certificates, to a trusted root\. The client checks that each certificate along the path is valid \(not revoked\)\. It also checks that the end\-entity certificate has not expired, has integrity \(has not been tampered with or modified\), and that constraints in the certificate are enforced\.

## Path length constraint<a name="terms-pathlength"></a>

The basic constraints *pathLenConstraint* for a CA certificate sets the number of subordinate CA certificates that may exist in the chain below it\. For example, a CA certificate with a path length constraint of zero cannot have any subordinate CAs\. A CA with a path length constraint of one may have up to one level of subordinate CAs underneath it\. [RFC 5280](https://datatracker.ietf.org/doc/html/rfc5280#section-4.2.1.9) defines this as, “the maximum number of non\-self\-issued intermediate certificates that may follow this certificate in a valid certification path\." The path length value excludes the end\-entity certificate, though informal language about the "length" or "depth" of a validation chain may include it\.\.\.leading to confusion\.