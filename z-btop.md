
---

## ✅ Desativar terminal local e ativar btop

```bash
sudo nano /etc/systemd/system/btop-tty.service
```

Cole o seguinte conteúdo:

```ini
[Unit]
Description=btop
After=multi-user.target docker.service
Requires=docker.service

[Service]
ExecStartPre=/bin/sleep 20
ExecStart=/usr/bin/btop
StandardInput=tty
StandardOutput=tty
TTYPath=/dev/tty1
TTYReset=yes
TTYVHangup=yes
KillMode=process
Restart=always
User=root
WorkingDirectory=/root
CapabilityBoundingSet=CAP_SYS_ADMIN CAP_SYS_PTRACE
AmbientCapabilities=CAP_SYS_ADMIN CAP_SYS_PTRACE
ProtectKernelModules=no
ProtectControlGroups=no
ProcSubset=all

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl disable getty@tty1.service
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable btop-tty.service
sudo systemctl start btop-tty.service
sudo reboot
```

---

## ✅ Serviço Cloudflared

```bash
sudo nano /etc/systemd/system/cloudflared.service
```

```bash

ExecStartPre=/bin/sleep 10

```

---

## ✅ Testes Finais

```bash
ip -c a
hostname -I
docker ps -a
docker images
docker network ls
docker volume ls
docker compose ls
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
journalctl -p err..warning
systemctl list-units --type=service --all
```

---
