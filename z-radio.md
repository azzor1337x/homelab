
![Icecast2](https://img.shields.io/badge/icecast2-installed-lightgrey?logo=castorama&logoColor=white)
![Liquidsoap](https://img.shields.io/badge/liquidsoap-v2.3.2-orange?logo=music&logoColor=white)

---

## 📡 Icecast2

```bash
sudo apt install -y icecast2
```

---

## 🎧 Liquidsoap v2.3.2

```bash
sudo mkdir -p /opt/liquidsoap
sudo nano /opt/liquidsoap/docker-compose.yml
```

Conteúdo:
```yaml
services:
  liquidsoap:
    image: savonet/liquidsoap:v2.3.2
    container_name: liquidsoap
    volumes:
      - ./radio.liq:/etc/liquidsoap/radio.liq
      - /home/homelab/music:/home/music
    restart: unless-stopped
    command: ["liquidsoap", "/etc/liquidsoap/radio.liq"]
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

## 🎵 radio.liq (Liquidsoap)

```bash
sudo nano /opt/liquidsoap/radio.liq
```

Conteúdo:
```liq
radio_playlist = playlist(mode="normal", reload_mode="lazy", "/home/music")
crossfaded = crossfade(fade_in=5.0, fade_out=5.0, radio_playlist)
crossfaded = amplify(0.5, crossfaded)
radio = fallback(track_sensitive = false, [crossfaded, blank()])
output.icecast(%mp3,
  host = "host.docker.internal",
  port = 8000,
  password = "hackme",
  mount = "/radio.mp3",
  name = "radio",
  description = "em costrução",
  genre = "louvor",
  url = "http://radio.azzor1337x.shop/radio.mp3",
  public = true,
  radio)
```

```bash
sudo systemctl enable icecast2
sudo systemctl start icecast2
cd /opt/liquidsoap
sudo docker-compose up -d
```

---

## index.html

```bash
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="Rádio - em construção" />
  <meta name="theme-color" content="#1E293B" />
  <title>Rádio</title>

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- Font Awesome -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />

  <!-- Manifest for PWA -->
  <link rel="manifest" href="/manifest.json" />
</head>
<body class="font-sans text-gray-100 flex flex-col items-center justify-center p-4" style="background-color: #000000; min-height: 100vh;">

  <div class="bg-[rgba(15,23,42,0.85)] rounded-xl shadow-2xl p-8 player-container w-full max-w-md">
    <div class="text-center mb-8">
      <h1 class="text-3xl font-bold mb-2">Rádio</h1>
      <p class="text-gray-300">em construção</p>
    </div>

    <div class="flex flex-col items-center mb-8 space-y-4">
      <button id="playButton" class="bg-black bg-opacity-70 hover:bg-opacity-100 text-white rounded-full w-16 h-16 flex items-center justify-center mb-2 transition-all duration-200">
        <i id="playIcon" class="fas fa-play text-2xl"></i>
      </button>
      <p id="statusText" class="text-sm text-gray-400">Carregando áudio...</p>

      <div class="w-full max-w-xs">
        <div class="flex items-center space-x-2">
          <i class="fas fa-volume-down text-gray-300"></i>
          <input type="range" id="volumeControl" class="w-full h-1 bg-gray-700 outline-none" min="0" max="1" step="0.01" value="0.1" />
          <i class="fas fa-volume-up text-gray-300"></i>
        </div>
      </div>
    </div>

    <div class="bg-black bg-opacity-50 rounded-lg p-4 mb-8">
      <h3 class="font-semibold text-center mb-2">Versículo do dia</h3>
      <p id="bibleVerse" class="text-center italic text-gray-200">Carregando versículo...</p>
    </div>

    <div class="text-xs text-gray-400 text-center mb-4">
      <p>Esta rádio é um projeto pessoal, sem fins lucrativos. Os louvores são usados apenas com o propósito de tocar no coração de amigos, familiares e quem mais ouvir. Caso você seja o artista ou detentor dos direitos e deseje que o conteúdo seja removido, entre em contato e será retirado imediatamente com respeito e gratidão.</p>
    </div>

    <div class="flex justify-center space-x-4">
      <a href="https://wa.me/5547992280371" target="_blank" class="text-gray-300 hover:text-white transition-colors duration-200">
        <i class="fab fa-whatsapp text-xl"></i>
      </a>
      <a href="https://www.instagram.com/azzor1337x/" target="_blank" class="text-gray-300 hover:text-white transition-colors duration-200">
        <i class="fab fa-instagram text-xl"></i>
      </a>
    </div>
  </div>

  <audio id="radioStream" preload="none">
    <source src="https://radio.azzor1337x.shop/radio.mp3" type="audio/mpeg" />
  </audio>

  <script>
    const radioStream = document.getElementById('radioStream');
    const playButton = document.getElementById('playButton');
    const playIcon = document.getElementById('playIcon');
    const volumeControl = document.getElementById('volumeControl');
    const statusText = document.getElementById('statusText');

    let isPlaying = false;

    window.addEventListener('load', () => {
      radioStream.play().then(() => {
        playIcon.className = 'fas fa-pause text-2xl';
        statusText.textContent = "Tocando!";
        isPlaying = true;
      }).catch((e) => {
        console.log("Autoplay bloqueado:", e);
      });
    });

    playButton.addEventListener('click', async () => {
      if (isPlaying) {
        radioStream.pause();
        playIcon.className = 'fas fa-play text-2xl';
        statusText.textContent = "Pausado";
        isPlaying = false;
      } else {
        try {
          await radioStream.play();
          playIcon.className = 'fas fa-pause text-2xl';
          statusText.textContent = "Tocando!";
          isPlaying = true;
        } catch (error) {
          console.error("Erro ao tocar áudio:", error);
          alert("Não foi possível tocar o áudio. Verifique sua conexão.");
        }
      }
    });

    volumeControl.addEventListener('input', function () {
      radioStream.volume = this.value;
    });

    radioStream.volume = volumeControl.value;

    radioStream.addEventListener('canplay', () => {
      statusText.textContent = "Pronto para tocar!";
    });

    radioStream.addEventListener('error', () => {
      statusText.textContent = "Erro ao carregar o áudio.";
      playIcon.className = 'fas fa-play text-2xl';
      isPlaying = false;
    });

    async function getDailyVerseFromAPI() {
      const verses = [
        "Salmos 100:4",
        "Salmos 150:6",
        "Salmos 95:1",
        "Salmos 103:1",
        "Salmos 147:1",
        "Hebreus 13:15",
        "Efésios 5:19",
        "Isaías 25:1",
        "Apocalipse 5:13",
        "Salmos 34:1"
      ];

      const randomVerse = verses[Math.floor(Math.random() * verses.length)];
      const url = `https://bible-api.com/${encodeURIComponent(randomVerse)}?translation=almeida`;

      try {
        const response = await fetch(url);
        const data = await response.json();

        if (data && data.text) {
          document.getElementById('bibleVerse').textContent = `${data.text.trim()} — ${data.reference}`;
        } else {
          throw new Error("Resposta inválida da API.");
        }
      } catch (error) {
        console.error("Erro ao carregar versículo:", error);
        document.getElementById('bibleVerse').textContent = "Não foi possível carregar o versículo.";
      }
    }

    getDailyVerseFromAPI();

    if ('serviceWorker' in navigator) {
      window.addEventListener('load', () => {
        navigator.serviceWorker.register('/sw.js')
          .then(reg => console.log('Service Worker registrado'))
          .catch(err => console.log('Erro ao registrar SW:', err));
      });
    }
  </script>
</body>
</html>
```

























![HTML](https://img.shields.io/badge/HTML-Static_Pages-orange?logo=html5)
![Nginx](https://img.shields.io/badge/Nginx-Web_Server-brightgreen?logo=nginx)
![Cloudflared](https://img.shields.io/badge/Cloudflared-Tunnel-blue)
![Icecast2](https://img.shields.io/badge/icecast2-installed-lightgrey?logo=castorama&logoColor=white)
![Liquidsoap](https://img.shields.io/badge/liquidsoap-v2.3.2-orange?logo=music&logoColor=white)

---

## 📝 Página Principal (blog)

```bash
sudo mkdir -p /opt/blog
sudo nano /opt/blog/index.html
```

Conteúdo:
```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="Rádio - em construção" />
  <meta name="theme-color" content="#1E293B" />
  <title>Rádio</title>

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- Font Awesome -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />

  <!-- Manifest for PWA -->
  <link rel="manifest" href="/manifest.json" />
</head>
<body class="font-sans text-gray-100 flex flex-col items-center justify-center p-4" style="background-color: #000000; min-height: 100vh;">

  <div class="bg-[rgba(15,23,42,0.85)] rounded-xl shadow-2xl p-8 player-container w-full max-w-md">
    <div class="text-center mb-8">
      <h1 class="text-3xl font-bold mb-2">Rádio</h1>
      <p class="text-gray-300">em construção</p>
    </div>

    <div class="flex flex-col items-center mb-8 space-y-4">
      <button id="playButton" class="bg-black bg-opacity-70 hover:bg-opacity-100 text-white rounded-full w-16 h-16 flex items-center justify-center mb-2 transition-all duration-200">
        <i id="playIcon" class="fas fa-play text-2xl"></i>
      </button>
      <p id="statusText" class="text-sm text-gray-400">Carregando áudio...</p>

      <div class="w-full max-w-xs">
        <div class="flex items-center space-x-2">
          <i class="fas fa-volume-down text-gray-300"></i>
          <input type="range" id="volumeControl" class="w-full h-1 bg-gray-700 outline-none" min="0" max="1" step="0.01" value="0.1" />
          <i class="fas fa-volume-up text-gray-300"></i>
        </div>
      </div>
    </div>

    <div class="bg-black bg-opacity-50 rounded-lg p-4 mb-8">
      <h3 class="font-semibold text-center mb-2">Versículo do dia</h3>
      <p id="bibleVerse" class="text-center italic text-gray-200">Carregando versículo...</p>
    </div>

    <div class="text-xs text-gray-400 text-center mb-4">
      <p>Esta rádio é um projeto pessoal, sem fins lucrativos. Os louvores são usados apenas com o propósito de tocar no coração de amigos, familiares e quem mais ouvir. Caso você seja o artista ou detentor dos direitos e deseje que o conteúdo seja removido, entre em contato e será retirado imediatamente com respeito e gratidão.</p>
    </div>

    <div class="flex justify-center space-x-4">
      <a href="https://wa.me/5547992280371" target="_blank" class="text-gray-300 hover:text-white transition-colors duration-200">
        <i class="fab fa-whatsapp text-xl"></i>
      </a>
      <a href="https://www.instagram.com/azzor1337x/" target="_blank" class="text-gray-300 hover:text-white transition-colors duration-200">
        <i class="fab fa-instagram text-xl"></i>
      </a>
    </div>
  </div>

  <audio id="radioStream" preload="none">
    <source src="https://radio.azzor1337x.shop/radio.mp3" type="audio/mpeg" />
  </audio>

  <script>
    const radioStream = document.getElementById('radioStream');
    const playButton = document.getElementById('playButton');
    const playIcon = document.getElementById('playIcon');
    const volumeControl = document.getElementById('volumeControl');
    const statusText = document.getElementById('statusText');

    let isPlaying = false;

    window.addEventListener('load', () => {
      radioStream.play().then(() => {
        playIcon.className = 'fas fa-pause text-2xl';
        statusText.textContent = "Tocando!";
        isPlaying = true;
      }).catch((e) => {
        console.log("Autoplay bloqueado:", e);
      });
    });

    playButton.addEventListener('click', async () => {
      if (isPlaying) {
        radioStream.pause();
        playIcon.className = 'fas fa-play text-2xl';
        statusText.textContent = "Pausado";
        isPlaying = false;
      } else {
        try {
          await radioStream.play();
          playIcon.className = 'fas fa-pause text-2xl';
          statusText.textContent = "Tocando!";
          isPlaying = true;
        } catch (error) {
          console.error("Erro ao tocar áudio:", error);
          alert("Não foi possível tocar o áudio. Verifique sua conexão.");
        }
      }
    });

    volumeControl.addEventListener('input', function () {
      radioStream.volume = this.value;
    });

    radioStream.volume = volumeControl.value;

    radioStream.addEventListener('canplay', () => {
      statusText.textContent = "Pronto para tocar!";
    });

    radioStream.addEventListener('error', () => {
      statusText.textContent = "Erro ao carregar o áudio.";
      playIcon.className = 'fas fa-play text-2xl';
      isPlaying = false;
    });

    async function getDailyVerseFromAPI() {
      const verses = [
        "Salmos 100:4",
        "Salmos 150:6",
        "Salmos 95:1",
        "Salmos 103:1",
        "Salmos 147:1",
        "Hebreus 13:15",
        "Efésios 5:19",
        "Isaías 25:1",
        "Apocalipse 5:13",
        "Salmos 34:1"
      ];

      const randomVerse = verses[Math.floor(Math.random() * verses.length)];
      const url = `https://bible-api.com/${encodeURIComponent(randomVerse)}?translation=almeida`;

      try {
        const response = await fetch(url);
        const data = await response.json();

        if (data && data.text) {
          document.getElementById('bibleVerse').textContent = `${data.text.trim()} — ${data.reference}`;
        } else {
          throw new Error("Resposta inválida da API.");
        }
      } catch (error) {
        console.error("Erro ao carregar versículo:", error);
        document.getElementById('bibleVerse').textContent = "Não foi possível carregar o versículo.";
      }
    }

    getDailyVerseFromAPI();

    if ('serviceWorker' in navigator) {
      window.addEventListener('load', () => {
        navigator.serviceWorker.register('/sw.js')
          .then(reg => console.log('Service Worker registrado'))
          .catch(err => console.log('Erro ao registrar SW:', err));
      });
    }
  </script>
</body>
</html>
```

---

## 📝 Subdomínio homelab

```bash
sudo mkdir -p /opt/blog/homelab
sudo nano /opt/blog/homelab/index.html
```

Conteúdo:
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

Conteúdo:
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

Conteúdo:
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

## 📡 Icecast2

```bash
sudo apt install -y icecast2
```

---

## 🎧 Liquidsoap v2.3.2

```bash
sudo mkdir -p /opt/liquidsoap
sudo nano /opt/liquidsoap/docker-compose.yml
```

Conteúdo:
```yaml
services:
  liquidsoap:
    image: savonet/liquidsoap:v2.3.2
    container_name: liquidsoap
    volumes:
      - ./radio.liq:/etc/liquidsoap/radio.liq
      - /home/homelab/music:/home/music
    restart: unless-stopped
    command: ["liquidsoap", "/etc/liquidsoap/radio.liq"]
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

## 🎵 radio.liq (Liquidsoap)

```bash
sudo nano /opt/liquidsoap/radio.liq
```

Conteúdo:
```liq
radio_playlist = playlist(mode="normal", reload_mode="lazy", "/home/music")
crossfaded = crossfade(fade_in=5.0, fade_out=5.0, radio_playlist)
crossfaded = amplify(0.5, crossfaded)
radio = fallback(track_sensitive = false, [crossfaded, blank()])
output.icecast(%mp3,
  host = "host.docker.internal",
  port = 8000,
  password = "hackme",
  mount = "/radio.mp3",
  name = "radio",
  description = "em costrução",
  genre = "louvor",
  url = "http://radio.azzor1337x.shop/radio.mp3",
  public = true,
  radio)
```

```bash
sudo systemctl enable icecast2
sudo systemctl start icecast2
cd /opt/liquidsoap
sudo docker-compose up -d
```

---
