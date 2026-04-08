---
layout: single
title: "Breaking the Speed Limit — Distributed Port Scanning with a Botnet"
excerpt: "How I built a centrally orchestrated mini botnet in an isolated GNS3 lab to bypass per-IP scan throttling and complete a full TCP scan in under 60 seconds."
author_profile: true
permalink: /p_botnet-portscan/
toc: true
toc_label: "Table of Contents"
toc_icon: "network-wired"
toc_sticky: true
tags:
  - gns3
  - python
  - mikrotik
  - network-security
  - penetration-testing
  - project
categories:
  - Projects
header:
  teaser: /assets/img/botnet-portscan-teaser.png
---

> **Context:** This project was completed as part of a graduate-level cybersecurity course. All activities were performed inside a fully isolated GNS3 simulation environment. No real systems, networks, or devices were targeted at any point.

---

## The Challenge

Imagine you are a penetration tester hired to assess a server's security. Standard practice? Run a port scan — probe every door, see which ones are open.

Simple enough. Until the defender gets smart.

The target organization had caught on to port scanning activity and responded by deploying a **per-source IP rate limit of 100 new connections per second** on their perimeter firewall. The result? Scanning all 65,535 TCP ports from a single machine now takes **over 10 minutes**.

In a real engagement, that's a problem. The longer you're active on a network, the more likely you are to be detected, logged, and kicked out. We were given a hard constraint:

> **Scan all 65,535 TCP ports — under 60 seconds — while evading the firewall's rate-limiting rule.**

And there was a twist: **the attack had to be executed from only one machine.** You couldn't just fire up five scanners simultaneously. Everything had to be *centrally orchestrated*.

That's where things got interesting.

---

## Understanding the Wall

Before looking for a way around it, I needed to understand exactly what the firewall was doing.

The rate-limiting rule was straightforward but effective: **any single source IP was capped at 100 new connections per second**. Exceed that, and your IP gets blacklisted — frozen out for 2 full minutes. Try to brute-force through it, and you lose your window entirely.

A back-of-the-napkin calculation told the story quickly:

| Scenario | Connections/sec | Time for 65,535 ports |
|---|---|---|
| Single scanner, no limit | Unlimited | ~seconds |
| Single scanner, rate-limited | 100/sec | **~11 minutes** |
| **Distributed (4 bots)** | **360/sec combined** | **~46 seconds ✅** |

The firewall's blind spot was hiding in plain sight: the rule was **per source IP**. It had no visibility into *coordinated* traffic coming from *multiple* sources. That's the crack I needed to work with.

---

## The Architecture — A Mini Botnet

The solution was to build a small, coordinated **botnet** — a network of machines that each do a portion of the scanning work, all directed by a single **Command & Control (C2) node**.

Here's how I structured it inside GNS3:

<!-- PLACEHOLDER: Insert topology diagram showing C2 node, 4 bot nodes, MikroTik firewall, and victim server -->
![Topology Diagram](/assets/img/botnet-portscan-topology.png)
*Network topology: C2 center, 4 bot nodes, MikroTik firewall, and victim server*

### The Players

**C2 Node (External Attacker 1)**
This is the brain. It holds the attack parameters, distributes instructions to the bots, and collects results when the scan is done. Crucially, *this is the only machine the operator interacts with directly* — satisfying the "single point of execution" requirement.

**Bot Nodes (4 machines)**
Each bot runs a lightweight **Python-based agent**. They don't receive direct commands — instead, they use a **pull model**: each bot periodically polls the C2 over HTTP, asking *"do you have instructions for me?"*

This is a classic design choice in real-world botnets. Pull-based communication is harder to detect and block than push-based (where the C2 reaches out to bots directly), because the bots look like regular web clients making outbound requests.

### The State Machine

Each bot agent operated in two states:

```
[ SLEEPING ] ──── receives command payload ────► [ EXECUTING ]
     ▲                                                  │
     └──────────── scan complete, report back ──────────┘
```
*Pull-based C2 communication: bots poll for instructions, execute, then report back*

- **Sleeping:** Idle. Bot quietly polls the C2 at regular intervals. No scanning, no noise.
- **Executing:** A valid instruction payload was received. Bot begins scanning its assigned port range segment and reports results back to C2 when done.



---

## Configuration — The Numbers That Matter

Before launching, I configured the C2 server with the exact parameters needed to stay under the radar:

| Parameter | Value | Reasoning |
|---|---|---|
| Target | Victim Server (`200.2.4.11`) | Isolated lab target |
| Port Range | 0 – 65,535 | Full TCP range required |
| Bot Count | 4 | Divides the workload into 4 parallel segments |
| Connections per Batch | **90** | 10% below the 100-limit — safety margin for jitter |
| Combined Throughput | **360 connections/sec** | 3.6× what any single IP is allowed |

The 90-connection-per-batch setting was a deliberate choice. Hitting exactly 100 risks edge cases — network jitter, slight timing drift — pushing a bot fractionally over the threshold. Running at 90 gives a comfortable buffer while still achieving the goal.

---

## Execution — How It Played Out

The sequence was methodical:

1. **Bot initialization** — Python agents were started on all four bot nodes. Each entered its *Sleeping* state and began polling the C2.
2. **C2 activation** — The C2 server was initialized with the attack parameters, generating the instruction payload.
3. **Instruction pickup** — On their next poll, each bot fetched the payload and immediately transitioned to *Executing*.
4. **Parallel scan** — All four bots launched simultaneously, each covering a different segment of the port range. From the firewall's perspective: four separate, well-behaved clients making normal-looking connection attempts.
5. **Result aggregation** — Each bot reported its findings back to C2. The C2 stitched the partial results into a single, complete port map.


---

## Results

The numbers came in clean:

- ✅ **All 10 expected open ports discovered** — 100% accuracy
- ✅ **Completed in ~46 seconds** — under the 60-second requirement
- ✅ **No bot was blacklisted** — every node stayed under the 100 connections/sec threshold throughout
- ✅ **Centrally orchestrated** — the entire operation was initiated and managed from one C2 node

<!-- PLACEHOLDER: Insert screenshot of final aggregated port scan results on C2 console -->
![Scan Results](/assets/img/botnet-portscan-results.png)
*Aggregated results on C2 console: all open ports accurately reported*

The firewall never saw the full picture. Each individual bot looked perfectly compliant. The rate-limiting rule — designed to stop a single aggressive scanner — had no answer for four coordinated, well-behaved ones.

---

## What This Taught Me

This project wasn't just about getting a scan to run faster. A few things stuck with me:

**1. Firewall rules have context boundaries.**
A rule that says "no more than 100 connections per IP" is only as strong as the assumption behind it — that threats come from one source. Distributed attacks shatter that assumption without ever technically breaking the rule.

**2. Pull-based C2 is surprisingly stealthy.**
The bots generated no unusual inbound traffic. To a network monitor, they looked like clients browsing a web server. This is a pattern defenders need to actively watch for — not just inbound attack signatures.

**3. The orchestration constraint was the real exam.**
The task specifically required central execution for full marks. Building the C2 layer was what separated a brute-force workaround from an actual architecture. That design thinking — *how do I stay in control of a distributed operation?* — is exactly what both red teamers and blue teamers need to understand.

**4. Margins matter.**
Running at 90% of the limit instead of 100% was a small tweak with real consequences. In a real engagement, the difference between "stealth" and "blacklisted" can be a few connections per second.

---

> **Tools used:** GNS3 · MikroTik RouterOS · Python · Custom HTTP-based C2 · TCP port scanner
