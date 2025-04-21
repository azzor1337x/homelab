![Nginx](https://img.shields.io/badge/Nginx-Web_Server-brightgreen?logo=nginx)
![HTML](https://img.shields.io/badge/HTML-Static_Pages-orange?logo=html5)
![Cloudflared](https://img.shields.io/badge/Cloudflared-Tunnel-blue)

## 📝 HTML para Página Principal (blog) e Subdomínio homelab

Crie os arquivos HTML:

```bash
sudo mkdir -p /opt/blog
sudo nano /opt/blog/index.html
```

Conteúdo Página Principal (blog):

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>azzor1337x - Blog</title>
</head>
<body>
  <h1>azzor1337x</h1>
  <p>blog!</p>
</body>
</html>
```

---

```bash
sudo mkdir -p /opt/blog/homelab
sudo nano /opt/blog/homelab/index.html
```

Conteúdo Subdomínio homelab:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>azzor1337x - Homelab</title>
</head>
<body>
  <h1>azzor1337x</h1>
  <p>homelab!</p>
</body>
</html>
```

---

## 🐳 docker-compose para Nginx (blog e homelab)

```bash
sudo nano /opt/blog/docker-compose.yml
```

```yaml
services:
  blog:
    image: nginx:alpine
    container_name: web_blog
    ports:
      - "8082:80"
    volumes:
      - .:/usr/share/nginx/html:ro
    restart: unless-stopped

  homelab:
    image: nginx:alpine
    container_name: web_homelab
    ports:
      - "8083:80"
    volumes:
      - ./homelab:/usr/share/nginx/html:ro
    restart: unless-stopped
```

```bash
cd /opt/blog
sudo docker-compose up -d
```

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

---

## ⚙️ Configuração do Túnel

```bash
sudo nano /root/.cloudflared/config.yml
```

```yaml
tunnel: blog
credentials-file: /root/.cloudflared/<UUID>.json

ingress:
  - hostname: pihole.azzor1337x.shop
    service: http://127.0.0.1:80

  - hostname: music.azzor1337x.shop
    service: http://127.0.0.1:4533

  - hostname: radio.azzor1337x.shop
    service: http://127.0.0.1:8000

  - hostname: youtube.azzor1337x.shop
    service: http://127.0.0.1:8081

  - hostname: azzor1337x.shop
    service: http://127.0.0.1:8082

  - hostname: homelab.azzor1337x.shop
    service: http://127.0.0.1:8083

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
