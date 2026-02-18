# Private PKI Lab

## Overview

This project implements a complete private Public Key Infrastructure (PKI) using OpenSSL. It demonstrates certificate authority hierarchy, certificate issuance, and certificate revocation, simulating enterprise internal PKI environments.

This lab was created to demonstrate hands-on understanding of X.509 certificates, trust chains, and TLS authentication.

---

## Architecture

PKI hierarchy implemented:

Root CA (offline)
   └── Intermediate CA
         ├── Server Certificates
         └── Client Certificates

---

## Features

• Root Certificate Authority creation  
• Intermediate Certificate Authority creation  
• Server certificate issuance  
• Client certificate issuance  
• Certificate chain validation  
• Certificate Revocation List (CRL) generation  
• Certificate verification  

---

## Technologies Used

OpenSSL  
X.509 Certificates  
TLS / SSL  
PKI Architecture  

---

## Example Use Cases

• Internal enterprise PKI  
• Service authentication  
• Mutual TLS authentication  
• Secure internal services  

---

## Example Commands

Verify certificate chain:

```bash
openssl verify -CAfile chain.pem server-cert.pem
