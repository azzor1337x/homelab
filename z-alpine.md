
# Editor de Texto

```
apk add nano
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

# Docker

```
rm -rf /etc/apk/repositories
nano /etc/apk/repositories
```

```
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

# Internet + DNS + Hostname

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
nameserver 127.0.0.1
```

```
rm -rf /etc/hosts
nano /etc/hosts
```

```
127.0.0.1       homelab homelab localhost.localdomain localhost
::1             localhost localhost.localdomain
```

```
ifdown eth0 && ifup eth0 && reboot
```

# btop

```
apk add btop
```

# fastfetch

```
apk add fastfetch
```

# Finalizar!

```
apk update && apk upgrade
```
