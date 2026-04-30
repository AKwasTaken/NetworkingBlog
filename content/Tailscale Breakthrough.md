## Goal
Access the server remotely from any network without relying on local IP or SSH tunnels.

---

## Setup

Installed Tailscale:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

Started:

```bash
sudo tailscale up
```

Logged in through browser.

---

## Common Commands

Check status:

```bash
tailscale status
```

Get IP:

```bash
tailscale ip -4
```

Bring interface up:

```bash
sudo tailscale up
```

Bring interface down:

```bash
sudo tailscale down
```

Logout:

```bash
sudo tailscale logout
```

Ping another device:

```bash
tailscale ping <tailscale-ip>
```

---

## Initial Behavior

- Worked correctly on mobile hotspot
- Could access server using:

```
100.x.x.x
```

- SSH and services worked

---

## Issue on Campus WiFi

When connected to campus WiFi:

- Tailscale failed to connect
- Showed:
  - logged out
  - unable to reach control server
  - connection reset errors

Observed:

- Could not reach Tailscale servers
- Even `tailscale up` failed to authenticate

---

## Observation

Campus network uses firewall (Sophos).

Behavior:

- Blocks Tailscale control traffic
- Prevents connection to coordination servers
- Breaks login and synchronization

---

## Workaround

Connected server to mobile hotspot:

```
Hotspot → tailscale up → login works
```

Then switched back to campus WiFi.

---

## Interaction with VPN

After enabling campus VPN:

- Tailscale started working again

Observation:

- VPN routes traffic differently
- Tailscale traffic goes through VPN tunnel
- Bypasses campus firewall restrictions

---

## Key Behavior

```
Campus WiFi (no VPN) → Tailscale blocked
Campus WiFi + VPN → Tailscale works
Mobile hotspot → Tailscale works
```

---

## Notes

- Tailscale depends on external coordination servers
- Campus firewall interferes with direct access
- VPN indirectly fixes this by tunneling traffic