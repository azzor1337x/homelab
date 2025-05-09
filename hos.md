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
install-recommends: false
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














# Customização Visual da ISO - HomelabOS

## Objetivo

Personalizar a aparência da ISO gerada com Simple-CDD, incluindo:

* Nome do sistema (branding)
* Mensagem de boas-vindas (motd)
* Tela de boot (ISOLINUX/GRUB)
* Nome do arquivo ISO final

---

## 1. Renomear a ISO gerada

Após rodar o build:

```bash
mv images/debian-*.iso images/homelabos.iso
```

---

## 2. Adicionar mensagem de boas-vindas (motd)

Crie o arquivo `motd` com seu texto personalizado:

```bash
echo 'Bem-vindo ao HomelabOS - Debian minimal customizado para homelabs' > motd
```

Inclua no `late_command` do `preseed.cfg`:

```ini
d-i preseed/late_command string \
  in-target curl -fsSL https://get.docker.com | sh ; \
  echo "Bem-vindo ao HomelabOS - Debian minimal customizado para homelabs" > /target/etc/motd ;
```

---

## 3. Personalizar /etc/os-release

No mesmo `late_command`, adicione:

```bash
in-target sed -i 's/^NAME=.*/NAME="HomelabOS"/' /etc/os-release
in-target sed -i 's/^PRETTY_NAME=.*/PRETTY_NAME="HomelabOS (Debian Based)"/' /etc/os-release
```

Ou substitua todo o arquivo com um customizado:

```bash
echo 'NAME="HomelabOS"
VERSION="1.0"
ID=homelabos
PRETTY_NAME="HomelabOS (Debian Based)"
HOME_URL="https://github.com/seuusuario/homelabos"
' > os-release

cp os-release /target/etc/os-release
```

---

## 4. Customizar tela de boot (avancado)

### a) Montar ISO para edição

```bash
mkdir iso-root
sudo mount -o loop images/homelabos.iso iso-root
cp -r iso-root custom-iso
sudo umount iso-root
```

### b) Alterar ISOLINUX (modo BIOS)

Dentro de `custom-iso/isolinux/isolinux.cfg`, altere:

```cfg
UI gfxboot bootlogo
DEFAULT install
PROMPT 0
TIMEOUT 50
LABEL install
  menu label ^Install HomelabOS
```

Pode substituir o `bootlogo` com imagem personalizada (formato .cfg ou .rle dependendo da config).

### c) Reempacotar ISO

```bash
cd custom-iso
sudo apt install xorriso isolinux syslinux-common
xorriso -as mkisofs -o ../homelabos-custom.iso \
  -isohybrid-mbr /usr/lib/ISOLINUX/isohdpfx.bin \
  -c isolinux/boot.cat -b isolinux/isolinux.bin \
  -no-emul-boot -boot-load-size 4 -boot-info-table \
  -V "HOMELABOS" .
```

---

## Resultado

Uma ISO com identidade visual personalizada:

* Nome e descrição na inicialização
* Tela de boot com "HomelabOS"
* Motd e /etc/os-release com branding
* Nome do arquivo: `homelabos-custom.iso`

Pronto para gravar no USB e instalar com estilo!

