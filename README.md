# learn-ssl-cert
Here's a step-by-step guide to create a self-signed certificate for `subdomain.example.com`, including a Certificate Authority (CA), Certificate Signing Request (CSR), and SAN (Subject Alternative Name):

---

### 1. **Create a Certificate Authority (CA)**
#### Generate CA Private Key:
```bash
openssl genrsa -out ca.key 2048
```

#### Create Self-Signed CA Certificate:
```bash
openssl req -x509 -new -key ca.key -days 365 -out ca.crt -subj "/CN=My Local CA"
```

---

### 2. **Create Server Certificate (for `subdomain.example.com`)**
#### Generate Server Private Key:
```bash
openssl genrsa -out server.key 2048
```

#### Create a CSR Configuration File (`server.cnf`):
```ini
[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req
prompt = no

[req_distinguished_name]
CN = subdomain.example.com

[v3_req]
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1 = subdomain.example.com
```

#### Generate the CSR with SAN:
```bash
openssl req -new -key server.key -out server.csr -config server.cnf
```

---

### 3. **Sign the CSR with the CA**
```bash
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 365 -sha256 -extfile server.cnf -extensions v3_req
```

---

### 4. **Verify the Certificate**
Check the generated certificate details:
```bash
openssl x509 -in server.crt -text -noout
```
Ensure `subdomain.example.com` appears under **Subject Alternative Name**.

---

### Final Files:
- **CA Files**: `ca.key` (CA private key) and `ca.crt` (CA certificate).
- **Server Files**: `server.key` (private key), `server.csr` (CSR), and `server.crt` (signed certificate).

---

### Usage Notes:
- Import `ca.crt` into your system/browser trust store to avoid "untrusted certificate" warnings.
- Use `server.key` and `server.crt` on your web server (e.g., Apache/Nginx).
- Self-signed certificates are for testing/internal use only. Production environments require certificates from trusted CAs (e.g., Let’s Encrypt).

How to ensure the private key produce the cert using hash
```bash
key=c:\temp\your_private_key.pem
cert=c:\temp\your_certificate.crt
openssl rsa -noout -modulus -in $key | openssl md5
openssl x509 -noout -modulus -in $cert | openssl md5
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
