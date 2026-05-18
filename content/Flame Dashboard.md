## Goal

Install the Flame dashboard on my home-server, and config it to my liking.

---

## Docker

This is like setting up virtual envs for python? Docker has containers that I can run apps on top, which contains all the requisites, dependencies and all those stuff in one singular package, so when sharing, everything gets shared.

```bash
sudo apt update
```

Updating the terminal first.
Uninstall all the previous versions of docker.

```bash
sudo apt remove docker docker-engine docker.io containerd runc
```

Install docker.io.

```bash
sudo apt install docker.io docker-compose-v2
```

Check the version of Docker installed.

```bash
docker --version
docker compose version
```

Create a docker user and add it to the group.

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## Flame in Docker!

```bash
mkdir -p ~/docker/flame
cd ~/docker/flame
```

Create a docker compose file.

```bash
nano compose.yml
```

with this content inside.

```yaml
services:
  flame:
    image: pawelmalak/flame
    container_name: flame
    volumes:
      - ./data:/app/data
    ports:
      - 5005:5005
    restart: unless-stopped
```

---

## Flame first flight!

Boot flame.

```bash
docker compose up -d
```

This will take time, as this will pull the flame dependencies and files from some serve(?).

Try this to see if everything went smoothly.

```bash
docker ps
```

Your Flame dashboard is on!
Go to

```web
http://<your_ip>:5005/
```

and go to settings, then change your password. The default password is

```password
flame_password
```

Voila! Installed docker and Flame! Now add the applications and enjoy the dashboard.

---

## Useful commands

Power down Flame.

```bash
docker compose down
```

Start it again:

```bash
docker compose up -d
```

View logs:

```bash
docker compose logs -f
```

Update flame:

```bash
docker compose pull
docker compose up -d
```

Done!
