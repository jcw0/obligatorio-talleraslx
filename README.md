# Obligatorio Taller Servidores Linux

## 🎯 Objetivo

Implementar y documentar la configuración de servicios en servidores **Ubuntu Server 24.04** y **CentOS Stream 9** utilizando **Ansible** como herramienta de automatización.  
El proyecto incluye:  
- Configuración de red e inventario Ansible.  
- Ejecución de playbooks para instalación y configuración de servicios.  
- Aplicación de medidas de hardening y seguridad.  
- Verificación y validación mediante comandos ad-hoc.  
- Registro de evidencias mediante capturas de pantalla y logs.

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
├── ansible_basics.md           
└── Documentos/                 
    ├── Tarea1/                
    ├── Tarea2/                 
    ├── Tarea3/                 
    ├── Tarea4_CentOS/          
    ├── Tarea4_Ubuntu/          
    └── Tarea5/                 

```

---


## ▶️ Ejecución de playbooks

### 🌐 Nota sobre redes e inventario (macOS + VMware Fusion)

En mi entorno local (macOS con VMware Fusion) utilizo **IPs de NAT** para conectar por SSH a las VMs debido a problemas de conectividad con la red interna/host-only.  
Esto quedó evidenciado en las capturas incluidas.  
Para la evaluación en otro entorno, basta con reemplazar en `inventory.ini` las IPs NAT por las IPs internas reales de cada VM.

Ejemplo de inventario con placeholders:
```ini
[ubuntu]
ubuntu1 ansible_host=IP_UBUNTU ansible_user=user1

[centos]
centos1 ansible_host=IP_CENTOS ansible_user=user1

[linux:children]
ubuntu
centos

[webserver]
centos1
```

Para obtener las IPs en cada VM:
```bash
ip a
```

Verificación del inventario:
```bash
ansible-inventory -i inventory.ini --list
ansible all -i inventory.ini -m ping
```

> **Nota personal:** en mi máquina sigo usando IPs NAT por la limitación mencionada. Esto no afecta la portabilidad: basta con que en `inventory.ini` se reemplacen los placeholders por las IPs que correspondan al entorno donde se ejecute.

---

### Ejecución de Playbooks

#### NFS en CentOS
```bash
ansible-playbook -i inventory.ini playbooks/nfs_setup.yml -K
```
> El `-K` es necesario porque varias tareas requieren privilegios (ej. modificar firewall, montar directorios exportados).

#### Hardening en Ubuntu
```bash
ansible-playbook -i inventory.ini playbooks/hardening.yml -K
```
> El `-K` es necesario para tareas como configurar UFW, reiniciar `sshd` y administrar Fail2ban.

---

### Comandos de verificación posteriores a los playbooks

#### NFS (CentOS)
- **Estado del servicio**:
```bash
ansible centos -i inventory.ini -a "systemctl status nfs-server"
```
- **Export y permisos (requiere sudo)**:
```bash
ansible centos -i inventory.ini -b -K -a "cat /etc/exports"
ansible centos -i inventory.ini -b -K -a "exportfs -v"
ansible centos -i inventory.ini -b -K -a "ls -ld /var/nfs_shared"
```
- **Firewall (requiere sudo)**:
```bash
ansible centos -i inventory.ini -b -K -a "firewall-cmd --list-ports"
ansible centos -i inventory.ini -b -K -a "firewall-cmd --list-services"
```

#### Hardening (Ubuntu)
- **UFW**:
```bash
ansible ubuntu -i inventory.ini -b -K -a "ufw status verbose"
```
- **SSH config**:
```bash
ansible ubuntu -i inventory.ini -a "grep -E 'PermitRootLogin|PasswordAuthentication' /etc/ssh/sshd_config"
```
- **Fail2ban**:
```bash
ansible ubuntu -i inventory.ini -b -K -a "fail2ban-client status"
ansible ubuntu -i inventory.ini -b -K -a "fail2ban-client status sshd"
```
- **Historial de actualizaciones**:
```bash
ansible ubuntu -i inventory.ini -m shell -b -K -a "grep -E '^(Start-Date|Upgrade:)' /var/log/apt/history.log | tail -n 20"
```


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
---

## 📚 Referencias

- **Ansible Documentation** – [https://docs.ansible.com](https://docs.ansible.com)  
- **Ansible ad-hoc commands** – [https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html)  
- **UFW (Uncomplicated Firewall)** – [https://help.ubuntu.com/community/UFW](https://help.ubuntu.com/community/UFW)  
- **Fail2ban** – [https://www.fail2ban.org/](https://www.fail2ban.org/)  
- **Chrony** – [https://chrony.tuxfamily.org/](https://chrony.tuxfamily.org/)  
- **NFS (Network File System)** – [https://wiki.archlinux.org/title/NFS](https://wiki.archlinux.org/title/NFS)  
- **Firewalld** – [https://firewalld.org/documentation/](https://firewalld.org/documentation/)  
- **VMware Fusion** – [https://www.vmware.com/products/fusion.html](https://www.vmware.com/products/fusion.html)  
- **Netplan** – [https://netplan.io/examples/](https://netplan.io/examples/)

---