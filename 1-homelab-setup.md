
![Docker](https://img.shields.io/badge/docker-installed-blue?logo=docker&logoColor=white)
![Docker Compose](https://img.shields.io/badge/docker--compose-configured-2496ED?logo=docker&logoColor=white)
![Unbound](https://img.shields.io/badge/unbound-dns--resolver-yellow?logo=gnubash&logoColor=black)
![Pi-hole](https://img.shields.io/badge/pi--hole-dns%20%2F%20dhcp-red?logo=pi-hole&logoColor=white)
![Portainer](https://img.shields.io/badge/portainer-docker--ui-0db7ed?logo=portainer&logoColor=white)

---

## 🚀 Primeiro boot(via terminal local)

# 1

### 3

```bash
ip -c a
```

---

## 🔐 Usando o IP acima, acesse Senha root e Timezone - Via PuTTY

```bash
sudo passwd root
su
```

---

## 🌐 IP Fixo (systemd-networkd)

```bash
sudo nano /etc/udev/rules.d/10-network.rules
```

```ini
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:e0:4c:68:00:94", NAME="eth0"
```

```bash
sudo nano /etc/systemd/network/10-wired.network
```

```ini
[Match]
Name=eth0

[Network]
Address=192.168.1.99/24
Gateway=192.168.1.1
DNS=192.168.1.1
#DNS=127.0.0.1
```

---

## 🔧 resolv.conf

```bash
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
```

```conf
nameserver 192.168.1.1
#nameserver 127.0.0.1
```

---

## ⚙️ Ativar systemd-networkd

```bash
sudo systemctl enable --now systemd-networkd
sudo systemctl disable --now networking
sudo reboot
```

---

## 🐳 Instalar Docker + Docker Compose

```bash
sudo apt install -y iptables btop
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove -y $pkg; done
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/debian $(. /etc/os-release && echo \"$VERSION_CODENAME\") stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -sf /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose version
```

---

## 🔐 Configurar Unbound como DNS Recursivo

```bash
sudo apt install unbound -y
sudo wget https://www.internic.net/domain/named.root -qO /var/lib/unbound/root.hints
sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
```

Conteúdo:
```conf
server:
    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    do-ip6: yes
    prefer-ip6: no
    harden-glue: yes
    harden-dnssec-stripped: yes
    use-caps-for-id: no
    prefetch: yes
    num-threads: 1
    so-rcvbuf: 1m
    edns-buffer-size: 1232
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
sudo systemctl restart unbound
dig pi-hole.net @127.0.0.1 -p 5335
```

---

## ❌ Instalar Pi-hole com Docker

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

Use como DNS no Pi-hole: `127.0.0.1#5335`

---

Comentar #DNS=192.168.1.1 e descomentar: `127.0.0.1`

```bash
sudo nano /etc/systemd/network/10-wired.network
```

Comentar #nameserver 192.168.1.1 e descomentar: `127.0.0.1`

```bash
sudo nano /etc/resolv.conf
```
---

## ⚙️ Ultimo Reboot

```bash
sudo reboot
```

---

## 📊 Instalar Portainer

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

---
