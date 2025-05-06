
![Cloudflared](https://img.shields.io/badge/Cloudflared-Tunnel-blue)

---

## ☁️ Cloudflared Tunnel

### Instalar Cloudflared

```bash
cd /opt/blog
sudo wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
```

### Autenticar

```bash
sudo cloudflared tunnel login
```

### Criar o túnel

```bash
sudo cloudflared tunnel create blog
```

Copie o nome do túnel (UUID).

## ⚙️ Configuração do Túnel

```bash
sudo nano /root/.cloudflared/config.yml
```

Conteúdo:
```yaml
tunnel: blog
credentials-file: /root/.cloudflared/<UUID>.json

ingress:
  - hostname: pihole.azzor1337x.shop
    service: http://127.0.0.1:80

  - hostname: navidrome.azzor1337x.shop
    service: http://127.0.0.1:4533

  - hostname: radio.azzor1337x.shop
    service: http://127.0.0.1:8000

  - hostname: XXX.azzor1337x.shop
    service: http://127.0.0.1:8081

  - hostname: XXX.azzor1337x.shop
    service: http://127.0.0.1:8082

  - hostname: XXX.azzor1337x.shop
    service: http://127.0.0.1:8083

  - hostname: XXX.azzor1337x.shop
    service: http://127.0.0.1:8084

  - hostname: XXX.azzor1337x.shop
    service: http://127.0.0.1:8085

  - hostname: XXX.azzor1337x.shop
    service: http://127.0.0.1:8086

  - hostname: XXX.azzor1337x.shop
    service: http://127.0.0.1:8087

  - hostname: metube.azzor1337x.shop
    service: http://127.0.0.1:8088

  - hostname: azzor1337x.shop
    service: http://127.0.0.1:8089

  - hostname: portainer.azzor1337x.shop
    service: http://127.0.0.1:9000

  - service: http_status:404
```

---

## 🚀 Iniciar o túnel

```bash
sudo cloudflared tunnel run blog
```

Ou instale como serviço:

```bash
sudo cloudflared service install
```

---
