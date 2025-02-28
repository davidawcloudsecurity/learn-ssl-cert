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
Interesting way to generate key and csr using san
```
openssl req -out example.csr -newkey rsa:2048 -nodes -keyout example.key -config example.cnf
```
```
default_bits = 2048
distinguished_name = req_distinguished_name
req_extensions = req_ext
prompt = no
[ req_distinguished_name ]
countryName = SG
stateOrProvinceName = SG
localityName = SG
organizationName = SFA
commonName = example.gov
[ req_ext ]
subjectAltName = @alt_names
[alt_names]
DNS.1 = example.gov
```
