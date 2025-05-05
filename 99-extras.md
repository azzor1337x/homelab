
![Timezone](https://img.shields.io/badge/timezone-America%2FSao_Paulo-green?logo=clockify&logoColor=white)
![Portainer](https://img.shields.io/badge/portainer-docker--ui-0db7ed?logo=portainer&logoColor=white)
![MeTube](https://img.shields.io/badge/MeTube-YouTube%20Downloader-red?logo=youtube)
![Navidrome](https://img.shields.io/badge/Navidrome-Music%20Server-yellow?logo=musicbrainz)

---

## 📊 Timezone

```bash
sudo timedatectl set-timezone America/Sao_Paulo
```

---

## 📊 Portainer

```bash
sudo mkdir -p /opt/portainer
sudo nano /opt/portainer/docker-compose.yml
```

```yaml
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data

volumes:
  portainer_data:
```

```bash
cd /opt/portainer
sudo docker-compose up -d
```

---

## 📺 MeTube

```bash
sudo mkdir -p /opt/metube
sudo nano /opt/metube/docker-compose.yml
```

```yaml
services:
  metube:
    image: ghcr.io/alexta69/metube
    container_name: metube
    restart: unless-stopped
    ports:
      - "8088:8088"
    volumes:
      - /home/homelab/music:/downloads
```

## ⚙️ Criar pasta das musicas

```bash
sudo mkdir -p /home/homelab/music
```

```bash
cd /opt/metube
sudo docker-compose up -d
```

---

## 🎧 Navidrome

```bash
sudo mkdir -p /opt/navidrome
sudo nano /opt/navidrome/docker-compose.yml
```

```yaml
services:
  navidrome:
    image: deluan/navidrome:latest
    container_name: navidrome
    ports:
      - "4533:4533"
    restart: unless-stopped
    volumes:
      - "/opt/navidrome/data:/data"
      - "/home/homelab/music:/music:ro"
```

```bash
cd /opt/navidrome
sudo docker-compose up -d
```

---
