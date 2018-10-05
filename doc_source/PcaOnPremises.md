# Set Up a Certificate Authority<a name="PcaOnPremises"></a>

You must have a certificate authority \(CA\) available to sign your private CA certificate\. This can be a commercial or an on\-premises CA\. It can be a root or an intermediate CA\. You can use OpenSSL to create a test certificate authority\. We recommend, however, that you use a production CA rather than a test CA\. You use the certificate authority to sign your private CA certificate\. Signing the certificate is part of the process that you must follow to create your CA and get it ready to use: 

1. [Step 1: Create a Private Certificate Authority](PcaCreateCa.md)

1. [Step 2: Get a Certificate Signing Request \(CSR\)](PcaGetCsr.md)

1. [Step 3: Sign Your Private CA Certificate](PcaSignCert.md)

1. [Step 4: Import Your Private CA Certificate into ACM PCA](PcaImportCaCert.md)