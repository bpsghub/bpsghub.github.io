---
layout: single
title: "Secure Multi-Site Network Design & Security Implementation"
permalink: /p_securemultisite/
author_profile: true
toc: true
toc_label: "Secure Multi-Site Network"
---

## Overview
In this project, I demonstrate my ability to design, implement, secure, and analyse an enterprise‑grade network environment. The environment simulates three enterprise buildings connected through secure routing, VPNs, firewalls, and intrusion detection systems.

All infrastructure was built and tested inside GNS3, using MikroTik firewalls and multiple Ubuntu‑based servers.

This project showcases my capability to work end‑to‑end across network design, security engineering, threat detection, encryption, and incident response.

> Tools: GNS3, Mikrotik firewall, VPN, SnortIDS, Wireshark

## Topology
I designed a full mesh network interconnecting the three sites, each with:

- A perimeter firewall/router (MikroTik RouterOS)
- A dedicated client LAN
- Unique subnets
- Connectivity to ISP‑level external nodes (External‑Client and External‑Attacker)

{% include figure image_path="/assets/img/topology.png" caption="Network Topology"%}

### What is in the topology
1. DNS Server (Internal)
- Implemented as a forwarding DNS server (→ Google DNS)
- Only resolvable by internal clients and VPN users
- Hosted on an internal subnet for added protection

2. Certificate Authority (Internal)
- Built using OpenSSL
- Generated:
    - Act as a root CA
    - Signed certificates for WEB and SMTP servers
- Enabled internal PKI trust across the network 

3. Web Server
- HTTPS enabled
- Hosted custom webpage
- Configured with TLS using certificates issued by internal Certificate Authority

4. SMTP Server
- Public-facing server
- Enforced encryption for:
    - Client-to-server (STARTTLS)
    - Server-to-server (smtp_tls_security_level = encrypt)

5. SSH Server (Internal-only via Remote VPN)
- Accessible only through Remote Access VPN
- Using OpenSSH
- Hardened by:
    - Restricting inbound access to VPN IP ranges
    - Firewall isolation

## Security Features

### 1. Email Security
There are 3 parts of email communication that need to be secured:
a. Communication between SMTP servers
b. Communication between SMTP server and client
c. End-to-end email encryption

Let's go through them one by one:
#### 1a. Encrypt communication between SMTP servers
***Why?***  
When sending and receiving emails, mail servers communicate between each other using SMTP protocol. However, the original SMTP protocol transmits data in plain text without encryption. This means any intermediate network devices between source and destination servers can potentially intercept and read the message content.

***So...***  
I need to enforce encryption in communication between SMTP servers by setting the config in the mail server as follow:  
in */etc/postfix/main.cf/*  
- set smtpd_tls_security_level = encrypt  
- set smtp_tls_security_level = encrypt
{% include figure image_path="/assets/img/smtp_encrypt.png" caption="Encrypt SMTP"%}


#### 1b. Secure communication between SMTP server and client
***Why?***
Without encryption, email credentials (username/password) and message content are transmitted in plain text when clients connect to the mail server. This exposes sensitive authentication data and email content to potential eavesdropping on the network path between the client and server.

***So...***
Common protection would be using TLS encryption explicitly (STARTTLS, port 587) or implicitly (SMTPS, port 465). In mail server, set the configuration in */etc/postfix/master.cf* : 

```
submission inet n - y - - smtpd
-o syslog_name=postfix/submission
-o smtpd_tls_security_level=encrypt

smtps inet n - y - - smtpd
-o syslog_name=postfix/smtps
-o smtpd_tls_wrappermode=yes
-o smtpd_tls_security_level=encrypt
```

{% include figure image_path="/assets/img/smtp_tls.png" caption="TLS over SMTP"%}

#### 1c. End-to-End Email Encryption
***Why?***   
While TLS secures the transmission channels, the mail server and any intermediate servers can still read the email content. If an attacker compromises the mail server or gains administrative access, they can read all stored emails. End-to-end encryption ensures that only the intended recipient can decrypt and read the message.

***So...***   
Implement PGP/GPG (Pretty Good Privacy/GNU Privacy Guard) for end-to-end encryption:

1. **Key Generation**: Each user generates a public/private key pair
   - Private key stays with the user (never shared)
   - Public key is distributed/published

2. **Encryption Process**:
   - Sender encrypts the email using recipient's public key
   - Email remains encrypted during transmission and storage
   - Only the recipient's private key can decrypt it

3. **Implementation**:
   - Users utilize email clients that support PGP (e.g., Thunderbird with Enigmail, GPG Suite for macOS)
   - Alternatively, use S/MIME certificates from your internal CA for similar functionality

This ensures that even if the mail server is compromised, the attacker cannot read the email contents without the recipient's private key.