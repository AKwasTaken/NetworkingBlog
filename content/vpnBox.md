## Goal

```text
qBittorrent → VPN only
everything else → normal network
```

---

## Problem

Using VPN normally:

- affects entire system
- breaks Tailscale and SSH access
- not suitable for selective usage

---

## Approach

Use Linux network namespaces to isolate traffic.

---

## Creating Namespace

```bash
sudo ip netns add vpnbox
```

---

## Virtual Interface

Created veth pair:

```bash
sudo ip link add veth-host type veth peer name veth-vpn
sudo ip link set veth-vpn netns vpnbox
```

---

## Assign IPs

```bash
sudo ip addr add 10.200.1.1/24 dev veth-host
sudo ip link set veth-host up

sudo ip netns exec vpnbox ip addr add 10.200.1.2/24 dev veth-vpn
sudo ip netns exec vpnbox ip link set veth-vpn up
```

---

## Enable Internet Access

```bash
sudo sysctl -w net.ipv4.ip_forward=1
sudo iptables -t nat -A POSTROUTING -o wlp3s0 -j MASQUERADE
```

---

## Default Route

```bash
sudo ip netns exec vpnbox ip route add default via 10.200.1.1
```

---

## Running VPN Inside Namespace

```bash
sudo ip netns exec vpnbox openvpn --config /etc/sshvpn/config.ovpn
```

---

## Running qBittorrent Inside Namespace

```bash
sudo ip netns exec vpnbox qbittorrent-nox
```

---

## Observations

- qBittorrent traffic goes through VPN
- system network remains unchanged
- Tailscale and SSH continue working normally

---

## Issues Faced

- no logs when running OpenVPN with daemon
- required manual debugging
- needed process management for start/stop

---

## Scripts

Created helper script:
```bash
sudo nano /usr/local/bin/vpnbox
```

The script:
```bash
#!/bin/bash

NS="vpnbox"
CONFIG="/etc/sshvpn/config.ovpn"
VPN_PID="/tmp/vpnbox_vpn.pid"
QB_PID="/tmp/vpnbox_qb.pid"

case "$1" in
    start)
        echo "Starting VPN inside $NS..."
        sudo ip netns exec $NS \
        openvpn --config "$CONFIG" \
        --daemon --writepid "$VPN_PID"
        sleep 3
        echo "VPN started"
        ;;
    stop)
        echo "Stopping VPN..."
        if [ -f "$VPN_PID" ]; then
            sudo kill $(cat "$VPN_PID") 2>/dev/null
            rm -f "$VPN_PID"
            echo "VPN stopped"
        else
            echo "VPN not running"
        fi
        ;;
    qb)
        echo "Starting qBittorrent (nox)..."
        sudo ip netns exec $NS \
        qbittorrent-nox --daemon \
        --profile=/tmp/qb-profile &
        echo $! > "$QB_PID"
        sleep 2
        echo "qBittorrent started at http://localhost:8080"
        ;;
    stopqb)
        echo "Stopping qBittorrent..."
        if [ -f "$QB_PID" ]; then
            sudo kill $(cat "$QB_PID") 2>/dev/null
            rm -f "$QB_PID"
            echo "qBittorrent stopped"
        else
            echo "qBittorrent not running"
        fi
        ;;
    status)
        sudo systemctl status vpnbox --no-pager -n 20
        ;;
    ip)
        echo "IP inside namespace:"
        sudo ip netns exec $NS ip a
        ;;
    route)
        echo "Routing inside namespace:"
        sudo ip netns exec $NS ip route
        ;;
    test)
        echo "Testing internet from vpnbox..."
        sudo ip netns exec $NS curl -s ifconfig.me
        echo ""
        ;;
    shell)
        echo "Entering vpnbox shell..."
        sudo ip netns exec $NS bash
        ;;
    logs)
        sudo journalctl -u vpnbox -f
        ;;
    *)
        echo "Usage:"
        echo "  vpnbox start      → start VPN"
        echo "  vpnbox stop       → stop VPN"
        echo "  vpnbox qb         → start qBittorrent"
        echo "  vpnbox stopqb     → stop qBittorrent"
        echo "  vpnbox status     → show status"
        echo "  vpnbox ip         → show IPs"
        echo "  vpnbox route      → show routes"
        echo "  vpnbox test       → test internet"
        echo "  vpnbox shell      → enter namespace"
        echo "  vpnbox logs       → show logs"
        ;;
esac
```


Usage commands:

```bash
vpnbox start      → start VPN
vpnbox stop       → stop VPN
vpnbox qb         → start qBittorrent
vpnbox stopqb     → stop qBittorrent
vpnbox status     → show status
vpnbox ip         → show IPs
vpnbox route      → show routes
vpnbox test       → test internet
vpnbox shell      → enter namespace
vpnbox logs       → show logs
```

Handles:
- starting VPN in namespace
- running qBittorrent
- checking status

We create a systemd helper file:

```bash
sudo nano /etc/systemd/system/vpnbox.service
```

And fill it with:

```bash
[Unit]
Description=OpenVPN inside vpnbox namespace
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/ip netns exec vpnbox /usr/sbin/openvpn --config /etc/sshvpn/config.ovpn
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

---

## Result

- isolated VPN usage
- no interference with main system networking
- stable setup for downloads

---

## Notes

- requires understanding of networking concepts
- more complex than standard VPN usage
- but provides full control over traffic