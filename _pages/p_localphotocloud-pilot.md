---
layout: single
title: "Ditching Google Photos: Building My Own Photo Cloud on a Windows Laptop"
excerpt: "A step-by-step account of how I set up a self-hosted photo server using Immich, Docker, and Tailscale — on a Windows laptop, with no spare hardware needed."
author_profile: true
permalink: /p_localphotocloud-pilot/
toc: true
toc_label: "Table of Contents"
toc_icon: "camera"
toc_sticky: true
tags:
  - self-hosting
  - homelab
  - docker
  - cybersecurity
  - project
categories:
  - Projects
---

I finally got fed up with Google Photos. The free tier is long gone, storage keeps filling up, and frankly — my personal photos sitting on someone else's server has always bothered me. So I decided to build my own private photo cloud from scratch, entirely on open-source software, hosted on hardware I own.

The catch? I don't have a spare Raspberry Pi or a dedicated server sitting around right now. What I *do* have is a Windows laptop. So this is the story of how I turned it into a working photo server — and what I learned along the way.

---

## What I Wanted to Build

The goal was simple on paper: a system that works just like Google Photos, except everything lives on my own hardware at home. That means:

- Auto-backup from my phone the moment I take a photo
- Ability to browse and search my entire library from anywhere
- Secure access — nobody gets in without my credentials
- 100% free and open-source software

After some research, I landed on a stack that ticks all those boxes.

---

## The Stack

**[Immich](https://immich.app/)** is the heart of the setup. It's the closest open-source equivalent to Google Photos I've found — it has a beautiful mobile app for iOS and Android that auto-backs up your camera roll in the background, face recognition, a map view, albums, and a clean web interface. It runs entirely in Docker containers, which makes setup surprisingly straightforward.

**[Docker Desktop](https://www.docker.com/products/docker-desktop/)** runs all the Immich containers on Windows. Under the hood it uses WSL2 (Windows Subsystem for Linux) — essentially a real Linux kernel built into Windows — so Immich thinks it's running on Linux. You never have to touch WSL2 directly.

**[Tailscale](https://tailscale.com/)** handles secure remote access. It's a zero-config VPN that lets my phone and laptop form a private network. When I'm out and want to browse my photos, my phone connects to Immich over Tailscale — the server is never exposed to the public internet.

Everything is free. Everything is open-source (Tailscale's client is open-source; the coordination server is free for personal use).

---

## How It All Fits Together

Here's the architecture at a glance:

```
📱 Phone (Immich app)  💻 Browser (anywhere)
          ↕ encrypted Tailscale VPN
     🔒 Tailscale private network
          ↕
     🖥️ Windows Laptop
     └─ Docker Desktop (WSL2 backend)
        └─ Immich server + PostgreSQL + Redis
           └─ 💾 External HDD (your actual photos)
```

Photos never leave your hardware. Tailscale only carries encrypted traffic between your own devices.

---

## Prerequisites

Before starting, make sure you have:

- Windows 10 (version 2004 or later) or Windows 11
- At least 8 GB RAM (16 GB recommended)
- Virtualisation enabled in BIOS (it usually is by default)
- An external drive or a folder with enough space for your photo library

---

## Step 1: Enable WSL2

Docker Desktop needs WSL2 to run Linux containers on Windows. Open **PowerShell as Administrator** and run:

```powershell
wsl --install
```

Restart your computer when prompted. Then make sure WSL2 is set as the default:

```powershell
wsl --set-default-version 2
```

Verify that virtualisation is active:

```powershell
systeminfo | findstr "Hyper-V Requirements"
# Should say: "A hypervisor has been detected"
```

---

## Step 2: Install Docker Desktop

Download Docker Desktop from [docker.com](https://www.docker.com/products/docker-desktop/) and run the installer with default settings. It will automatically configure WSL2 as its backend.

After installation, open Docker Desktop → ⚙️ **Settings** and configure:

- **General** → Enable *Start Docker Desktop when you log in*
- **WSL Integration** → Enable for your Ubuntu distro

> **Note:** The Resources (CPU/Memory) sliders will be greyed out. That's expected — when using the WSL2 backend, Docker Desktop can't control resources directly. You configure them via a `.wslconfig` file instead (see the next step).

### Configure WSL2 resource limits

Open PowerShell and create the config file:

```powershell
notepad "$env:USERPROFILE\.wslconfig"
```

> This file doesn't exist by default. Notepad will ask *"do you want to create a new file?"* — click **Yes**.

Paste in the following, adjusting values to your laptop's RAM:

```ini
[wsl2]
memory=4GB
processors=2
swap=2GB
localhostForwarding=true
```

A good rule of thumb: set `memory` to half your total RAM. The `localhostForwarding=true` line is important — without it, `localhost` won't forward correctly to WSL2 (more on this later).

Save the file, then restart WSL2:

```powershell
wsl --shutdown
```

Reopen Docker Desktop from the taskbar and wait for it to go green.

### Verify Docker is working

```powershell
docker --version
docker compose version
```

Both should print version numbers. If they do, you're ready.

---

## Step 3: Set Up Immich

Create a folder for the Immich config files:

```powershell
mkdir C:\immich
cd C:\immich
```

Download the official Docker Compose file and environment template:

```powershell
Invoke-WebRequest -Uri "https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml" -OutFile "docker-compose.yml"

Invoke-WebRequest -Uri "https://github.com/immich-app/immich/releases/latest/download/example.env" -OutFile ".env"
```

Open the `.env` file and set your storage paths:

```powershell
notepad .env
```

Find and update these two lines:

```ini
UPLOAD_LOCATION=C:\Photos\immich-library
DB_DATA_LOCATION=C:\Photos\immich-db
```

Point these to wherever you want to store your photos. If you're using an external drive (e.g. `D:\` or `H:\`), use that path — just make sure the drive is always plugged in before Docker starts.

---

## Step 4: Open Port 2283 in Windows Firewall

This one tripped me up for a while. Windows Firewall silently blocks WSL2 ports by default — there's no warning, the browser just says the page isn't working. You need to explicitly allow it.

Open **PowerShell as Administrator** (right-click → Run as administrator) and run:

```powershell
New-NetFirewallRule -DisplayName "Immich" -Direction Inbound -Protocol TCP -LocalPort 2283 -Action Allow -Profile Any
```

The `-Profile Any` flag is important — it covers all network profiles including Tailscale's virtual adapter. Without it, you'll fix localhost access but Tailscale access will still fail.

> **Admin required:** This command will fail with *"Access is denied"* if you run it in a regular PowerShell window.

---

## Step 5: Start Immich

```powershell
cd C:\immich
docker compose up -d
```

The first launch downloads all the container images (around 2–3 GB), so give it a few minutes. Subsequent starts are near-instant.

Once it's up, open your browser:

```
http://localhost:2283
```

You should see the Immich setup screen. Create your admin account — use a strong, unique password and store it in a password manager.

> **One important thing:** create a separate regular user account for day-to-day use. Go to **Administration → Users → Create user**. Use the admin account only for server management, not for your personal photo library.

---

## Step 6: Set Up Remote Access with Tailscale

With Immich running locally, the next challenge is accessing it from outside the house. I went with Tailscale because it installs like a normal Windows app, needs no domain name, and just works.

**On your laptop:**

Download and install Tailscale from [tailscale.com](https://tailscale.com/download/windows). Sign in with a free account. Once connected, click the Tailscale tray icon to find your Tailscale IP — it'll look like `100.x.x.x`.

**On your phone:**

Install the Tailscale app (iOS or Android) and sign in with the same account.

That's it. Both devices are now on the same private network. From your phone (or any device with Tailscale running), you can reach Immich at:

```
http://100.x.x.x:2283
```

---

## Step 7: Mobile Auto-Backup

This is the part that makes it feel like Google Photos. Install the free **Immich app** from the App Store or Google Play. When you open it for the first time, enter your server URL — the Tailscale IP address from the step above.

Log in with your regular user account (not admin), then go to **Profile → Backup → Enable background backup**. Set it to WiFi only to avoid using mobile data.

From this point, every photo you take is automatically uploaded to your home server in the background. The first run will take a while if you have a large existing camera roll — just leave the phone on WiFi overnight and let it catch up.

---

## Step 8: Keep the Laptop Running

Your server is only online while the laptop is awake. To keep it running reliably:

**Disable sleep when plugged in:**

Go to **Settings → System → Power & Sleep** (Windows 10) or **Settings → System → Power** (Windows 11). Under *"When plugged in, put my PC to sleep after"* — set to **Never**.

**Disable lid-close sleep** (if you want to close the lid):

Search *"Choose what closing the lid does"* in the Start menu. Set *"When I close the lid → Plugged in"* to **Do nothing**.

**Enable Docker Desktop auto-start:**

This is already set in Docker Desktop settings. It means after any reboot, Immich comes back up automatically without you needing to do anything.

---

## What I Learned: The Honest Limitations

Running this on a laptop works — but it's a pilot, not a permanent solution. Here's what you're signing up for:

| Limitation | What it means in practice |
|---|---|
| Laptop must be on & awake | Immich goes offline the moment it sleeps or shuts down |
| External drive must be connected at startup | Missing drive = all containers fail to start |
| Drive disconnection breaks Docker | Stale WSL2 mounts require a `wsl --shutdown` + restart cycle |
| No built-in 2FA in Immich | Strong password + Tailscale VPN is your security model |
| Shared resources | Face detection jobs compete with your normal laptop usage |
| Windows Updates | Reboots take Immich offline until everything restarts |
| No redundancy | Single drive failure = data loss — set up offsite backup ASAP |

Most of these go away when you move to dedicated always-on hardware (a Raspberry Pi 5 or a mini PC running Linux). The beauty of this setup is that migration is simple — just copy two folders to the new machine and you're done.

---

## Troubleshooting: The Gotchas I Hit

I ran into several issues during setup that aren't well-documented anywhere. Here's what happened and how I fixed each one.

### localhost:2283 shows "page isn't working"

**Symptom:** Browser says `ERR_EMPTY_RESPONSE` or `This page isn't working`.

**Cause:** Windows Firewall blocking WSL2 ports, and/or `localhostForwarding` not set in `.wslconfig`.

**Fix:** Make sure your `.wslconfig` has `localhostForwarding=true`, add the firewall rule (Step 4), then do a full restart:

```powershell
wsl --shutdown
# Reopen Docker Desktop, wait for green icon, then:
cd C:\immich
docker compose down
docker compose up -d
```

### Tailscale IP shows "server not reachable" in the mobile app

**Cause:** The firewall rule only covered the loopback adapter. Tailscale uses a separate virtual network adapter that Windows blocks separately.

**Fix:** The `-Profile Any` flag in the firewall rule (Step 4) covers this. If you already added the rule without it, add a new one:

```powershell
New-NetFirewallRule -DisplayName "Immich Tailscale" -Direction Inbound -Protocol TCP -LocalPort 2283 -Action Allow -Profile Any
```

### HTTP 500 error on the Immich login page

**Symptom:** Page loads but login fails with a 500 error and a stack trace.

**Cause:** `ENOTFOUND database` in the server logs — the Immich server container can't reach the PostgreSQL database container. The Docker internal network broke, usually after a WSL2 restart.

**Diagnose:**

```powershell
cd C:\immich
docker compose logs immich-server --tail=50
```

**Fix:** Tear down and recreate the Docker network:

```powershell
docker compose down --remove-orphans
docker compose up -d
```

Wait 60 seconds before opening the browser — the database needs time to initialise.

### Docker fails to start after the external drive disconnects

**Symptom:** Error message like `error while creating mount source path '/run/desktop/mnt/host/h/immich-db': mkdir ... file exists`.

**Cause:** WSL2 holds on to a stale mount for the drive letter. When you plug the drive back in, Docker can't recreate the mount because the old one is stuck.

**Fix:**

```powershell
# Clear the stale mount
wsl --shutdown

# Verify the drive is accessible
Test-Path "H:\immich-db"   # Should return True

# Restart Docker Desktop from the taskbar, then:
cd C:\immich
docker compose up -d
```

**Prevention:** Always run `docker compose down` before unplugging the drive. Never yank it while Immich is running.

### Docker resource sliders are greyed out

Not a bug. When using the WSL2 backend, Docker Desktop disables its own resource controls. Use the `.wslconfig` file instead (covered in Step 2).

### .wslconfig file not found

Also not a bug. The file doesn't exist by default — you have to create it. Run this in PowerShell and Notepad will prompt you to create it:

```powershell
notepad "$env:USERPROFILE\.wslconfig"
```

### New-NetFirewallRule returns "Access is denied"

You ran it in a regular PowerShell window. Close it, right-click PowerShell → **Run as administrator**, and try again.

### One phone uploads much slower than the other

Upload speed depends on WiFi band, phone hardware, and Android battery optimisation. On the slower phone, try:

- Settings → Battery → Battery optimisation → Immich → set to **Unrestricted**
- Settings → Apps → Immich → Data usage → enable **Background data**
- On Xiaomi/MIUI: Settings → Apps → Manage apps → Immich → Autostart → **Enable**
- Connect to 5GHz WiFi instead of 2.4GHz

The initial bulk upload of your full camera roll will always be slow regardless of device — let it run overnight and it'll catch up.

### Can't find the 2FA setting in Immich

Immich doesn't have built-in 2FA — this is a deliberate design decision by the team. They leave authentication to external identity providers.

For a private setup behind Tailscale, a strong unique password is sufficient — your server isn't reachable from the public internet at all, only from your Tailscale-connected devices. If you later expose Immich publicly via Cloudflare Tunnel, add **Cloudflare Access** in front of it — it's free and adds an email OTP gate before anyone reaches the Immich login page.

---

## The Result

After working through all of the above, the setup does exactly what I wanted. Photos taken on my phone appear in Immich automatically. I can browse my library from anywhere. Nothing leaves my hardware. The monthly cost went from a Google One subscription to basically zero (just the electricity to keep the laptop running).

Is a Windows laptop the ideal server? No — it's a pilot. The real goal is to prove the setup works before investing in dedicated hardware. When I eventually move this to a proper always-on device, migration is just copying two folders.

But it works, it's mine, and Google Photos can stay closed.

---

## What's Next

- Move to dedicated always-on hardware (Raspberry Pi 5 or mini PC)
- Set up offsite backup with Backblaze B2 + rclone
- Explore Cloudflare Access for a proper authentication layer
- Migrate existing Google Photos library using Google Takeout + Immich's built-in import tool
