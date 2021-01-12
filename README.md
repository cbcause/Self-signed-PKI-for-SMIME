# Self-signed-PKI-for-SMIME
Condensed instructions and commands for creating a basic self signed PKI for email protection with S/MIME. The instructions include the neccesary steps for creating S/MIME certificates compatible with Apple Mail and iOS Mail clients. The commands include parameters like -days and -config tailored for my preferences. Feel free to play around and learn more about these on https://www.openssl.org/docs/manmaster/. In the future i will maybe create a universal setup script. For now, here are the plain text instructions:

---------------------------------------------------------------------------------------------------------------

S/MIME Self Signed (Mac compatible)

You need to add the following attributes to your key otherwise it will not work with iPhones/Mac etc. In your openssl.cnf you need to define:
* keyUsage = nonRepudiation, digitalSignature, keyEncipherment
* extendedKeyUsage = clientAuth,emailProtection
* subjectAltName=email:move

---------------------------------------------------------------------------------------------------------------

1 - GENERATE CA KEY
openssl genrsa -aes256 -out CA.key 4096

2 - GENERATE SELF-SIGNED CA CERTIFICATE
openssl req -new -x509 -days 3650 -key CA.key -out CA.crt -config /Users/user/openssl-smime.cnf

---------------------------------------------------------------------------------------------------------------

3 - GENERATE CLIENT KEY
openssl genrsa -aes256 -out client.key 4096

4 - GENERATE CSR
openssl req -new -key client.key -out client.csr -config /Users/user/openssl-smime.cnf -extensions usr_cert

5 - GENERATE CLIENT CERTIFICATE
openssl x509 -req -days 365 -in client.csr -CA CA.crt -CAkey CA.key -CAcreateserial -CAserial CA.seq -out client.crt -addtrust emailProtection -addreject clientAuth -addreject serverAuth -trustout -extfile /Users/user/openssl-smime.cnf -extensions usr_cert

6 - EXPORT CERTIFICATE (with private key! - not for distribution)
openssl pkcs12 -export -in client.crt -inkey client.key -out client.p12

---------------------------------------------------------------------------------------------------------------
