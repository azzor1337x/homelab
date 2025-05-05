![Nginx](https://img.shields.io/badge/Nginx-Web_Server-brightgreen?logo=nginx)
![HTML](https://img.shields.io/badge/HTML-Static_Pages-orange?logo=html5)
![Cloudflared](https://img.shields.io/badge/Cloudflared-Tunnel-blue)

---

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
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>azzor1337x</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Inter', sans-serif;
    }

    body {
      background-color: #0d1117;
      color: #c9d1d9;
      line-height: 1.6;
    }

    header {
      background-color: #161b22;
      padding: 1rem 2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 1px solid #30363d;
    }

    .logo {
      font-size: 1.5rem;
      font-weight: 600;
      color: #58a6ff;
      text-decoration: none;
    }

    .top-buttons a {
      color: #c9d1d9;
      margin-left: 1rem;
      text-decoration: none;
      font-weight: 500;
      border: 1px solid #30363d;
      padding: 0.5rem 1rem;
      border-radius: 6px;
      transition: background-color 0.3s, color 0.3s;
    }

    .top-buttons a:hover {
      background-color: #21262d;
      color: #58a6ff;
    }

    .hidden {
      display: none;
    }

    .hero {
      padding: 4rem 2rem;
      text-align: center;
    }

    .hero h1 {
      font-size: 3rem;
      margin-bottom: 1rem;
      color: #58a6ff;
    }

    .hero p {
      font-size: 1.2rem;
      max-width: 600px;
      margin: 0 auto 2rem;
    }

    footer {
      text-align: center;
      padding: 2rem;
      border-top: 1px solid #30363d;
      margin-top: 4rem;
      color: #8b949e;
    }

    /* Estilo do player de áudio */
    audio {
      display: block;
      margin: 2rem auto;
      width: 100%;
      max-width: 400px;
    }
  </style>
</head>
<body>

  <header>
    <a href="#" class="logo">azzor1337x</a>
    <div class="top-buttons">
      <a id="metubeLink" class="hidden" href="https://youtube.azzor1337x.shop" target="_blank">MeTube</a>
    </div>
  </header>

  <section class="hero">
    <h1>Rádio</h1>
    <p>
      Olá, espero que esses louvores toquem seu coração! A rádio está tocando em looping uma seleção especial. Em breve, você poderá fazer seus pedidos de música e sugestões para tornar a experiência ainda mais pessoal.
    </p>

    <!-- Player de áudio aqui dentro -->
    <audio autoplay loop controls>
      <source src="https://radio.azzor1337x.shop/radio.mp3" type="audio/mpeg">
      Seu navegador não suporta o elemento de áudio.
    </audio>
  </section>

  <footer>
    © 2025 azzor1337x Homelab. Todos os direitos reservados.
  </footer>

  <script>
    const radio = document.querySelector('audio');
    if (radio) {
      radio.volume = 0.05; // Define o volume para 5%
    }

    document.addEventListener('keydown', function(e) {
      if (e.key.toLowerCase() === 'm') {
        const metubeLink = document.getElementById('metubeLink');
        metubeLink.classList.toggle('hidden');
      }
    });
  </script>
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
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>azzor1337x</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Inter', sans-serif;
    }

    body {
      background-color: #0d1117;
      color: #c9d1d9;
      line-height: 1.6;
    }

    header {
      background-color: #161b22;
      padding: 1rem 2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 1px solid #30363d;
    }

    .logo {
      font-size: 1.5rem;
      font-weight: 600;
      color: #58a6ff;
      text-decoration: none;
    }

    .top-buttons a {
      color: #c9d1d9;
      margin-left: 1rem;
      text-decoration: none;
      font-weight: 500;
      border: 1px solid #30363d;
      padding: 0.5rem 1rem;
      border-radius: 6px;
      transition: background-color 0.3s, color 0.3s;
    }

    .top-buttons a:hover {
      background-color: #21262d;
      color: #58a6ff;
    }

    .hero {
      padding: 4rem 2rem;
      text-align: center;
    }

    .hero h1 {
      font-size: 3rem;
      margin-bottom: 1rem;
      color: #58a6ff;
    }

    .hero p {
      font-size: 1.2rem;
      max-width: 600px;
      margin: 0 auto 2rem;
    }

    .button-row {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 1rem;
      margin-top: 2rem;
    }

    .btn {
      background-color: #238636;
      color: white;
      border: none;
      border-radius: 6px;
      font-size: 1rem;
      padding: 0.75rem 1.5rem;
      cursor: pointer;
      text-decoration: none;
      transition: background-color 0.3s;
    }

    .btn:hover {
      background-color: #2ea043;
    }

    footer {
      text-align: center;
      padding: 2rem;
      border-top: 1px solid #30363d;
      margin-top: 4rem;
      color: #8b949e;
    }
  </style>
</head>
<body>

  <header>
    <a href="#" class="logo">azzor1337x</a>
    <div class="top-buttons">
      <a href="https://github.com/azzor1337x/homelab" target="_blank">GitHub</a>
    </div>
  </header>

  <section class="hero">
    <h1>Homelab</h1>
    <p>
      Olá, espero que esteja bem! Esse é meu homelab e acredite, eu rodo ele em um notebook antigo ASUSTeK K43U com processador AMD C-60 de apenas 1GHz e memórias DDR3. Nele estão alguns serviços para uso pessoal e aprendizado. Em breve, mais novidades!
    </p>
    <div class="button-row">
      <a class="btn" href="https://azzor1337x.shop" target="_blank">Rádio</a>
      <a class="btn" href="https://radio.azzor1337x.shop/radio.mp3" target="_blank">Stream</a>
      <a class="btn" href="https://radio.azzor1337x.shop" target="_blank">Icecast</a>     
      <a class="btn" href="https://music.azzor1337x.shop" target="_blank">Navidrome</a>
      <a class="btn" href="https://pihole.azzor1337x.shop/admin/queries" target="_blank">Pi-hole</a>
      <a class="btn" href="https://homelab.azzor1337x.shop" target="_blank">Homelab</a>
      <a class="btn" href="https://portainer.azzor1337x.shop" target="_blank">Portainer</a>
      <a class="btn" href="https://youtube.azzor1337x.shop" target="_blank">MeTube</a> <!-- Novo botão para MeTube -->
    </div>
  </section>

  <footer>
    © 2025 azzor1337x Homelab. Todos os direitos reservados.
  </footer>

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
