# Ansible - Tarea 5

**1. ¿Qué es Ansible? Mencione dos actividades que se puedan hacer con Ansible**  
Ansible es una herramienta de automatización de código abierto que se utiliza para la gestión de la configuración, el despliegue de aplicaciones y la orquestación de tareas. Es una herramienta de automatización de configuración y gestión de sistemas.

Dos actividades que se pueden realizar con Ansible son:

Despliegue de software.

Configuración de servidores.

**2. ¿Qué es un playbook de Ansible?**  
Un playbook de Ansible es un archivo escrito en formato YAML que define las tareas a ejecutar en uno o varios hosts. Los playbooks son la base para ejecutar automatizaciones complejas y pueden incluir una o más "plays".

**3. ¿Qué información contiene un inventario de Ansible?**  
Un inventario de Ansible es un archivo que contiene una lista de hosts y grupos de hosts, junto con variables asociadas como usuario, dirección IP o rutas. Define sobre qué hosts y grupos se ejecutarán las tareas.

**4. Explique qué es un módulo de Ansible y dé un ejemplo**  
Un módulo de Ansible es una unidad de funcionalidad autónoma que realiza una tarea específica en un host remoto. Los módulos son idempotentes, lo que significa que pueden ejecutarse varias veces sin cambiar el estado del sistema, a menos que sea necesario.

Un ejemplo de módulo es apt, que se utiliza para instalar, actualizar y eliminar paquetes en sistemas basados en Debian, como Ubuntu.

**5. ¿Qué ventajas tiene Ansible sobre otros métodos de automatización?**  
Ansible ofrece varias ventajas sobre otras herramientas de automatización:

Es agentless: No requiere la instalación de un agente en los hosts remotos, ya que utiliza SSH para la comunicación.

Sintaxis simple: Utiliza archivos YAML para sus playbooks, lo que facilita su lectura y escritura.

Es idempotente: Las tareas pueden ejecutarse repetidamente sin causar efectos secundarios no deseados.

Seguridad: Al usar SSH para la conexión, las comunicaciones son seguras por defecto.