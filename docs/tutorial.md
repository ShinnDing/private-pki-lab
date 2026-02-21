# Private PKI Lab — End-to-End Tutorial

This tutorial demonstrates how to build a complete private Public Key Infrastructure (PKI) using OpenSSL. It includes creating a Root Certificate Authority, Intermediate Certificate Authority, and issuing server and client certificates suitable for TLS and mutual TLS (mTLS).

This lab simulates enterprise PKI architecture and is safe to publish because private keys are never committed.

---

# Architecture Overview

```
Root CA (offline)
└── Intermediate CA
    ├── Server Certificate (serverAuth)
    └── Client Certificate (clientAuth)
```

Root CA signs Intermediate CA  
Intermediate CA signs server and client certificates  

---

# Security Principles

Private keys must never be committed to source control.

The following files are safe to publish:

```
*.cert.pem
*.csr.pem
*.cnf
```

The following must remain private:

```
*.key.pem
```

Use a `.gitignore` file to prevent committing private keys:

```
*.key.pem
*.key
```

---

# Project Structure

```
private-pki-lab/
├── root-ca/
│   ├── root-ca.cert.pem
│   └── intermediate-ca-ext.cnf
├── intermediate-ca/
│   ├── intermediate-ca.cert.pem
│   └── intermediate-ca.csr.pem
├── certs/
│   ├── server.cert.pem
│   ├── server.csr.pem
│   ├── server-ext.cnf
│   ├── client.cert.pem
│   ├── client.csr.pem
│   ├── client-ext.cnf
│   └── ca-chain.pem
└── docs/
    └── tutorial.md
```

---

# Step 1 — Create Project Structure

```
mkdir -p private-pki-lab/root-ca
mkdir -p private-pki-lab/intermediate-ca
mkdir -p private-pki-lab/certs
mkdir -p private-pki-lab/docs
```

---

# Step 2 — Create Root CA

## Generate Root CA private key (DO NOT COMMIT)

```
openssl genrsa -out root-ca/root-ca.key.pem 4096
chmod 400 root-ca/root-ca.key.pem
```

## Generate Root CA certificate (SAFE TO COMMIT)

```
openssl req -x509 -new -sha256 -days 3650 \
  -key root-ca/root-ca.key.pem \
  -out root-ca/root-ca.cert.pem
```

Example values:

```
Country: US
State: Minnesota
Organization: Shinn Private PKI
Organizational Unit: PKI
Common Name: Shinn Root CA
```

Secure certificate:

```
chmod 444 root-ca/root-ca.cert.pem
```

---

# Step 3 — Create Intermediate CA

## Generate Intermediate CA private key (DO NOT COMMIT)

```
openssl genrsa -out intermediate-ca/intermediate-ca.key.pem 4096
chmod 400 intermediate-ca/intermediate-ca.key.pem
```

## Generate CSR

```
openssl req -new -sha256 \
  -key intermediate-ca/intermediate-ca.key.pem \
  -out intermediate-ca/intermediate-ca.csr.pem
```

Common Name:

```
Shinn Intermediate CA
```

---

# Step 4 — Sign Intermediate CA with Root CA

Create extension file:

```
cat > root-ca/intermediate-ca-ext.cnf << EOF
basicConstraints=critical,CA:TRUE,pathlen:0
keyUsage=critical,keyCertSign,cRLSign
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid:always,issuer
EOF
```

Sign certificate:

```
openssl x509 -req -sha256 -days 1825 \
  -in intermediate-ca/intermediate-ca.csr.pem \
  -CA root-ca/root-ca.cert.pem \
  -CAkey root-ca/root-ca.key.pem \
  -CAcreateserial \
  -out intermediate-ca/intermediate-ca.cert.pem \
  -extfile root-ca/intermediate-ca-ext.cnf
```

Secure certificate:

```
chmod 444 intermediate-ca/intermediate-ca.cert.pem
```

---

# Step 5 — Create Server Certificate

## Generate server private key (DO NOT COMMIT)

```
openssl genrsa -out certs/server.key.pem 2048
chmod 400 certs/server.key.pem
```

## Generate CSR

```
openssl req -new -sha256 \
  -key certs/server.key.pem \
  -out certs/server.csr.pem
```

Common Name:

```
localhost
```

---

## Create server extension file

```
cat > certs/server-ext.cnf << EOF
basicConstraints=CA:FALSE
keyUsage=critical,digitalSignature,keyEncipherment
extendedKeyUsage=serverAuth
subjectAltName=DNS:localhost,IP:127.0.0.1
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer
EOF
```

---

## Sign server certificate

```
openssl x509 -req -sha256 -days 825 \
  -in certs/server.csr.pem \
  -CA intermediate-ca/intermediate-ca.cert.pem \
  -CAkey intermediate-ca/intermediate-ca.key.pem \
  -CAcreateserial \
  -out certs/server.cert.pem \
  -extfile certs/server-ext.cnf
```

Secure certificate:

```
chmod 444 certs/server.cert.pem
```

---

# Step 6 — Create Client Certificate

## Generate client private key (DO NOT COMMIT)

```
openssl genrsa -out certs/client.key.pem 2048
chmod 400 certs/client.key.pem
```

## Generate CSR

```
openssl req -new -sha256 \
  -key certs/client.key.pem \
  -out certs/client.csr.pem
```

Common Name:

```
demo-client
```

---

## Create client extension file

```
cat > certs/client-ext.cnf << EOF
basicConstraints=CA:FALSE
keyUsage=critical,digitalSignature
extendedKeyUsage=clientAuth
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer
EOF
```

---

## Sign client certificate

```
openssl x509 -req -sha256 -days 825 \
  -in certs/client.csr.pem \
  -CA intermediate-ca/intermediate-ca.cert.pem \
  -CAkey intermediate-ca/intermediate-ca.key.pem \
  -CAcreateserial \
  -out certs/client.cert.pem \
  -extfile certs/client-ext.cnf
```

Secure certificate:

```
chmod 444 certs/client.cert.pem
```

---

# Step 7 — Create CA Chain Bundle

```
cat intermediate-ca/intermediate-ca.cert.pem root-ca/root-ca.cert.pem > certs/ca-chain.pem
chmod 444 certs/ca-chain.pem
```

---

# Step 8 — Verify Certificates

Verify server certificate:

```
openssl verify \
  -CAfile root-ca/root-ca.cert.pem \
  -untrusted intermediate-ca/intermediate-ca.cert.pem \
  certs/server.cert.pem
```

Verify client certificate:

```
openssl verify \
  -CAfile root-ca/root-ca.cert.pem \
  -untrusted intermediate-ca/intermediate-ca.cert.pem \
  certs/client.cert.pem
```

Expected output:

```
OK
```

---

# Result

You now have:

• Root Certificate Authority  
• Intermediate Certificate Authority  
• Server Certificate  
• Client Certificate  
• Valid trust chain  

This PKI can be used for:

• TLS servers  
• Mutual TLS authentication  
• Identity infrastructure testing  
• Security lab environments  

---

# Next Steps

Use these certificates with:

• Mutual TLS secured APIs  
• Identity providers  
• TLS servers  
• Zero Trust environments  

---

# License

MIT License