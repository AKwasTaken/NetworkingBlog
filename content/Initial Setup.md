## Goal
Turn a Linux Mint laptop into a basic home server.

---

## What I did

- Installed Linux Mint on the laptop
- Set up user account and basic environment
- Connected to campus WiFi
- Verified internet access

---

## SSH Setup

This is basically for remote access-ing into my server-laptop from anywhere*.

Installed SSH server:

```bash
sudo apt update
sudo apt install openssh-server
```

Checked status:

```bash
sudo systemctl status ssh
```

Enabled SSH:

```bash
sudo systemctl enable ssh
```

---

## Finding IP Address

```bash
hostname -I
```

Used this IP to connect from Mac:

```bash
ssh my_username@<ip-address>
```

---

## Directory Structure

Created basic folders:

```bash
mkdir ~/Plex
mkdir ~/Jellyfin
mkdir ~/Movies
```

Later organized into:

```text
~/Jellyfin/
├── Films
└── TV
```

---

## Basic Script Setup

Created simple scripts:

```bash
nano script.sh
```

Made executable:

```bash
chmod +x script.sh
```

Moved scripts to global path:

```bash
sudo mv script.sh /usr/local/bin/script
```

Now usable as:

```bash
script
```

---

## Permissions

Checked folder permissions:

```bash
ls -ld ~/Jellyfin
```

Changed ownership when needed:

```bash
sudo chown -R akwastaken:akwastaken ~/Jellyfin
```

---

## Notes

- SSH worked only inside the same network initially
- IP changes after reconnecting to WiFi
- Needed repeated use of `hostname -I`
- Learned basic file permissions and script execution