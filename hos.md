# Documentação do HOS - ISO Personalizada Debian

## Passos para preparar a customização

### 1. Criar estrutura de diretórios

```bash
mkdir -p hos-simple-cdd/profiles
cd hos-simple-cdd
```

### 2. Criar arquivo de perfil

Salve o seguinte conteúdo em `profiles/hos.profile`:

```ini
profile: hos
packages: openssh-server sudo curl ca-certificates net-tools iproute2
default_language: en_US
keyboard: us
timezone: UTC
user-fullname: root
user-uid: 1000
user-password: toor
```

#### Explicações

* **profile:** Define o nome do perfil usado na ISO. Deve corresponder ao nome do arquivo.
* **packages:** Lista os pacotes a serem instalados junto com o sistema base.

  * Pode adicionar pacotes como `git`, `vim`, `iptables`, `docker-ce`, etc.
  * Pode remover `net-tools` se quiser usar somente ferramentas modernas (ex: `ip` ao invés de `ifconfig`).
* **default\_language / keyboard / timezone:** Define idioma, layout de teclado e fuso horário padrão.
* **user-fullname / user-uid / user-password:** Cria um usuário automático com nome, UID e senha.

  * A senha está em texto puro; para maior segurança, use uma hash criptografada (ex: SHA-512).

### 3. Criar arquivo `preseed.cfg`

Salve o seguinte conteúdo na raiz como `preseed.cfg`:

```ini
d-i debian-installer/locale string en_US.UTF-8
d-i console-setup/ask_detect boolean false
d-i keyboard-configuration/xkb-keymap select us
d-i netcfg/choose_interface select auto
d-i netcfg/get_hostname string homelab
d-i netcfg/get_domain string local
d-i clock-setup/utc boolean true
d-i time/zone string UTC
d-i partman-auto/method string regular
d-i partman-auto/choose_recipe select atomic
d-i partman/confirm_write_new_label boolean true
d-i partman/confirm boolean true
d-i passwd/root-login boolean true
d-i passwd/make-user boolean false
d-i pkgsel/include string openssh-server sudo curl ca-certificates net-tools iproute2

# Comando de pós-instalação (opcional)
d-i preseed/late_command string \
  in-target curl -fsSL https://get.docker.com | sh ;
```

#### Explicações

* **locale / keyboard:** Define idioma e teclado.
* **netcfg:** Configura rede via DHCP, hostname e domínio.
* **clock-setup / time/zone:** Define uso de UTC e fuso horário.
* **partman:** Usa particionamento automático ("atomic").

  * Pode trocar "regular" por "lvm".
  * Pode adicionar criptografia com `method string crypto`.
* **passwd:** Habilita login root, sem usuário comum.
* **pkgsel/include:** Pacotes a instalar além da base mínima.
* **late\_command:** Roda comandos no sistema instalado (ex: instalar Docker).

### 4. Gerar a ISO personalizada

```bash
sudo apt install simple-cdd
build-simple-cdd --profiles hos --local-packages . --auto
```

### Resultado

A ISO final estará no diretório `images/`.

---

Você pode versionar tudo isso no GitHub, facilitando reproduções e modificações futuras.
