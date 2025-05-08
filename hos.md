Documentação do HOS - ISO Personalizada Debian

Arquivo: profiles/hos.profile

profile: hos

Define o nome do perfil usado na ISO. Deve corresponder ao nome do arquivo.

Pode ser alterado para qualquer nome que represente sua variante (ex: homelab-server).


packages: openssh-server sudo curl ca-certificates net-tools iproute2

Lista os pacotes a serem instalados junto com o sistema base.

Pode adicionar pacotes como git, vim, iptables, docker-ce, etc.

Pode remover net-tools se quiser usar somente ferramentas modernas (ip ao invés de ifconfig).


default_language: en_US
keyboard: us
timezone: UTC

Define idioma, layout de teclado e fuso horário padrão.

Pode ajustar conforme a localização desejada.


user-fullname: root
user-uid: 1000
user-password: toor

Cria um usuário automático com nome, UID e senha.

Pode remover essas linhas se quiser que o instalador pergunte por um usuário.

A senha está em texto puro; para maior segurança, use uma hash criptografada (ex: SHA-512).



---

Arquivo: preseed.cfg

d-i debian-installer/locale string en_US.UTF-8

Define o locale padrão do sistema.


d-i console-setup/ask_detect boolean false
d-i keyboard-configuration/xkb-keymap select us

Configura o teclado automaticamente


d-i netcfg/choose_interface select auto
d-i netcfg/get_hostname string homelab
d-i netcfg/get_domain string local

Rede via DHCP automática.

Define o nome do host e domínio local.

Pode definir IP estático com comandos extras ou via script de pós-instalação.


d-i clock-setup/utc boolean true
d-i time/zone string UTC

Define o fuso horário e uso de UTC para o relógio do sistema.


d-i partman-auto/method string regular
d-i partman-auto/choose_recipe select atomic
d-i partman/confirm_write_new_label boolean true
d-i partman/confirm boolean true

Usa particionamento automático (tudo em uma partição).

Pode trocar "regular" por "lvm" para usar LVM.

Pode adicionar criptografia: "method string crypto".


d-i passwd/root-login boolean true
d-i passwd/make-user boolean false

Habilita login direto como root.

Pode remover para forçar criação de usuário comum.


d-i pkgsel/include string openssh-server sudo curl ca-certificates net-tools iproute2

Instala pacotes extras após a base do sistema.

Redundante se já estiverem no .profile, mas útil para garantir instalação.


# Comando de pós-instalação (opcional)
d-i preseed/late_command string \
  in-target curl -fsSL https://get.docker.com | sh ;

Executa script após a instalação, dentro do sistema alvo.

Ideal para instalar Docker, Unbound, ou configurar o ambiente automaticamente.

Pode usar scripts locais com cp /cdrom/... /target/... && in-target bash /....


