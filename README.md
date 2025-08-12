# Obligatorio Taller Servidores Linux

## 📋 Requisitos previos

- **Ansible** instalado en el bastión/controller (`ansible --version` para verificar).
- Acceso SSH con **clave pública** desde el bastión a las VMs Ubuntu y CentOS.
- Inventario (`inventory.ini`) configurado con:
  - Grupos: `linux`, `ubuntu`, `centos`, `webserver`.
  - Variables `ansible_user` y `ansible_host` según corresponda.
- VMs listas:
  - **CentOS Stream 9**
  - **Ubuntu Server 24.04**
  - Especificaciones:
    - 1 CPU, 2 GB RAM
    - `/boot`: 1–2 GB
    - `/`: 10 GB
    - `/var`: 5 GB
    - Swap: 4 GB
    - 2 interfaces de red: NAT + Red interna

---

## 📂 Estructura del proyecto

```
obligatorio-taller-linux/
├── README.md
├── inventory.ini
├── playbooks/
│   ├── nfs_setup.yml
│   └── hardening.yml
└── Documentos/
    ├── Tarea3/
    ├── Tarea4_CentOS/
    ├── Tarea4_Ubuntu/
    └── Tarea5/
```

---

## ▶️ Ejecución de playbooks

### NFS (CentOS)
```bash
ansible-playbook -i inventory.ini playbooks/nfs_setup.yml
```

### Hardening (Ubuntu)
```bash
ansible-playbook -i inventory.ini playbooks/hardening.yml
```

---

## ✅ Validación de cada tarea

### **Tarea 3 – Comandos ad-hoc**

**Listar usuarios en Ubuntu**
```bash
ansible ubuntu -i inventory.ini -a "getent passwd"
```

**Uso de memoria en todos los servidores**
```bash
ansible all -i inventory.ini -a "free -h"
```

**chrony instalado y activo en CentOS**
```bash
ansible centos -i inventory.ini -m dnf -a "name=chrony state=present"
ansible centos -i inventory.ini -a "systemctl is-active chronyd"
ansible centos -i inventory.ini -a "chronyc tracking"
```

---

### **Tarea 4 – NFS en CentOS**

**Verificar servicio**
```bash
ansible centos -i inventory.ini -a "systemctl status nfs-server"
ansible centos -i inventory.ini -a "systemctl is-enabled nfs-server"
```

**Verificar export**
```bash
ansible centos -i inventory.ini -a "cat /etc/exports"
ansible centos -i inventory.ini -a "exportfs -v"
```

**Verificar permisos directorio**
```bash
ansible centos -i inventory.ini -a "ls -ld /var/nfs_shared"
```

**Verificar firewall**
```bash
ansible centos -i inventory.ini -a "firewall-cmd --list-ports"
ansible centos -i inventory.ini -a "firewall-cmd --list-services"
```

**Montaje desde Ubuntu**
```bash
sudo apt-get install -y nfs-common
sudo mkdir -p /mnt/nfs
sudo mount -t nfs -o nfsvers=4.2 centos1:/var/nfs_shared /mnt/nfs
df -h | grep nfs
```

---

### **Tarea 4 – Hardening en Ubuntu**

**Verificar UFW**
```bash
ansible ubuntu -i inventory.ini -a "ufw status verbose"
```

**Verificar configuración SSH**
```bash
ansible ubuntu -i inventory.ini -a "grep -E 'PermitRootLogin|PasswordAuthentication|PubkeyAuthentication' /etc/ssh/sshd_config"
```

**Verificar fail2ban**
```bash
ansible ubuntu -i inventory.ini -a "fail2ban-client status"
ansible ubuntu -i inventory.ini -a "fail2ban-client status sshd"
```

---

## 📚 Referencias

- [Guía configuración UFW](https://www.cyberciti.biz/faq/how-to-configure-firewall-with-ufw-on-ubuntu-20-04-lts/)
- [Configuración Netplan](https://netplan.io/)
- [Documentación oficial Ansible](https://docs.ansible.com/)

---

## 🛠 Handlers implementados

### En `nfs_setup.yml`
```yaml
handlers:
  - name: reload exports
    command: exportfs -r
```

### En `hardening.yml`
```yaml
handlers:
  - name: reboot system
    reboot:
  - name: restart ssh
    service:
      name: ssh
      state: restarted
```
