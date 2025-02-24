# learn-ssl-cert
How to ensure the private key produce the cert using hash
```bash
openssl rsa -noout -modulus -in your_private_key.pem | openssl md5
openssl x509 -noout -modulus -in your_certificate.crt | openssl md5
```
How to check for san in x509
```
openssl x509 -in certificate.pem -noout -text | grep -A1 "Subject Alternative Name"
```
