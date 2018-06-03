

# https-certificate (TODO)

Signing Certificates With Your Own CA
======
TL;DR
1. Create the root ca certificate  
   1. Generate private key  
command: `openssl genrsa -des3 -out rootCA.key 4096`  
output file: **rootCA.key**  

   2. Generate root ca cert  
command: `openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 365  -out rootCA.pem`  
output file: **rootCA.pem**

2. Create a certificate issued by the created root ca certificate  
   1. create a server Certificate Signing Request (CSR)  
command: `openssl req -new -nodes -out server.csr -newkey rsa:2048 -keyout server.key`  
output file: **server.csr, server.key**

   2. create v3.ext with the following content ( required to fix ERR_CERT_COMMON_NAME_INVALID)  
      ```
      authorityKeyIdentifier=keyid,issuer
      basicConstraints=CA:FALSE
      keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
      subjectAltName = @alt_names
      [alt_names]
      DNS.1 = acme-site.dev
      DNS.2 = acme-static.dev
      ```  
      output file: **v3.ext**

   3. Sign the CSR using the root ca certificate from step 1.2     
command: `openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out server.crt -days 500 -sha256 -extfile v3.ext`  
output file: **server.crt**

3. Bundle all cert to PKCS12 keystore  
command: `openssl pkcs12 -inkey server.key -in server.crt -export -out server.pfx`  
output file: **server.pfx** -> the result keystore for using in server config


Reference:  
* https://docs.oracle.com/cd/E19509-01/820-3503/ggezy/index.html
* https://medium.com/@tbusser/creating-a-browser-trusted-self-signed-ssl-certificate-2709ce43fd15

