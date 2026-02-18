# Private PKI Lab

## Overview

This project implements a complete private Public Key Infrastructure (PKI) using OpenSSL. It demonstrates certificate authority hierarchy, certificate issuance, validation, and revocation, simulating enterprise internal PKI environments.

This lab demonstrates hands-on understanding of X.509 certificates, trust chains, and TLS authentication.

---

## Architecture

PKI hierarchy implemented:

Root CA (offline)  
└── Intermediate CA  
&nbsp;&nbsp;&nbsp;&nbsp;├── Server Certificates  
&nbsp;&nbsp;&nbsp;&nbsp;└── Client Certificates  

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

openssl verify -CAfile chain.pem server-cert.pem

View certificate details:

openssl x509 -in server-cert.pem -text -noout

---

## Learning Objectives

This project demonstrates understanding of:

• Certificate Authority hierarchy  
• Certificate signing process  
• Trust chains  
• Certificate validation  
• Revocation mechanisms  

---

## Repository Structure

private-pki-lab/
├── root-ca/
├── intermediate-ca/
├── certs/
├── crl/
└── README.md

---

## Author

Stephanie Shinn  
IAM Consultant | PKI | Identity Security | Quantum-Safe Cryptography
