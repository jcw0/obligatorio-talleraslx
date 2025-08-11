# Obligatorio Taller de Servidores Linux

Repositorio para el obligatorio del **Taller de Servidores Linux** (Universidad ORT Uruguay).

## 📂 Estructura del repositorio
    .
        ├── ansible/

            ├── inventory.ini # Archivo de inventario

            ├── ansible.cfg # Configuración de Ansible

        ├── playbooks/

            ├── nfs_setup.yml # Playbook NFS (CentOS)

            └── hardening.yml # Playbook Hardening (Ubuntu)

        └── templates/ # Templates Jinja2

        ├── Documentos/

            ├── capturas/ # Evidencias gráficas

            ├── logs/ # Salidas de ejecución

        └── README_Documentos.md # Descripción de evidencias

        └── ansible_basics.md # Respuestas a Tarea 5
---

## 🚀 Ejecución de los playbooks

### 1️⃣ Verificar inventario

```bash
ansible-inventory -i ansible/inventory.ini --list
ansible all -i ansible/inventory.ini -m ping
```

### 2️⃣ Playbook NFS (CentOS)

Instala y configura NFS con firewall y directorio compartido:
```bash
ansible-playbook -i ansible/inventory.ini ansible/playbooks/nfs_setup.yml
```

### 3️⃣ Playbook Hardening (Ubuntu)

Actualiza, habilita UFW, asegura SSH y configura Fail2Ban:
```bash
ansible-playbook -i ansible/inventory.ini ansible/playbooks/hardening.yml
```

## Descripción de tareas

Tarea 1: Instalación de servidores CentOS y Ubuntu con configuración de red y SSH.

Tarea 2: Inventario Ansible con grupos y subgrupos.

Tarea 3: Comandos ad-hoc para usuarios, memoria y Chrony.

Tarea 4: Playbooks para NFS en CentOS y Hardening en Ubuntu.

Tarea 5: Preguntas teóricas sobre Ansible (ansible_basics.md).

## Requisitos

Ansible instalado en el nodo de control.

Acceso SSH con clave pública a los servidores.

Servidores CentOS Stream 9 y Ubuntu 24.04 con configuración de red según enunciado.

## Avances y evidencias

- [x] Creación y configuración de la VM Ubuntu 24.04
- [x] Creación y configuración de la VM CentOS Stream 9
- [x] Configuración de inventario y pruebas con Ansible
- [x] Creación y ejecución de playbooks
- [x] Respuestas teóricas y documentación final

## Cómo clonar el repositorio

```bash
git clone https://github.com/jcw0/obligatorio-talleraslx.git
```