
![MeTube](https://img.shields.io/badge/MeTube-YouTube%20Downloader-red?logo=youtube)
![Navidrome](https://img.shields.io/badge/Navidrome-Music%20Server-yellow?logo=musicbrainz)

## 📺 metube

```bash
sudo mkdir -p /home/homelab/music
```

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
      - "8081:8081"
    volumes:
      - /home/homelab/music:/downloads
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
