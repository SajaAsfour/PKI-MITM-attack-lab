# 🔐 Public Key Infrastructure (PKI) & Man-in-the-Middle Attacks

## 📌 Overview

This project explores how **Public Key Infrastructure (PKI)** protects secure communications and how weaknesses in the trust model can lead to **Man-in-the-Middle (MITM) attacks**.

The experiment demonstrates two scenarios:

1️⃣ **MITM attack attempt with a valid but mismatched certificate (Attack fails)**
2️⃣ **MITM attack using a compromised Certificate Authority (Attack succeeds)**

The lab shows how browsers validate certificates and how **trust in Certificate Authorities (CAs)** forms the backbone of HTTPS security. 

---

# 🎯 Learning Objectives

* Understand how **Public Key Infrastructure (PKI)** works
* Learn how **SSL/TLS certificates protect HTTPS connections**
* Simulate a **Man-in-the-Middle (MITM) attack**
* Understand **certificate domain verification**
* Demonstrate how **compromised Certificate Authorities break security**

---

# 🧪 Lab Environment

The experiment uses the **SEED Security Labs PKI environment** running inside Docker containers.

### Setup Steps

1. Download the lab files from SEED Labs.
2. Extract `Labsetup.zip`.
3. Rename the folder to your student name.
4. Build and run the environment using Docker.

```bash id="cmd1"
docker-compose build
docker-compose up
```

This launches the virtual network environment used for the PKI experiment. 

---

# ⚔️ Task 5 — Man-in-the-Middle Attack (Failed)

## Objective

Attempt to impersonate **[www.facebook.com](http://www.facebook.com)** using a certificate issued for a different domain.

The expectation is that the **browser will detect the mismatch and block the connection**.

---

## Attack Scenario

1. A victim attempts to visit:

```
https://www.facebook.com
```

2. The attacker intercepts the connection.
3. The attacker redirects traffic to a fake server.
4. The attacker presents a certificate for another domain.

However, browsers verify whether the certificate domain matches the requested website. 

---

## Step 1 — Create a Fake Website

A fake Facebook login page was created using HTML and CSS to simulate a phishing website.

Example structure:

```
facebook.html
```

This page mimics the Facebook login interface to trick users into entering credentials.

---

## Step 2 — Redirect Traffic (DNS Poisoning Simulation)

The victim machine’s **/etc/hosts** file was modified:

```
10.9.0.80   www.facebook.com
```

This causes requests intended for Facebook to be redirected to the attacker’s server.

---

## Step 3 — Attempt to Access Facebook

When navigating to:

```
https://www.facebook.com
```

Firefox displayed the error:

```
SSL_ERROR_BAD_CERT_DOMAIN
```

This occurs because the certificate presented by the attacker was issued for:

```
www.sajaAsfour2025.com
```

instead of:

```
www.facebook.com
```

---

## Why the Attack Failed

Browsers perform **domain verification** during TLS handshake.

They compare:

```
Certificate Common Name (CN)
```

with the website domain.

Because the domains did not match, the browser blocked the connection and warned the user. 

This demonstrates how **PKI prevents simple impersonation attacks**.

---

# ⚠️ Task 6 — MITM Attack with a Compromised CA (Successful)

## Objective

Demonstrate what happens when a **Certificate Authority is compromised**.

If attackers gain access to a CA’s private key, they can issue **valid certificates for any domain**.

This completely breaks the PKI trust model. 

---

## Step 1 — Create a Malicious Certificate Authority

A **self-signed CA certificate** was generated using OpenSSL.

Example command:

```bash id="cmd2"
openssl req -new -x509 -key ca.key -out ca.crt
```

This CA becomes the root of trust for certificates it signs.

---

## Step 2 — Generate a Certificate for Fake Facebook

A **Certificate Signing Request (CSR)** was generated for:

```
www.facebook.com
```

Then it was signed using the malicious CA.

Result:

```
server.crt
```

This certificate appears legitimate if the CA is trusted.

---

## Step 3 — Deploy the Fake HTTPS Website

The fake website was configured in **Apache** using the generated certificate:

```
server.crt
server.key
```

Apache was started inside the container to host the malicious website.

---

## Step 4 — Add the Malicious CA to Browser Trust Store

The attacker’s CA certificate was imported into **Firefox Trusted Authorities**.

Once trusted, the browser accepts any certificate signed by this CA.

---

## Result of the Attack

After visiting:

```
https://www.facebook.com
```

The browser displayed:

✔ Secure connection
✔ HTTPS padlock icon
✔ No security warning

However, the site was actually the **attacker’s fake server**.

This demonstrates that:

> HTTPS encryption alone does not guarantee website authenticity. 

---

# ⚠️ Security Implications

This experiment highlights a critical weakness:

If a **Certificate Authority is compromised**, attackers can:

* Issue valid certificates for any website
* Launch undetectable MITM attacks
* Steal user credentials and sensitive data

This is why modern systems rely on:

* **Certificate Transparency logs**
* **CA auditing**
* **Hardware security modules (HSMs)**
* **Multi-factor authentication**

---

# 📂 Project Structure

```
PKI-MITM-Attack-Lab
│
├── facebook.html
├── Asfour_1210737_TODO3
└── README.md
```

---

# 🔐 Security Concepts Demonstrated

* Public Key Infrastructure (PKI)
* SSL/TLS Certificates
* Certificate Authority Trust Model
* Man-in-the-Middle (MITM) Attacks
* DNS Poisoning
* HTTPS Security
* Certificate Verification

---

# 👩‍💻 Author

**Saja Asfour**

---

# 📚 References

* SEED Security Labs – PKI Experiment
* OpenSSL Documentation
* TLS/SSL Certificate Standards
* X.509 Certificate Architecture
