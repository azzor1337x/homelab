
![Timezone](https://img.shields.io/badge/timezone-America%2FSao_Paulo-green?logo=clockify&logoColor=white)
![Portainer](https://img.shields.io/badge/portainer-docker--ui-0db7ed?logo=portainer&logoColor=white)

---

```bash
sudo timedatectl set-timezone America/Sao_Paulo
```

---

## 📊 Instalar Portainer

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
