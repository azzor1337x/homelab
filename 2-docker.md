
![iptables](https://img.shields.io/badge/iptables-firewall-important?logo=linux&logoColor=white)
![Docker](https://img.shields.io/badge/docker-installed-blue?logo=docker&logoColor=white)
![Docker Compose](https://img.shields.io/badge/docker--compose-configured-2496ED?logo=docker&logoColor=white)


---

## 🔥 Iptables

```bash
sudo apt install -y iptables
```

---

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

---

## 🐳 Docker Compose
📌 Fonte: https://docs.docker.com/compose/install/standalone/

```bash

sudo curl -SL https://github.com/docker/compose/releases/download/v2.35.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

sudo docker-compose version

```

---
