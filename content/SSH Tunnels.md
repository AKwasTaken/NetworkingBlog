## Goal
Access the campus login portal and other services remotely through the server.

---

## Idea

Use SSH port forwarding to access services running on the server or within the campus network.

---

## Command Used

```bash
ssh -L 8090:gateway.iisertvm.ac.in:8090 akwastaken@<server-ip>
```

---

## What This Does

- Opens a local port (8090) on the client (Mac)
- Forwards traffic to:
  - `gateway.iisertvm.ac.in:8090` through the server
- Allows accessing the login portal via:

```text
http://localhost:8090/httpclient.html
```

---

## Observations

- Successfully opened the campus login portal remotely
- Could interact with the portal from the local machine
- Did not require direct access to server GUI

---

## Issues

- Port conflicts if 8090 already in use
- Tunnel needs to be re-established manually
- Requires active SSH session
- Not persistent

---

## Variations Tried

Background SSH:

```bash
ssh -f -N -L 8090:gateway.iisertvm.ac.in:8090 akwastaken@<server-ip>
```

Observed:
- runs without opening terminal session
- still fails if port already in use

---

## Limitations

- Only solves access to specific services
- Does not provide full network connectivity
- Not a long-term solution for remote access

---

## Notes

- Useful for quick access to internal portals
- Helped understand port forwarding
- Eventually replaced by VPN-based solutions