## Goal
Set up a media server that works locally without restrictions.

---

## Installation

Installed Jellyfin:

```bash
sudo apt update
sudo apt install jellyfin
```

Started service:

```bash
sudo systemctl start jellyfin
sudo systemctl enable jellyfin
```

---

## Accessing the Server

Opened in browser:

```text
http://<server-ip>:8096
```

Used:

```bash
hostname -I
```

to find IP.

---

## Initial Setup

- created user account
- selected media libraries
- skipped metadata options initially

---

## Folder Structure

Created folders:

```bash
mkdir -p ~/Jellyfin/Films
mkdir -p ~/Jellyfin/TV
```

Added these paths in Jellyfin setup.

---

## Adding Media

Moved files into folders:

```bash
mv "<file>" ~/Jellyfin/Films/
mv "<folder>" ~/Jellyfin/TV/
```

Jellyfin scanned and detected media automatically.

---

## Permissions

Checked:

```bash
ls -ld ~/Jellyfin ~/Jellyfin/*
```

Ensured correct ownership:

```bash
sudo chown -R akwastaken:akwastaken ~/Jellyfin
chmod -R 775 ~/Jellyfin
```

---

## Access from Other Devices

- opened `http://<ip>:8096` on phone
- worked within same WiFi network

Tested with iOS app (Streamyfin):
- connected using server IP
- playback worked

---

## Observations

- works without external account
- simple setup compared to Plex
- UI is basic but functional
- local streaming works reliably

---

## Limitations

- not accessible outside network
- depends on local IP
- no built-in remote access solution

---

## Notes

- replaced Plex completely
- used as main media server going forward