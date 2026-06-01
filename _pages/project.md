---
title: "Projects"
layout: splash
classes:
  - page-project
permalink: /project/
header:
  overlay_image: /assets/img/header-bg.jpg
  overlay_filter: 0.6
  overlay_color: "#0a1628"
intro:
  - excerpt: "<p style='font-size:1.25em;font-style:italic;color:#555;margin:0;'>Things I built, broke, fixed, and learned from.</p>"
feature_row:
  - image_path: /assets/img/multisite-thumb.PNG
    alt: "Secure Multi-Site Network Design"
    title: "Secure Multi-Site Network Design"
    excerpt: "<span style='background:#E1F5EE;color:#0F6E56;font-size:11px;font-weight:600;padding:3px 10px;border-radius:99px;letter-spacing:0.03em;'>Cybersecurity</span><br><br>What does it actually take to secure three enterprise buildings under one network? I built the whole thing in a simulator — VPNs, firewalls, IDS — and found out.<br><br><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>GNS3</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>MikroTik</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Snort IDS</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;'>Wireshark</span>"
    url: "/p_securemultisite/"
    btn_label: "Read write-up →"
    btn_class: "btn--primary"

  - image_path: /assets/img/webrecovery-thumb.png
    alt: "Website Recovery"
    title: "Website Recovery"
    image_position: left center
    excerpt: "<span style='background:#E6F1FB;color:#185FA5;font-size:11px;font-weight:600;padding:3px 10px;border-radius:99px;letter-spacing:0.03em;'>Web / DevOps</span><br><br>\"Our website is down.\" No logs. No context. Just a blank screen and a deadline. Here's how I brought it back — and why the real problem had nothing to do with the code.<br><br><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Next.js 15</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Docker</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Traefik</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;'>WordPress</span>"
    url: "/p_webrecovery/"
    btn_label: "Read write-up →"
    btn_class: "btn--primary"

  - image_path: /assets/img/photocloud-thumb.PNG
    alt: "Local Photo Cloud"
    title: "Local Photo Cloud"
    excerpt: "<span style='background:#FAEEDA;color:#854F0B;font-size:11px;font-weight:600;padding:3px 10px;border-radius:99px;letter-spacing:0.03em;'>Homelab</span><br><br>Google Photos killed the free tier. My photos were sitting on someone else's server. So I built my own — fully private, fully open-source, running on hardware I own.<br><br><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Self-hosted</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;'>Open source</span>"
    url: "/p_localphotocloud-pilot/"
    btn_label: "Read write-up →"
    btn_class: "btn--primary"

  - image_path: /assets/img/botnet-portscan-topology.png
    alt: "Distributed Port Scanning with a Botnet"
    title: "Distributed Port Scanning with a Botnet"
    excerpt: "<span style='background:#FDEBEC;color:#9C1C2C;font-size:11px;font-weight:600;padding:3px 10px;border-radius:99px;letter-spacing:0.03em;'>Network Security</span><br><br>Can a firewall rate limit stop a full TCP scan? I built a C2-coordinated botnet in an isolated GNS3 lab to scan all 65,535 ports in under 60 seconds without blacklisting.<br><br><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>GNS3</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Python</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>MikroTik</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;'>Pen Testing</span>"
    url: "/p_botnet-portscan/"
    btn_label: "Read write-up →"
    btn_class: "btn--primary"

  - image_path: /assets/img/vis-home.PNG
    alt: "No Cloud, No Compromise: How I Built ViScriber for Secure Local Transcription"
    title: "ViScriber: Local Transcription for Privacy-Critical Work"
    excerpt: "<span style='background:#EAF2FF;color:#1E4E8C;font-size:11px;font-weight:600;padding:3px 10px;border-radius:99px;letter-spacing:0.03em;'>Privacy Engineering</span><br><br>Faced with sensitive interview recordings and strict data-handling constraints, I built ViScriber, a cross-platform desktop tool that transcribes audio and video fully offline using Whisper.<br><br><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Whisper</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Python</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>PyInstaller</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;'>Offline-first</span>"
    url: "/p_viscriber/"
    btn_label: "Read write-up →"
    btn_class: "btn--primary"

  - image_path: /assets/img/kidsync_banner.png
    alt: "KidSync: How a Wrong Uniform Turned Into a Weekend Project"
    title: "KidSync: Parent Reminder Automation"
    excerpt: "<span style='background:#EAF8EE;color:#1D6A3A;font-size:11px;font-weight:600;padding:3px 10px;border-radius:99px;letter-spacing:0.03em;'>Automation</span><br><br>Teacher reminders were getting buried in noisy parent chats, so I built KidSync: copy a message to Gmail, let AI extract events, and auto-populate a shared family calendar.<br><br><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Google Apps Script</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Gemini</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;margin-right:4px;'>Gmail</span><span style='background:#f5f5f2;color:#555;font-size:11px;padding:2px 8px;border-radius:99px;border:1px solid #e0e0da;font-family:monospace;'>Google Calendar</span>"
    url: "/p_kidsync/"
    btn_label: "Read write-up →"
    btn_class: "btn--primary"
---

{% include feature_row id="intro" type="center" %}
{% include feature_row %}
