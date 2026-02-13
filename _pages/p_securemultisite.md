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
- Hardened by:
    - Restricting inbound access to VPN IP ranges
    - Firewall isolation