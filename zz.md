![root](https://img.shields.io/badge/root-superuser-red?logo=linux&logoColor=white)
![iptables](https://img.shields.io/badge/iptables-firewall-important?logo=linux&logoColor=white)
![HTML](https://img.shields.io/badge/HTML-Static_Pages-orange?logo=html5&logoColor=white)
![Timezone](https://img.shields.io/badge/timezone-America%2FSao_Paulo-green?logo=clockify&logoColor=white)
![Navidrome](https://img.shields.io/badge/Navidrome-Music%20Server-yellow?logo=musicbrainz)
![Alpine](https://img.shields.io/badge/Alpine-Repository-0D597F?logo=alpinelinux&logoColor=white)

## 📊 Timezone

```bash
sudo timedatectl set-timezone America/Sao_Paulo
```

## 🎧 Navidrome

```
services:
  navidrome:
    image: deluan/navidrome:latest
    container_name: navidrome
    ports:
      - "4533:4533"
    restart: unless-stopped
    volumes:
      - "/opt/navidrome/data:/data"
      - "/home/music:/music:ro"
```
