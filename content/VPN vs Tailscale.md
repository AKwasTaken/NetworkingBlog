## Problem

OpenVPN and Tailscale do not work together reliably either.

```text
OpenVPN breaks Tailscale
```

---

## Observations

After starting VPN:

- existing SSH connections drop
- Tailscale becomes unreachable
- `tailscale status` shows offline or logged out

---

## Checking Routes

Used:

```bash
ip route
```

Observed:

```text
0.0.0.0/1 via <vpn-gateway> dev tun0
128.0.0.0/1 via <vpn-gateway> dev tun0
```

---

## Meaning

- VPN splits internet into two routes
- all traffic is forced through `tun0`
- local routing is overridden

---

## Effect on Tailscale

- Tailscale tries to reach external servers
- traffic gets redirected into VPN
- campus firewall interferes with routing

Result:
- Tailscale cannot connect properly
- peers become unreachable

---

## Attempt 1 — Manual Route Override

Added route:

```bash
sudo ip route add 100.64.0.0/10 via <local-gateway>
```

Goal:
- force Tailscale traffic outside VPN

Result:
- inconsistent behavior
- sometimes works, often breaks
- not stable

---

## Attempt 2 — More Overrides

Added additional routes:

```bash
sudo ip route add <tailscale-server-range> via <local-gateway>
```

Result:
- complex routing
- unpredictable failures
- difficult to debug

---

## Attempt 3 — Split Tunneling

Tried modifying VPN behavior to avoid full tunnel.

Result:
- campus network still blocks traffic
- Tailscale still unreliable

---

## Interaction with Firewall

Campus uses Sophos firewall.

Behavior:

- blocks direct external connections
- interferes with Tailscale traffic
- routing tricks do not bypass restrictions reliably

---

## Key Insight
Trying to bypass VPN for Tailscale does not work reliably

---

## Working Understanding

Two stable modes:

```text
1. Tailscale without VPN → works only outside campus restrictions
2. Tailscale inside VPN → works because traffic is tunneled
```

---

## Notes

- routing changes affect all traffic
- manual overrides are fragile
- understanding `ip route` was essential