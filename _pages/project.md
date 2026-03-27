---
title: "Projects"
layout: splash
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
---

{% include feature_row id="intro" type="center" %}
{% include feature_row %}
