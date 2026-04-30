## Goal
Use campus VPN to access network resources without needing captive portal login.

---

## OpenVPN Setup

Installed OpenVPN:

```bash
sudo apt update
sudo apt install openvpn
```

---

## Config Files

Placed files in:

```text
/etc/sshvpn/
├── config.ovpn
├── creds.txt
```

Get your .ovpn file from the [VPN Portal](https://gateway.iisertvm.ac.in:8443/vpnportal/webpages/index.html#3352)


`creds.txt` format:

```text
username
password
```

---

## Running VPN Manually

```bash
sudo openvpn --config /etc/sshvpn/config.ovpn --auth-user-pass /etc/sshvpn/creds.txt
```

Observed:
- connects successfully
- blocks terminal (foreground process)

---

## systemd Service

Created service:

```bash
sudo nano /etc/systemd/system/sshvpn.service
```

Basic structure:

```ini
[Unit]
Description=OpenVPN SSH VPN
After=network.target

[Service]
ExecStart=/usr/sbin/openvpn --config /etc/sshvpn/config.ovpn --auth-user-pass /etc/sshvpn/creds.txt
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

---

## Enable and Start

```bash
sudo systemctl daemon-reload
sudo systemctl enable sshvpn
sudo systemctl start sshvpn
```

Check status:

```bash
sudo systemctl status sshvpn
```

---

## Wrapper Command

Created script:

```bash
sudo nano /usr/local/bin/sshvpn
```

```bash
#!/bin/bash
CONFIG="/etc/sshvpn/config.ovpn"

get_gateway() {
    ip route | grep default | awk '{print $3}' | head -n 1
}

case "$1" in
    start)
        echo "Starting VPN..."
        sudo systemctl start sshvpn
        echo "VPN started"
        ;;
        
    stop)
        sudo systemctl stop sshvpn
        echo "VPN stopped"
        ;;
        
    restart)
        echo "Restarting VPN..."
        sudo systemctl restart sshvpn
        echo "VPN restarted"
        ;;
        
    status)
        sudo systemctl status sshvpn
        ;;
        
    update)
        if [ -z "$2" ]; then
            echo "Usage: sshvpn update <path-to-ovpn>"
            exit 1
        fi
        sudo cp "$2" "$CONFIG"
        sudo chmod 600 "$CONFIG"
        echo "VPN config updated at $CONFIG"
        echo "Please restart your VPN"
        ;;
        
    showconfig)
        echo "Current config location:"
        echo "$CONFIG"
        ;;
        
    *)
        echo "Usage: $0 {start|stop|restart|status|update|showconfig}"
        ;;
        
esac
```

Basic usage:

```bash
sshvpn start
sshvpn stop
sshvpn restart
sshvpn status
```

Internally uses:

```bash
systemctl start sshvpn
systemctl stop sshvpn
```

Updating config file:

```bash
sshvpn update <path-to-ovpn>
sshvpn restart
```

Updating the config will copy the config file to /etc/sshvpn/ for more safety.

---

## Observations

- VPN connects successfully
- Internet works without captive portal login
- Traffic routed through VPN

---

## Issues

- SSH connection drops when VPN starts
- Routing changes break existing connections
- Difficult to manage remotely once VPN is active

---

## Notes

- systemd simplifies managing VPN
- credentials must be stored securely
- foundation for later networking experiments