
---

```bash
sudo mkdir -p /opt/blog/encantare
sudo nano /opt/blog/encantare/index.html
```

```bash
<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Encantare Nails</title>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=Poppins:wght@300;500&display=swap" rel="stylesheet">
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body, html {
      font-family: 'Poppins', sans-serif;
      color: #fff;
      background-color: #0e0e0e;
      scroll-behavior: smooth;
    }

    .hero-video {
      position: relative;
      height: 100vh;
      overflow: hidden;
    }

    .hero-video iframe {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 100vw;
      height: 56.25vw; /* Manter a proporção 16:9 */
      transform: translate(-50%, -50%);
      pointer-events: none;
      z-index: 1;
      filter: brightness(60%);
    }

    .overlay {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.5);
      z-index: 2;
    }

    .hero-text {
      position: absolute;
      top: 50%;
      left: 50%;
      z-index: 3;
      transform: translate(-50%, -50%);
      text-align: center;
      font-family: 'Playfair Display', serif;
    }

    .hero-text h1 {
      font-size: 4em;
      letter-spacing: 5px;
      font-weight: 700;
      text-transform: uppercase;
      text-shadow: 2px 2px 5px rgba(0,0,0,0.7);
      color: #ffc0cb;
    }

    .container {
      padding: 60px 20px;
      max-width: 1000px;
      margin: 0 auto;
      z-index: 10;
    }

    .title {
      font-size: 2.5em;
      margin-bottom: 20px;
      color: #ffc0cb;
      font-family: 'Playfair Display', serif;
    }

    .section {
      margin-bottom: 60px;
    }

    .section p {
      font-size: 1.2em;
      line-height: 1.6;
      font-weight: 300;
      color: #ddd;
      margin-bottom: 30px;
    }

    .gallery {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 20px;
      margin-top: 30px;
    }

    .gallery img {
      width: 100%;
      border-radius: 15px;
      object-fit: cover;
      transition: transform 0.3s ease;
    }

    .gallery img:hover {
      transform: scale(1.05);
    }

    .buttons {
      display: flex;
      gap: 20px;
      justify-content: center;
      margin-top: 40px;
    }

    .btn {
      background: #ffc0cb;
      color: #000;
      padding: 15px 40px;
      border: none;
      border-radius: 50px;
      font-size: 1.2em;
      cursor: pointer;
      text-decoration: none;
      transition: all 0.3s ease;
      box-shadow: 0 8px 15px rgba(0, 0, 0, 0.2);
    }

    .btn:hover {
      background: #fff;
      color: #000;
      transform: translateY(-5px);
      box-shadow: 0 15px 25px rgba(0, 0, 0, 0.3);
    }

    footer {
      background: #111;
      padding: 20px;
      text-align: center;
      font-size: 0.9em;
      color: #777;
      margin-top: 50px;
      letter-spacing: 2px;
    }

    footer a {
      color: #ffc0cb;
      text-decoration: none;
      font-weight: 600;
    }

    footer a:hover {
      text-decoration: underline;
    }
  </style>
</head>
<body>

  <div class="hero-video">
    <iframe src="https://player.vimeo.com/video/1076812298?muted=1&autoplay=1&autopause=0&loop=1&background=1&app_id=122963"
      frameborder="0"
      allow="autoplay; fullscreen"
      allowfullscreen>
    </iframe>
    <div class="overlay"></div>
    <div class="hero-text">
      <h1>Encantare Nails</h1>
    </div>
  </div>

  <div class="container">
    <div class="section">
      <h2 class="title">Beleza que Encanta</h2>
      <p>Especializada em alongamento de unhas e esmaltação em gel, trazendo arte, cuidado e sofisticação para suas mãos.</p>
    </div>

    <div class="section">
      <h2 class="title">Galeria de Estilo</h2>
      <div class="gallery">
        <img src="https://i.pinimg.com/736x/af/4b/d0/af4bd023dbbe713d3197ecf8ef50fc10.jpg" alt="Unha 1"/>
        <img src="https://i.pinimg.com/736x/15/97/8f/15978f1cb815576340c4b31991aa8ea3.jpg" alt="Unha 3"/>
        <img src="https://i.pinimg.com/736x/f1/41/88/f141880276193c4c1435ed53026ecc9c.jpg" alt="Unha 4"/>
      </div>
    </div>

    <div class="section">
      <div class="buttons">
        <a class="btn" href="https://wa.me/5547997210373" target="_blank">Agendar pelo WhatsApp</a>
      </div>
    </div>
  </div>

  <footer>
    © 2025 Encantare Nails — Joinville/SC | <a href="https://www.instagram.com/encantare_nails/" target="_blank">Instagram</a>
  </footer>

</body>
</html>
```

```bash
sudo nano /opt/blog/docker-compose.yml
```

```bash
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

  encantare:
    image: nginx:alpine
    container_name: web_encantare
    ports:
      - "8084:80"
    volumes:
      - ./encantare:/usr/share/nginx/html:ro
    restart: unless-stopped
```

```bash
cd /opt/blog
sudo docker-compose up -d
```

---
