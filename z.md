## 📦 Pré-Instalação: Limpando o Disco

1. No instalador do Debian, pressione `Ctrl + Alt + F2` para abrir o terminal.
2. Execute:
   ```bash
   dd if=/dev/zero of=/dev/sda bs=1M count=10
   ```
3. Reinicie com `Ctrl + Alt + Del`.

## 🧱 Particionamento Manual com LVM

1. Selecione `Manual` no particionamento.
2. Crie uma nova partição primária com 119GB e defina como `physical volume for LVM`.
3. Configure o LVM:
   - Crie volume group: `homelab`
   - Crie logical volumes:
     - `root` → 10 GB
     - `home` → 10 GB
     - `var` → 10 GB
     - `opt` → 10 GB
     - `usr` → 10 GB
     - `swap` → 1 GB
4. Defina os pontos de montagem:

| Montagem | Tipo | Dispositivo |
|----------|------|-------------|
| `/`      | ext4 | `/dev/mapper/homelab-root` |
| `/home`  | ext4 | `/dev/mapper/homelab-home` |
| `/var`   | ext4 | `/dev/mapper/homelab-var` |
| `/opt`   | ext4 | `/dev/mapper/homelab-opt` |
| `/usr`   | ext4 | `/dev/mapper/homelab-usr` |
| `swap`     | swap | `/dev/mapper/homelab-swap` |

## 🐧 Sistema Operacional

- **Debian 12 Minimal** instalado com:
  - SSH Server
  - Standard System Utilities

## 🚀 Primeiro boot(via terminal local)

Logar usando o usuario criado no momento da instalação e listar o IP:
```bash
ip -c a
```

## 🔐 Senha root e acesso(via PuTTY)

Após listar o IP, acesse via Putty usando o usuario criado no momento da instalação, defina uma senha root e acesse:
```bash
sudo passwd root
su
```

## 🌐 IP Fixo (systemd-networkd)

```bash
sudo nano /etc/udev/rules.d/10-network.rules
```

Conteúdo:
```ini
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:e0:4c:68:00:94", NAME="eth0"
```

```bash
sudo nano /etc/systemd/network/10-wired.network
```

Conteúdo:
```ini
[Match]
Name=eth0

[Network]
Address=192.168.1.99/24
Gateway=192.168.1.1
DNS=192.168.1.1
#DNS=127.0.0.1
```

## 🌐 DNS (resolv.conf)

```bash
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
```

Conteúdo:
```conf
nameserver 192.168.1.1
#nameserver 127.0.0.1
```

## 🌐 Ativar systemd-networkd

```bash
sudo systemctl enable --now systemd-networkd
sudo systemctl disable --now networking
sudo reboot
```

## 🔥 Iptables

```bash
sudo apt install -y iptables
```

## 🐳 Docker
📌 Fonte: https://docs.docker.com/engine/install/debian/

```bash

for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update

sudo apt-get install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo docker run hello-world

```

## 🐳 Docker Compose
📌 Fonte: https://docs.docker.com/compose/install/standalone/

```bash

sudo curl -SL https://github.com/docker/compose/releases/download/v2.35.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

sudo docker-compose version

```

## 🌐 Unbound
📌 Fonte: https://docs.pi-hole.net/guides/dns/unbound/

```bash

sudo apt install -y unbound

wget https://www.internic.net/domain/named.root -qO- | sudo tee /var/lib/unbound/root.hints

sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf

```

Conteúdo:
```conf
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

Reinicie e teste:
```bash
sudo service unbound restart
dig pi-hole.net @127.0.0.1 -p 5335
```

## ❌ Docker Pi-hole
📌 Fonte: https://github.com/pi-hole/docker-pi-hole

```bash
sudo mkdir -p /opt/pihole
sudo nano /opt/pihole/docker-compose.yml
```

```yaml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    network_mode: host
    environment:
      TZ: 'America/Sao_Paulo'
      WEBPASSWORD: ''
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```

```bash
cd /opt/pihole
sudo docker-compose up -d
```

```bash
sudo docker exec -it pihole pihole setpassword
```

Apos acessar o pihole com a nova senha, desabilite o dns cadastrado e use este: `127.0.0.1#5335`

## 🌐 Atualizar systemd-networkd e resolv.conf

Comentar #DNS=192.168.1.1 e descomentar: `127.0.0.1`
```bash
sudo nano /etc/systemd/network/10-wired.network
```

Comentar #nameserver 192.168.1.1 e descomentar: `127.0.0.1`
```bash
sudo nano /etc/resolv.conf
```

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

## 📁 imagens.png

```bash
screenshot-mobile.png
screenshot-desktop.png
icon-512.png
icon-192.png
```

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

## 📡 Icecast2

```bash
sudo apt install -y icecast2
```

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

## ☁️ Cloudflared Tunnel

### Instalar Cloudflared

```bash
sudo mkdir -p /opt/cloudflared
cd /opt/cloudflared
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

  - hostname: metube.azzor1337x.shop
    service: http://127.0.0.1:8088

  - hostname: azzor1337x.shop
    service: http://127.0.0.1:8089

  - hostname: portainer.azzor1337x.shop
    service: http://127.0.0.1:9000

  - service: http_status:404
```

## 🚀 Iniciar o túnel

```bash
sudo cloudflared tunnel run blog
```

Ou instale como serviço:

```bash
sudo cloudflared service install
```

## 📊 Timezone

```bash
sudo timedatectl set-timezone America/Sao_Paulo
```

## 📁 Filebrowser

```bash
sudo mkdir -p /opt/filebrowser
cd /opt/filebrowser
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
filebrowser -a 0.0.0.0 -p 8888 -r /
```

## 📊 Portainer

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

## 📺 MeTube

```bash
sudo mkdir -p /opt/metube
sudo nano /opt/metube/docker-compose.yml
```

```yaml
services:
  metube:
    image: ghcr.io/alexta69/metube
    container_name: metube
    restart: unless-stopped
    ports:
      - "8088:8088"
    volumes:
      - /home/homelab/music:/downloads
```

## ⚙️ Criar pasta das musicas

```bash
sudo mkdir -p /home/homelab/music
```

```bash
cd /opt/metube
sudo docker-compose up -d
```

## 🎧 Navidrome

```bash
sudo mkdir -p /opt/navidrome
sudo nano /opt/navidrome/docker-compose.yml
```

```yaml
services:
  navidrome:
    image: deluan/navidrome:latest
    container_name: navidrome
    ports:
      - "4533:4533"
    restart: unless-stopped
    volumes:
      - "/opt/navidrome/data:/data"
      - "/home/homelab/music:/music:ro"
```

```bash
cd /opt/navidrome
sudo docker-compose up -d
```
