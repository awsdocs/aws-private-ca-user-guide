# Concepts<a name="PcaConcepts"></a>

This topic introduces basic terms and concepts related to AWS Certificate Manager Private Certificate Authority \(ACM PCA\)\.

**Topics**
+ [Certificate Authority](#concept-ca)
+ [Certificate Authority Certificate](#concept-ca-cert)
+ [Certificate Signature](#concept-signing)
+ [Private Certificate](#concept-pca-cert)
+ [Asymmetric Key Cryptography](#concept-asymmetric)
+ [Domain Name System](#concept-dns)
+ [Domain Names](#concept-dn)
+ [Fully Qualified Domain Name \(FQDN\)](#concept-fqdn)
+ [Public Key Infrastructure](#concept-pki)
+ [Root Certificate](#concept-root)
+ [Secure Sockets Layer \(SSL\)](#concept-ssl)
+ [Secure HTTPS](#concept-https)
+ [SSL Server Certificates](#concept-sslcert)
+ [Transport Layer Security \(TLS\)](#concept-tls)
+ [Trust](#concept-trust)
+ [X\.500 Distinguished Name](#concept-x500dn)

## Certificate Authority<a name="concept-ca"></a>

A certificate authority \(CA\) issues and if necessary revokes digital certificates\. The most common type of certificate is based on the ISO X\.509 standard\. An X\.509 certificate affirms the identity of the certificate subject and binds that identity to a public key\. The subject can be a user, an application, a computer, or other device\. The CA signs a certificate by hashing the contents and then encrypting the hash with the the private key related to the public key in the certificate\. A client application such as a web browser that needs to affirm the identity of a subject uses the public key to decrypt the certificate signature\. It then hashes the certificate contents and compares the hashed value to the decrypted signature to determine whether they match\. For information about key pairs, see [Asymmetric Key Cryptography](#concept-asymmetric)\. For information about certificate signing, see [Certificate Signature](#concept-signing)\. 

You can use ACM PCA to create a private CA and use the private CA to issue certificates\. Your private CA issues only private SSL/TLS certificates for use within your organization\. For more information, see [Private Certificate](#concept-pca-cert)\. Your private CA also requires a certificate before you can use it\. For more information, see [Certificate Authority Certificate](#concept-ca-cert)\. 

## Certificate Authority Certificate<a name="concept-ca-cert"></a>

A certificate authority \(CA\) certificate affirms the identity of the CA and binds it to the public key that is contained in the certificate\. The following example shows the typical fields contained in an ACM PCA X\.509 CA certificate\. Note that for a CA certificate, the `CA:` value in the `Basic Constraints` field is set to `TRUE`\. 

When you use ACM PCA to create a private CA, ACM PCA creates a certificate signing request \(CSR\)\. You must take the CSR to your on\-premises [public key infrastructure](#concept-pki) \(PKI\) to create a signed CA certificate\. Import the CA certificate into ACM PCA\. Your private CA will be subordinate to the CA that you used to sign it\. 

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

## Certificate Signature<a name="concept-signing"></a>

A digital signature is an encrypted hash over a certificate\. A signature is used to affirm the integrity of the certificate data\. Your private CA creates a signature by using a hash function such as SHA256 over the variable\-sized certificate content to produce an irreversible fixed\-size data string\. The fixed data is called a hash\. The CA then encrypts the hash value with its private key and concatenates the encrypted hash with the certificate\. 

To validate a signed certificate, a client application uses the CA public key to decrypt the signature\. The client then uses the same signing algorithm that the CA used to compute a hash over the rest of the certificate\. Note that the signing algorithm used by the CA is listed in the certificate\. If the computed hash value is the same as the decrypted hash value, the certificate has not been tampered with\. 

## Private Certificate<a name="concept-pca-cert"></a>

ACM PCA certificates are private SSL/TLS certificates that you can use within your organization\. Use them to identify resources such as clients, servers, applications, services, devices, and users\. When establishing a secure encrypted communications channel, each resource uses a certificate like the following as well as cryptographic techniques to prove its identity to another resource\. Internal API endpoints, web servers, VPN users, IoT devices, and many other applications use private certificates to establish encrypted communication channels that are necessary for their secure operation\. By default, private certificates are not publicly trusted\. An internal administrator must explicitly configure applications to trust private certificates and distribute the certificates\. 

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

## Asymmetric Key Cryptography<a name="concept-asymmetric"></a>

Asymmetric cryptography uses different but mathematically related keys to encrypt and decrypt content\. One of the keys is public and is made available in an X\.509 v3 certificate\. The other key is private and is stored securely\. The X\.509 certificate binds the identity of a user, computer, or other resource \(the certificate subject\) to the public key\. 

Private CA certificates and certificates issued by a private CA are X\.509 SSL/TLS certificates\. They bind the identity of a user, service, application, computer, or other device with the public key that's embedded in the certificate\. The associated private key is securely stored in AWS CloudHSM\. 

## Domain Name System<a name="concept-dns"></a>

The Domain Name System \(DNS\) is a hierarchical distributed naming system for computers and other resources connected to the internet or a private network\. DNS is primarily used to translate textual domain names, such as `aws.amazon.com`, into numerical IP \(internet protocol\) addresses of the form `111.222.333.444`\. 

## Domain Names<a name="concept-dn"></a>

A domain name is a text string such as `www.example.com` that can be translated by the Domain Name System \(DNS\) into an IP address\. Computer networks, including the internet, use IP addresses rather than text names\. A domain name consists of distinct labels separated by periods\. 

**TLD**  
The rightmost label is called the top\-level domain \(TLD\)\. Common examples include `.com`, `.net`, and `.edu`\. Also, the TLD for entities registered in some countries is an abbreviation of the country name and is called a country code\. Examples include `.uk` for the United Kingdom, `.ru` for Russia, and `.fr` for France\. When country codes are used, a second\-level hierarchy for the TLD is often introduced to identify the type of the registered entity\. For example, the `.co.uk` TLD identifies commercial enterprises in the United Kingdom\. 

**Apex domain**  
The apex domain name includes and expands on the top\-level domain\. For domain names that include a country code, the apex domain includes the code and the labels, if any, that identify the type of the registered entity\. The apex domain does not include subdomains \(see the following paragraph\)\. In `www.example.com`, the name of the apex domain is `example.com`\. In `www.example.co.uk`, the name of the apex domain is `example.co.uk`\. Other names that are often used instead of apex include base, bare, root, root apex, or zone apex\. 

**Subdomain**  
Subdomain names precede the apex domain name and are separated from it and from each other by a period\. The most common subdomain name is `www`, but any name is possible\. Also, subdomain names can have multiple levels\. For example, in `jake.dog.animals.example.com`, the subdomains are `jake`, `dog`, and `animals` in that order\. 

**FQDN**  
A fully qualified domain name \(FQDN\) is the complete DNS name for a computer, website, or other resource connected to a network or to the internet\. For example `aws.amazon.com` is the FQDN for Amazon Web Services\. An FQDN includes all domains up to the top\-level domain\. For example, `[subdomain1].[subdomain2]...[subdomainn].[apex domain].[top–level domain]` represents the general format of an FQDN\.

**PQDN**  
A domain name that is not fully qualified is called a partially qualified domain name \(PQDN\) and is ambiguous\. A name such as `[subdomain1.subdomain2.]` is a PQDN because the root domain cannot be determined\. 

**Registration**  
The right to use a domain name is delegated by domain name registrars\. Registrars are typically accredited by the Internet Corporation for Assigned Names and Numbers \(ICANN\)\. In addition, other organizations called registries maintain the TLD databases\. When you request a domain name, the registrar sends your information to the appropriate TLD registry\. The registry assigns a domain name, updates the TLD database, and publishes your information to WHOIS\. Typically, domain names must be purchased\. 

## Fully Qualified Domain Name \(FQDN\)<a name="concept-fqdn"></a>

See [Domain Names](#concept-dn)\.

## Public Key Infrastructure<a name="concept-pki"></a>

A public key infrastructure \(PKI\) is a comprehensive system that enables the creation, issuance, management, distribution, use, storage, and revocation of digital certificates\. A PKI consists of people, hardware, software, policies, documents, and procedures\. In ACM PCA, the purpose of a PKI is to manage private certificates\. ACM PCA implements the following: 

**Public Key Certificates**  
Your private CA issues certificates to affirm the identities of users, services, applications, computers, and other devices within your organization\. For more information, see [Private Certificate](#concept-pca-cert)\. 

**Certificate Repository**  
Your private CA stores all of the certificates it has issued\. You can, of course, delete a certificate from the ACM PCA repository\. 

**Certificate Revocation**  
ACM PCA supports certificate revocation\. Certificates that can no longer be trusted must be revoked by the certificate authority\. A certificate can be revoked before the end of its validity period for any number of reasons\. Common reasons include key compromise, end of operations, a change in affiliation, or the withdrawal of privilege\. When you create a private CA, you specify whether you want the CA to support certificate revocation\. If the CA supports revocation, you can request an audit report to review certificates that you have revoked\. 

**Key Storage**  
Your CA private keys are locked away securely in a hardware security module \(HSM\) that is owned and managed by Amazon\. 

## Root Certificate<a name="concept-root"></a>

A certificate authority \(CA\) typically exists within a hierarchical structure that contains multiple other CAs with clearly defined parent–child relationships between them\. Child or subordinate CAs are certified by their parent CAs, creating a certificate chain\. The CA at the top of the hierarchy is referred to as the root CA, and its certificate is called the root certificate\. This certificate is typically self\-signed\. 

## Secure Sockets Layer \(SSL\)<a name="concept-ssl"></a>

Secure Sockets Layer \(SSL\) and Transport Layer Security \(TLS\) are cryptographic protocols that provide communication security over a computer network\. TLS is the successor of SSL\. They both use X\.509 certificates to authenticate the server\. Both protocols negotiate a symmetric key to encrypt data that flows between the client and server\. 

## Secure HTTPS<a name="concept-https"></a>

HTTPS stands for HTTP over SSL/TLS, a secure form of HTTP that is supported by all major browsers and servers\. All HTTP requests and responses are encrypted before being sent across a network\. HTTPS combines the HTTP protocol with symmetric, asymmetric, and X\.509 certificate\-based cryptographic techniques\. HTTPS works by inserting a cryptographic security layer below the HTTP application layer and above the TCP transport layer in the Open Systems Interconnection \(OSI\) model\. The security layer uses the Secure Sockets Layer \(SSL\) protocol or the Transport Layer Security \(TLS\) protocol\. 

## SSL Server Certificates<a name="concept-sslcert"></a>

HTTPS transactions require server certificates to authenticate a server\. A server certificate is an X\.509 v3 data structure that binds the public key in the certificate to the subject of the certificate\. An SSL/TLS certificate is signed by a certificate authority \(CA\) and contains the name of the server, the validity period, the public key, the signature algorithm, and more\. 

## Transport Layer Security \(TLS\)<a name="concept-tls"></a>

See [Secure Sockets Layer \(SSL\)](#concept-ssl)\.

## Trust<a name="concept-trust"></a>

In order for a web browser to trust the identity of a website, the browser must be able to verify the website's certificate\. Browsers, however, trust only a small number of certificates known as CA root certificates\. A trusted third party, known as a certificate authority \(CA\), validates the identity of the website and issues a signed digital certificate to the website's operator\. The browser can then check the digital signature to validate the identity of the website\. If validation is successful, the browser displays a lock icon in the address bar\.

## X\.500 Distinguished Name<a name="concept-x500dn"></a>

X\.500 distinguished names \(DN\) are used to identify users, computers, applications, services, servers, and other devices in the X\.509 public key certificates that ACM PCA creates\. This includes private certificates and private CA certificates\. Common fields include the following: 
+ **organizationName \(O\)** – Name of the organization that issued or is the subject of a certificate
+ **organiztionUnit \(OU\)** – Department or division within an organization
+ **country \(C\)** – Two letter country code
+ **stateName \(S\)** – Name of a state or province such as Washington
+ **localityName \(L\)** – Locality name such as Seattle
+ **commonName \(CN\)** – Common name of the certificate subject or issuer