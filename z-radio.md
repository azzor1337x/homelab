
![Liquidsoap](https://img.shields.io/badge/liquidsoap-2.0-blue?logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0id2hpdGUiIGQ9Ik0xMiAwQzUuMzcgMCAwIDUuMzcgMCAxMnM1LjM3IDEyIDEyIDEyIDEyLTUuMzcgMTItMTJTMTguNjMgMCAxMiAwbTAgMS43NWM1LjYxIDAgMTAuMjUgNC42NCAxMC4yNSAxMC4yNVMxNy42MSAyMi4yNSAxMiAyMi4yNVMxLjc1IDE3LjYxIDEuNzUgMTIgNi4zOSAxLjc1IDEyIDEuNzVtLTEuNSA0LjV2MTIuNWgzdjEyLjVIMTJjLTQuMTQgMC03LjUtMy4zNi03LjUtNy41aDMuMjVjMCAyLjM2IDEuODkgNC4yNSA0LjI1IDQuMjVzNC4yNS0xLjg5IDQuMjUtNC4yNUgxOEMxOCA4LjgxIDE1LjE5IDYgMTIgNnYxLjVoLTF6Ii8+PC9zdmc+&logoColor=white)
![Icecast](https://img.shields.io/badge/icecast-2.4-blue?logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0id2hpdGUiIGQ9Ik0xMiAwQzUuMzcgMCAwIDUuMzcgMCAxMnM1LjM3IDEyIDEyIDEyIDEyLTUuMzcgMTItMTJTMTguNjMgMCAxMiAwbTAgMS43NWM1LjYxIDAgMTAuMjUgNC42NCAxMC4yNSAxMC4yNVMxNy42MSAyMi4yNSAxMiAyMi4yNVMxLjc1IDE3LjYxIDEuNzUgMTIgNi4zOSAxLjc1IDEyIDEuNzVtLTYuNSA0LjV2MTJoMTN2LTEySDUuNXptMS41IDEuNWg0djloLTR2LTl6bTYgMGg0djlIMTJ2LTl6Ii8+PC9zdmc+&logoColor=white)

---

## 🌐 Instalar Icecast2

```bash
sudo apt install -y icecast2
```

---

## 🎧 Liquidsoap

```bash
sudo mkdir -p /opt/liquidsoap
sudo nano /opt/liquidsoap/docker-compose.yml
```

## 📋 docker-compose.yml

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

```liq
radio_playlist = playlist(mode="normal", reload_mode="watch", "/home/music")
crossfaded = crossfade(fade_in=5.0, fade_out=5.0, radio_playlist)
crossfaded = amplify(0.5, crossfaded)
radio = fallback(track_sensitive = false, [crossfaded, blank()])
output.icecast(%mp3,
  host = "host.docker.internal",
  port = 8000,
  password = "hackme",
  mount = "/radio.mp3",
  name = "homelab",
  description = "radio",
  genre = "music",
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

