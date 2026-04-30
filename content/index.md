---
title: Timeline of my HomeServer Journey
---

This document is a chronological log of my journey setting up a personal home server on a Linux Mint laptop inside a campus network with heavy restrictions (Sophos firewall, captive portal, multiple subnets).

It documents what I tried, what failed, and what actually worked.

---

# Phase 0 -- Initial Setup

I started with a Linux Mint laptop that I wanted to turn into a “home server”.

Basic things I set up:
- SSH access from my Mac
- File structure for storing data
- Some basic shell scripting

*Details*: [[Initial Setup]]

---

# Phase 1 -- Minecraft Server

The first real use case was hosting a Minecraft server.

- Installed Java
- Set up a Minecraft server
- Experimented with mods

This was my first exposure to:
- running long-lived processes
- server configuration

*Details*: [[Minecraft Server]]

---

# Phase 2 -- Download Tools

To support media usage, I explored downloading tools:

- Tried `aria2c` (CLI-based)
- Faced issues with magnet links and usability
- Switched to qBittorrent

Learned:
- difference between CLI vs GUI tools
- how torrent clients work

*Details*: [[Download Tools]]

---

# Phase 3 -- Plex (Failed Attempt)

I tried setting up a media server using Plex.

What worked:
- local streaming

What failed:
- remote streaming outside network
- reliance on Plex relay / subscriptions

Conclusion:
- Plex is not ideal for restricted networks
- Huge headache.

*Details*: deprecated

---

# Phase 4 -- Jellyfin (Major Success)

Switched to Jellyfin.

- Set up media libraries (Films / TV)
- Fixed permission issues
- Successfully streamed locally

This became my main media server.

*Details*: [[Jellyfin Setup]]

---

# Phase 5 -- Networking Problems Begin

Ran into major issues due to campus network:

- Captive portal login required
- IP address changes frequently
- Multiple subnets → inconsistent connectivity

Started experimenting with:
- hostname-based access (`.local`)
- basic scripts to automate login

*Details*: [[Networking Basics]]

---

# Phase 6 -- SSH Tunneling

Tried accessing services remotely via SSH port forwarding:

```bash
ssh -L 8090:gateway.iisertvm.ac.in:8090 user@server
```

Goal:
- access login portal remotely

Partial success, but not scalable.

*Details*: [[SSH Tunnels]]

---

# Phase 7 -- VPN Setup (sshvpn)

Set up campus VPN using `.ovpn` file.

Built a wrapper command:

```bash
sshvpn start
sshvpn stop
```

Learned:
- systemd services
- OpenVPN configs
- credential management

*Details*: [[SSHVPN Setup]]

---

# Phase 8 -- Tailscale (Breakthrough)

Installed Tailscale for remote access.

- Got stable 100.x.x.x IP
- Could access server from anywhere

This solved:
- dynamic IP issues
- remote access without port forwarding

*Details*: [[Tailscale Breakthrough]]

---

# Phase 9 -- VPN vs Tailscale Conflict

Major issue:

```text
OpenVPN breaks Tailscale
```

Observed:
- routing conflicts
- traffic hijacking
- Sophos firewall interference

Tried:
- manual route overrides
- split tunneling

*Details*: [[VPN vs Tailscale]]]

---

# Phase 10 -- vpnbox (Per-App VPN)

Implemented advanced setup using network namespaces:

Goal:
```text
qBittorrent → VPN only
everything else → normal network
```

Steps:
- created network namespace
- ran OpenVPN inside it
- ran qBittorrent inside it

Result:
- clean separation of traffic
- no system-wide VPN interference

*Details*: [[vpnBox]]

---