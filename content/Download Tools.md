## Goal
Download media files efficiently on the server.

---

## aria2c (CLI)

This basically is a CLI torrent downloader.
Installed aria2:

```bash
sudo apt update
sudo apt install aria2
```

---

## Attempt with Magnet Links

Tried running:

```bash
aria2c "magnet:?xt=..."
```

Observed:
- command split incorrectly due to `&`
- multiple background jobs started
- download did not proceed properly

Errors included:
- DHT routing table issues
- no peers / no progress

---

## Issues with aria2c

- not straightforward for magnet links
- requires proper quoting
- less intuitive for torrent management
- no easy UI to monitor progress

Conclusion:
- not suitable for current use

---

## Switching to qBittorrent-nox

This is a web-version of qbittorrent, that you can port forward to any computer, and access your torrent from anywhere.

Installation:

```bash
sudo apt install qbittorrent-nox
```

Ran:

```bash
qbittorrent-nox
```

This opens a web window of qbittorrent, which I can open from anywhere using the IP. But this keeps the program running in the open ssh terminal. To avoid this, we can open a daemon instance of the same application using the following command.

```bash
qbittorrent-nox -d
```

---

## Usage

- added magnet links through local UI
- selected download directories:
  - `~/Jellyfin/TV`
  - `~/Jellyfin/Films`

Downloads worked reliably.

---

## Observations

- GUI made torrent management easier
- automatic peer discovery worked
- progress and errors visible

---

## Permissions Issue

Faced error when downloading to certain folders.

Checked:

```bash
ls -ld ~/Jellyfin/*
```

Found mismatch in ownership.

Fixed:

```bash
sudo chown -R akwastaken:akwastaken ~/Jellyfin
chmod -R 775 ~/Jellyfin
```

---

## Notes

- aria2c is powerful but not user-friendly for torrents
- qBittorrent works well for this setup
- directory permissions are important for downloads