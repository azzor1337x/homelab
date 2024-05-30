## Após a instalação do Ubuntu Server

```bash
#ainda no terminal, logar na conta criada na instalação
sudo passwd root #digite a senha criada na instalação
#defina uma senha para o root

sudo reboot

```
## Via SSH logar na conta criada

```bash
#logar na conta criada na instalação
su #digitar a senha para o root

cd /
cd
nano /etc/ssh/sshd_config #retire o # na frente da linha que consta PermitRootLogin prohibit-password e substitua por PermitRootLogin yes

sudo reboot

```
