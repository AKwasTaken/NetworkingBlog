## Goal
Understand and work around limitations of campus network.

---

## Observations

- WiFi requires login through captive portal
- Internet stops working after session expires
- IP address changes after reconnect
- Devices may be on different subnets
- Same WiFi ≠ same network access

---

## Checking IP

Used:

```bash
hostname -I
```

Observed:
- IP changes frequently
- needed to re-check before connecting

---

## Captive Portal

Login page:

```text
https://gateway.iisertvm.ac.in:8090/httpclient.html
```

Behavior:
- must login manually in browser
- session expires after some time
- no internet without login

---

## Problem

Server is remote (no direct screen access):

- cannot open browser easily
- cannot login manually
- services stop working when session expires

---

## hostname.local

Tested:

```text
<hostname>.local
```

Worked sometimes.

Observations:
- depends on mDNS
- not reliable across subnets
- inconsistent behavior on campus network

---

## Basic Script Attempts

Tried automating login process:

- storing login URL
- attempting curl-based login
- experimenting with scripts

Issues:
- login tokens change dynamically
- cannot reuse same request
- automation unreliable

---

## Connectivity Issues

Observed:

- device A can’t always reach device B
- even on same WiFi
- depends on subnet assignment

---

## Key Problems Identified

- dynamic IP
- captive portal login requirement
- subnet isolation
- lack of persistent access method

---

## Notes

- local access works only temporarily
- need a stable way to reach server
- manual login is a bottleneck