
![HTML](https://img.shields.io/badge/HTML-Static_Pages-orange?logo=html5)
![Nginx](https://img.shields.io/badge/Nginx-Web_Server-brightgreen?logo=nginx)
![Icecast2](https://img.shields.io/badge/icecast2-installed-lightgrey?logo=castorama&logoColor=white)
![Liquidsoap](https://img.shields.io/badge/liquidsoap-v2.3.2-orange?logo=music&logoColor=white)

---

## 📝 Página Principal (radio)

```bash
sudo mkdir -p /opt/radio
sudo nano /opt/radio/index.html
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

## 📝 manifest.json

```bash
sudo nano /opt/radio/manifest.json
```

Conteúdo:
```json
{
  "name": "Rádio",
  "short_name": "Rádio",
  "description": "Rádio",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#0F172A",
  "theme_color": "#0F172A",
  "icons": [
    {
      "src": "/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ],
  "screenshots": [
    {
      "src": "/screenshot-mobile.png",
      "sizes": "1080x1920",
      "type": "image/png"
    },
    {
      "src": "/screenshot-desktop.png",
      "sizes": "1440x900",
      "type": "image/png"
    }
  ]
}
```

## 📝 sw.js

```bash
sudo nano /opt/radio/sw.js
```

Conteúdo:
```js
const CACHE_NAME = 'radio-cache-v1';
const URLS_TO_CACHE = [
  '/',
  '/index.html',
  '/manifest.json',
  '/icon-192.png',
  '/icon-512.png'
];

// Instala e adiciona arquivos ao cache
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME).then(cache => {
      return cache.addAll(URLS_TO_CACHE);
    })
  );
});

// Limpa caches antigos ao ativar
self.addEventListener('activate', event => {
  event.waitUntil(
    caches.keys().then(keys =>
      Promise.all(
        keys.map(key => {
          if (key !== CACHE_NAME) {
            return caches.delete(key);
          }
        })
      )
    )
  );
});

// Responde com cache ou rede
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(response =>
      response || fetch(event.request)
    )
  );
});
```

## 📝 imagens.png

```bash
screenshot-mobile.png
screenshot-desktop.png
icon-512.png
icon-192.png
```

---

## 🐳 Nginx (radio)

```bash
sudo nano /opt/radio/docker-compose.yml
```

Conteúdo:
```yaml
services:
  radio:
    image: nginx:alpine
    container_name: radio
    ports:
      - "8089:80"
    volumes:
      - .:/usr/share/nginx/html:ro
    restart: unless-stopped
```

```bash
cd /opt/radio
sudo docker-compose up -d
```

---

## 📡 Icecast2

```bash
sudo apt install -y icecast2
```

---

## 🎧 Liquidsoap v2.3.2

```bash
sudo mkdir -p /opt/radio/liquidsoap
sudo nano /opt/radio/liquidsoap/docker-compose.yml
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
sudo nano /opt/radio/liquidsoap/radio.liq
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
  url = "http://radio.azzor1337x.shop/radio.mp3",
  public = true,
  radio)
```

```bash
sudo systemctl enable icecast2
sudo systemctl start icecast2
cd /opt/radio/liquidsoap
sudo docker-compose up -d
```

---
