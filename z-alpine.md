
# Editor de Texto

```
apk add nano
```

# Internet + DNS

```
rm -rf /etc/network/interfaces
nano /etc/network/interfaces
```

```
auto eth0
iface eth0 inet static
    address 192.168.1.99
    netmask 255.255.255.0
    gateway 192.168.1.1
```

```
rm -rf /etc/resolv.conf
nano /etc/resolv.conf
```

```
nameserver 192.168.1.1
#nameserver 127.0.0.1
```

```
ifdown eth0 && ifup eth0
ip a show eth0
```

```
ping -c 3 1.1.1.1
ping -c 3 cloudflare.com
```

# Firewall

```
apk add iptables
```

```
rc-update add iptables boot
service iptables start
```

# Docker

```
rm -rf /etc/apk/repositories
nano /etc/apk/repositories
```

```
#/media/sdb1/apks
http://dl-cdn.alpinelinux.org/alpine/v3.21/main
http://dl-cdn.alpinelinux.org/alpine/v3.21/community
```

```
apk update
apk add docker
```

```
rc-update add docker boot
service docker start
```

```
docker run hello-world
```

# Unbound

```
apk add unbound
```

```
mkdir -p /var/lib/unbound
wget https://www.internic.net/domain/named.root -qO- | tee /var/lib/unbound/root.hints
```

```
nano /etc/unbound/unbound.conf.d/pi-hole.conf
```

```
server:
    verbosity: 0
    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    do-ip6: yes
    prefer-ip6: no
    root-hints: "/var/lib/unbound/root.hints"
    harden-glue: yes
    harden-dnssec-stripped: yes
    use-caps-for-id: no
    edns-buffer-size: 1232
    prefetch: yes
    num-threads: 1
    so-rcvbuf: 1m
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10
    private-address: 192.0.2.0/24
    private-address: 198.51.100.0/24
    private-address: 203.0.113.0/24
    private-address: 255.255.255.255/32
    private-address: 2001:db8::/32
```

```
rc-update add unbound boot
service unbound start
```

```
apk add bind-tools
dig pi-hole.net @127.0.0.1 -p 5335
```

# Portainer

```
docker volume create portainer_data
docker run -d -p 9000:9000 -p 9443:9443 --name portainer \
  --restart unless-stopped \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

# Criar Stack do Pihole no Portainer

```
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    network_mode: host
    environment:
      - TZ=America/Sao_Paulo
      - WEBPASSWORD=
    volumes:
      - ./etc-pihole:/etc/pihole
      - ./etc-dnsmasq.d:/etc/dnsmasq.d
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```

```
docker exec -it pihole pihole setpassword
```

## Apos acessar o pihole com a nova senha, desabilite o dns cadastrado e use este: `127.0.0.1#5335`

# Local DNS

```
rm -rf /etc/resolv.conf
nano /etc/resolv.conf
```

```
#nameserver 192.168.1.1
nameserver 127.0.0.1
```

```
chattr +i /etc/resolv.conf
ifdown eth0 && ifup eth0
```

# Radio

```
mkdir -p /home/radio
nano /home/radio/index.html
```

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="ROARRadio - O rugido do Leão que toca o coração" />
  <meta name="theme-color" content="#111827" />
  <title>ROARRadio</title>

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- Font Awesome -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />

  <!-- Favicon -->
  <link rel="icon" type="image/x-icon" href="/favicon.ico" sizes="32x32" />
  <link rel="apple-touch-icon" href="/favicon.ico" />

  <!-- Manifest for PWA -->
  <link rel="manifest" href="/manifest.json" />

  <style>
    body::before {
      content: "";
      background: url('ROARRadio-logo.png') center/contain no-repeat;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      opacity: 0.05;
      z-index: -1;
      filter: blur(1px);
    }
  </style>
</head>
<body class="font-sans text-yellow-100 bg-[#111827] flex flex-col items-center justify-center p-4 min-h-screen">

  <div class="rounded-xl p-8 player-container w-full max-w-md">
    <div class="text-center mb-8">
      <h1 class="text-4xl font-bold text-yellow-400 mb-2">ROARRadio</h1>
      <p class="text-yellow-200">O rugido do Leão que toca o coração</p>
    </div>

    <div class="flex flex-col items-center mb-8 space-y-4">
      <button id="playButton" class="bg-yellow-600 hover:bg-yellow-700 text-white rounded-full w-16 h-16 flex items-center justify-center shadow-lg transition-all duration-200">
        <i id="playIcon" class="fas fa-play text-2xl"></i>
      </button>
      <p id="statusText" class="text-sm text-yellow-300">Carregando áudio...</p>

      <div class="w-full max-w-xs">
        <div class="flex items-center space-x-2">
          <i class="fas fa-volume-down text-yellow-300"></i>
          <input type="range" id="volumeControl" class="w-full h-1 accent-yellow-500 bg-gray-700 outline-none" min="0" max="1" step="0.01" value="0.1" />
          <i class="fas fa-volume-up text-yellow-300"></i>
        </div>
      </div>
    </div>

    <div class="rounded-lg p-4 mb-8">
      <h3 class="font-semibold text-center text-yellow-300 mb-2">Versículo do dia</h3>
      <p id="bibleVerse" class="text-center italic text-yellow-200">Carregando versículo...</p>
    </div>

    <div class="text-xs text-yellow-300 text-center mb-4">
      <p>Esta rádio é um projeto pessoal, sem fins lucrativos. Os louvores são usados apenas com o propósito de tocar no coração de amigos, familiares e quem mais ouvir. Caso você seja o artista ou detentor dos direitos e deseje que o conteúdo seja removido, entre em contato e será retirado imediatamente com respeito e gratidão.</p>
    </div>

    <div class="flex justify-center space-x-4">
      <a href="https://wa.me/5547992280371" target="_blank" class="text-yellow-300 hover:text-yellow-100 transition-colors duration-200">
        <i class="fab fa-whatsapp text-xl"></i>
      </a>
      <a href="https://www.instagram.com/azzor1337x/" target="_blank" class="text-yellow-300 hover:text-yellow-100 transition-colors duration-200">
        <i class="fab fa-instagram text-xl"></i>
      </a>
    </div>
  </div>

  <audio id="introStream" preload="auto">
    <source src="/intro.mp3" type="audio/mpeg" />
  </audio>

  <audio id="radioStream" preload="none">
    <source src="https://icecast.roarradio.site/radio.mp3" type="audio/mpeg" />
  </audio>

  <script>
    const introStream = document.getElementById('introStream');
    const radioStream = document.getElementById('radioStream');
    const playButton = document.getElementById('playButton');
    const playIcon = document.getElementById('playIcon');
    const volumeControl = document.getElementById('volumeControl');
    const statusText = document.getElementById('statusText');

    let isPlaying = false;
    let hasInteracted = false;

    // Função para iniciar a reprodução dos streams
    const resetAndPlay = async () => {
      try {
        introStream.currentTime = 0;
        radioStream.pause();
        introStream.play();

        statusText.textContent = "Tocando introdução...";
        playIcon.className = 'fas fa-pause text-2xl';
        isPlaying = true;

        introStream.onended = async () => {
          radioStream.currentTime = 0;
          await radioStream.play();
          statusText.textContent = "Transmitindo ao vivo!";
        };
      } catch (error) {
        console.error("Erro ao tocar áudio:", error);
        alert("Não foi possível tocar o áudio. Verifique sua conexão.");
      }
    };

    // Primeira interação do usuário com a página (qualquer clique)
    document.body.addEventListener('click', () => {
      if (!hasInteracted) {
        hasInteracted = true;
        resetAndPlay();
      }
    });

    // Adiciona o evento de click no botão de play
    playButton.addEventListener('click', () => {
      if (isPlaying) {
        introStream.pause();
        radioStream.pause();
        playIcon.className = 'fas fa-play text-2xl';
        statusText.textContent = "Pausado";
        isPlaying = false;
      } else {
        resetAndPlay(); // Reinicia tudo com a intro novamente
      }
    });

    // Controle de volume
    volumeControl.addEventListener('input', function () {
      introStream.volume = this.value;
      radioStream.volume = this.value;
    });

    introStream.volume = volumeControl.value;
    radioStream.volume = volumeControl.value;

    radioStream.addEventListener('canplay', () => {
      if (!isPlaying) statusText.textContent = "Pronto para tocar!";
    });

    radioStream.addEventListener('error', () => {
      statusText.textContent = "Erro ao carregar o áudio.";
      playIcon.className = 'fas fa-play text-2xl';
      isPlaying = false;
    });

    async function getDailyVerseFromAPI() {
      const verses = [
        "Salmos 100:4", "Salmos 150:6", "Salmos 95:1", "Salmos 103:1", "Salmos 147:1",
        "Hebreus 13:15", "Efésios 5:19", "Isaías 25:1", "Apocalipse 5:13", "Salmos 34:1"
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
  </script>
</body>
</html>
```

```
nano /home/radio/manifest.json
```

```
{
  "name": "ROARRadio",
  "short_name": "ROARRadio",
  "description": "ROARRadio - O rugido do Leão que toca o coração",
  "start_url": "/",
  "scope": "/",
  "display": "standalone",
  "background_color": "#0F172A",
  "theme_color": "#0F172A",
  "icons": [
    {
      "src": "/icon-192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "/icon-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
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


```
nano /home/radio/sw.js
```

```
const CACHE_VERSION = 'v1.0.0';
const CACHE_NAME = `radio-cache-${CACHE_VERSION}`;
const OFFLINE_URL = '/offline.html';

const URLS_TO_CACHE = [
  '/',
  '/index.html',
  '/manifest.json',
  '/icon-192.png',
  '/icon-512.png',
  '/intro.mp3',  
  OFFLINE_URL
];

self.addEventListener('install', event => {
  self.skipWaiting(); // Forçar ativação imediata
  event.waitUntil(
    caches.open(CACHE_NAME).then(cache => {
      return cache.addAll(URLS_TO_CACHE);
    })
  );
});

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

self.addEventListener('fetch', event => {
  const url = new URL(event.request.url);
  // Se for um recurso do próprio site
  if (url.origin === location.origin) {
    event.respondWith(
      caches.match(event.request).then(cached => {
        return cached || fetch(event.request).catch(() => caches.match(OFFLINE_URL));
      })
    );
  }
});
```

```
nano /home/radio/offline.html
```

```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>Offline</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
</head>
<body style="display: flex; justify-content: center; align-items: center; height: 100vh; background: #0F172A; color: white; text-align: center;">
  <div>
    <h1>Você está offline</h1>
    <p>A rádio não está disponível no momento.<br>Verifique sua conexão e tente novamente.</p>
  </div>
</body>
</html>
```

# Icecast

```
apk add icecast
```

```
rc-update add icecast boot
service icecast start
```

# Criar Stack do Liquidsoap no Portainer

```
services:
  liquidsoap:
    image: savonet/liquidsoap:v2.3.2
    container_name: liquidsoap
    volumes:
      - /home/radio/radio.liq:/etc/liquidsoap/radio.liq
      - /home/music:/home/music
    restart: unless-stopped
    command: [liquidsoap, /etc/liquidsoap/radio.liq]
    extra_hosts:
      - host.docker.internal:host-gateway
```

```
nano /home/radio/radio.liq
```

```
radio_playlist = playlist(mode="normal", reload_mode="lazy", "/home/music")
crossfaded = crossfade(fade_in=5.0, fade_out=5.0, radio_playlist)
crossfaded = amplify(0.5, crossfaded)
radio = fallback(track_sensitive = false, [crossfaded, blank()])
output.icecast(%mp3,
  host = "host.docker.internal",
  port = 8000,
  password = "hackme",
  mount = "/radio.mp3",
  name = "ROARRadio",
  description = "O rugido do Leao que toca o coracao",
  url = "https://icecast.roarradio.site",
  public = true,
  radio)
```

# Criar Stack do Radio no Portainer

```
services:
  radio:
    image: nginx:alpine
    container_name: radio
    ports:
      - 8001:80
    volumes:
      - /home/radio:/usr/share/nginx/html:ro
    restart: unless-stopped
```

# File Browser

```
apk add curl
apk add bash
```

```
cd /
```

```
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
filebrowser -a 0.0.0.0 -p 8888 -r /
```

# Cloudflared

```
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 -O /usr/bin/cloudflared
chmod +x /usr/local/bin/cloudflared
```

```
cloudflared --version
cloudflared tunnel login
```

```
cloudflared tunnel create radio
nano /root/.cloudflared/config.yml
```

```
tunnel: radio
credentials-file: /root/.cloudflared/<UUID>.json

ingress:

  - hostname: icecast.roarradio.site
    service: http://127.0.0.1:8000

  - hostname: roarradio.site
    service: http://127.0.0.1:8001

  - service: http_status:404
```

```
cloudflared tunnel run radio
```

```
nano /etc/init.d/cloudflared
```

```
#!/sbin/openrc-run

name="cloudflared"
description="Cloudflare Tunnel"
command="/usr/bin/cloudflared"
command_args="tunnel --config /root/.cloudflared/config.yml run radio"
pidfile="/var/run/cloudflared.pid"
command_background="yes"

output_log="/var/log/cloudflared.log"
error_log="/var/log/cloudflared.err"

depend() {
    need docker
}

start_pre() {
    ebegin "Aguardando container pihole iniciar"
    until docker ps | grep -q pihole; do
        sleep 2
    done
    eend 0
}
```

```
chmod +x /etc/init.d/cloudflared
rc-update add cloudflared default
```

```
rc-service cloudflared start
```

# btop

```
apk add btop
```

# Finalizar!

```
apk update && apk upgrade
```

# Criar Stack do MeTube no Portainer

```
services:
  metube:
    image: ghcr.io/alexta69/metube
    container_name: metube
    restart: unless-stopped
    ports:
      - 8081:8081
    volumes:
      - /home/music:/downloads
```
