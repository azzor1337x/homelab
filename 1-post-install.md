
![root](https://img.shields.io/badge/root-superuser-red?logo=linux&logoColor=white)
![systemd](https://img.shields.io/badge/systemd-init%20system-blue?logo=linux&logoColor=white)
![resolv.conf](https://img.shields.io/badge/resolv.conf-DNS%20Config-orange?logo=gnu-linux&logoColor=white)

---

## 🚀 Primeiro boot(via terminal local)

Logar usando o usuario criado no momento da instalação e listar o IP:
```bash
ip -c a
```

---

## 🔐 Senha root e acesso(via PuTTY)

Após listar o IP, acesse via Putty usando o usuario criado no momento da instalação, defina uma senha root e acesse:
```bash
sudo passwd root
su
```

---

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

---

## 🌐 resolv.conf

```bash
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
```

Conteúdo:
```conf
nameserver 192.168.1.1
#nameserver 127.0.0.1
```

---

## 🌐 Ativar systemd-networkd

```bash
sudo systemctl enable --now systemd-networkd
sudo systemctl disable --now networking
sudo reboot
```

---
