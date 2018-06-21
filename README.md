

# https-certificate (TODO)

Signing Certificates With Your Own CA
======
1. Create the root ca certificate  
   1. Generate private key  
command: `openssl genrsa -des3 -out rootCA.key 2048`  
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
      DNS.1 = *.mydomain.com
      DNS.2 = mydomain.com
      ```  
      output file: **v3.ext**

   3. Sign the CSR using the root ca certificate from step 1.2     
command: `openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out server.crt -days 500 -sha256 -extfile v3.ext`  
output file: **server.crt**

3. Bundle all cert to PKCS12 keystore  
command: `openssl pkcs12 -inkey server.key -in server.crt -export -out server.pfx`  
output file: **server.pfx** -> the result keystore for using in server config

4. Import server.crt to your Trust Certificate Authority  


Reference:  
* https://docs.oracle.com/cd/E19509-01/820-3503/ggezy/index.html
* https://medium.com/@tbusser/creating-a-browser-trusted-self-signed-ssl-certificate-2709ce43fd15

Note
---
* You can use openssl from git bash, if you are windows user
* DNS is the domain name without specifying port (cannot be IP address)
* Only Firefox trusts this certificate (Edge or Chrome 58+ will display SSL error "DLG_FLAGS_INVALID_CA" and "NET::ERR_CERT_AUTHORITY_INVALID" respectivly)

Problem
---

1. Openssl hangs when trying to run any commands in Git Bash (Windows)  
   Solution: add **winpty** in front of command  
   Ex. `winpty openssl genrsa -des3 -out rootCA.key 4096`  
   Credit: https://stackoverflow.com/questions/18905795/creating-csr-with-openssl-hangs
   

Attribute Explaination (TODO)
---

Common Cert Attribute
* Version 3
* Subject Key Identifier

1. CA certificate  
   * Basic Constraints: Subject is a CA 
   * Key usage: `<?>`

2. Certificate for Server Authentication
   * Authority Key Identifier: `<subject key identifier of the issuer>`
   * Basic Constraints: `Subject is not a CA`
   * Key usage: `<?>`
   * Subject Alternative Name (SAN): 
      * DNS Name: `<yourdomainname>` 

Reference:  
* https://knowledge.digicert.com/solution/SO18140.html



