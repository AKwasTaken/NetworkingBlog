## Goal
Run a Minecraft server on the Linux Mint laptop.

---

## Java Installation

Check if Java is installed:

```bash
java -version
```

Install Java 21:

```bash
sudo apt update
sudo apt install openjdk-21-jdk
```

Verify the installation:

```bash
java -version
```

I got a few errors while installing java, but got it fixed. Those errors I deem detrimental to a blog such as this.

---

## Server Setup (Legit Server)

Create a folder for the minecraft server:

```bash
mkdir ~/mcserver
cd ~/mcserver
```

Download the server jar file:

```bash
wget https://piston-data.mojang.com/v1/objects/.../server.jar)
```

Download links of specific versions of the server is [linked here](https://gist.github.com/cliffano/77a982a7503669c3e1acb0a0cf6127e9).

---

## First Run

Ran the server:

```bash
java -Xmx2G -Xms2G -jar server.jar nogui
```

Got error about EULA.

---

## Accepting EULA

Edited file:

```bash
nano eula.txt
```

Changed:

```text
eula=false → eula=true
```

---

## Running Server

Started again:

```bash
java -Xmx2G -Xms2G -jar server.jar nogui
```

Server started successfully.

---

## Joining the Server

Found IP:

```bash
hostname -I
```

Used in Minecraft client:

```text
<ip-address>:25565
```

Worked inside same network.

---

## Modded Server (Fabric)

Follow the instructions given in the site below.

[Fabric Server Loader](https://fabricmc.net/use/server/)

---

## Observations

- Server works locally without issues
- Mods require matching client setup
- Port 25565 is used by default

---

## Notes

- No remote access yet
- Did not configure port forwarding
- Server only accessible within same WiFi network