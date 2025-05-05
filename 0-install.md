
![Debian 12](https://img.shields.io/badge/debian-12-lightgrey?logo=debian&logoColor=white)
![LVM](https://img.shields.io/badge/lvm-setup-purple)

---

## 📦 Pré-Instalação: Limpando o Disco

1. No instalador do Debian, pressione `Ctrl + Alt + F2` para abrir o terminal.
2. Execute:
   ```bash
   dd if=/dev/zero of=/dev/sda bs=1M count=10
   ```
3. Reinicie com `Ctrl + Alt + Del`.

---

## 🧱 Particionamento Manual com LVM

1. Selecione `Manual` no particionamento.
2. Crie uma nova partição primária com 119GB e defina como `physical volume for LVM`.
3. Configure o LVM:
   - Crie volume group: `homelab`
   - Crie logical volumes:
     - `root` → 10 GB
     - `home` → 10 GB
     - `var` → 10 GB
     - `opt` → 10 GB
     - `usr` → 10 GB
     - `swap` → 1 GB
4. Defina os pontos de montagem:

| Montagem | Tipo | Dispositivo |
|----------|------|-------------|
| `/`      | ext4 | `/dev/mapper/homelab-root` |
| `/home`  | ext4 | `/dev/mapper/homelab-home` |
| `/var`   | ext4 | `/dev/mapper/homelab-var` |
| `/opt`   | ext4 | `/dev/mapper/homelab-opt` |
| `/usr`   | ext4 | `/dev/mapper/homelab-opt` |
| `swap`     | swap | `/dev/mapper/homelab-swap` |

## 🐧 Sistema Operacional

- **Debian 12 Minimal** instalado com:
  - SSH Server
  - Standard System Utilities
    
---
