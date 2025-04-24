
---

```bash
sudo rm -rf /opt/blog/index.html
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

    audio {
      display: none; /* esconde o player */
    }
  </style>
</head>
<body>
  <audio autoplay loop>
    <source src="https://radio.azzor1337x.shop/radio.mp3" type="audio/mpeg">
    Seu navegador não suporta o elemento de áudio.
  </audio>

  <header>
    <a href="#" class="logo">azzor1337x</a>
    <div class="top-buttons">
      <a href="https://github.com/azzor1337x/homelab" target="_blank">GitHub</a>
      <a id="metubeLink" class="hidden" href="https://youtube.azzor1337x.shop" target="_blank">MeTube</a>
    </div>
  </header>

  <section class="hero">
    <h1>Homelab</h1>
    <p>
      Ola, espero que esteja bem! Esse é meu homelab e acredite eu rodo ele em um notebook antigo, ASUSTeK K43U com processador AMD C-60 de apenas 1GHZ, duas memorias DDR3 de 4 GB de RAM. Nele estão alguns serviços úteis e divertidos para uso pessoal e aprendizado, em breve mais novidades!
    </p>
    <div class="button-row">
      <a class="btn" href="https://radio.azzor1337x.shop/radio.mp3" target="_blank">Rádio</a>
      <a class="btn" href="https://music.azzor1337x.shop" target="_blank">Navidrome</a>
      <a class="btn" href="https://pihole.azzor1337x.shop/admin/queries" target="_blank">Pi-hole</a>
      <a class="btn" href="https://homelab.azzor1337x.shop" target="_blank">Homelab</a>
      <a class="btn" href="https://portainer.azzor1337x.shop" target="_blank">Portainer</a>
    </div>
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
