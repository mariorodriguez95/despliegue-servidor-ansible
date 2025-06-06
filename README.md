PROYECTO: INFRAESTRUCTURA CON ANSIBLE – DESPLIEGUE AUTOMATIZADO DE SERVIDORES WEB
-----------------------------------------------------------------------------------------------------------------------------------------------------------------
RESUMEN
Este proyecto consiste en la automatización del despliegue y configuración de servidores web Apache en dos nodos Linux utilizando Ansible desde un nodo controlador. Se configura el firewall, se garantiza que Apache esté activo y se personaliza la página web en cada nodo. El objetivo es facilitar la gestión y el mantenimiento de múltiples servidores con un solo playbook.

OBJETIVOS
Desplegar servidores web Apache en dos nodos Linux mediante Ansible.
Configurar el firewall para permitir el tráfico necesario.
Asegurar que el servicio Apache esté activo y habilitado.
Personalizar la página de inicio en cada servidor.

TECNOLOGÍAS UTILIZADAS
Sistemas Operativos: Ubuntu Zorin OS en los tres nodos
Herramientas: Ansible, Apache, UFW
Redes: Red interna con IPs estáticas para la conexión entre nodos
Automatización: Playbooks y roles de Ansible

PREPARACIÓN DEL ENTORNO

Crear tres máquinas virtuales con Ubuntu Zorin OS: un nodo controlador y dos nodos gestionados.

Configurar la red para que las máquinas puedan comunicarse entre sí (red interna o adaptador solo anfitrión).

Asignar IPs estáticas:
10.0.2.15 controlador
10.0.2.16 nodo1
10.0.2.17 nodo2

Añadir los nombres de host al archivo /etc/hosts en el controlador:
10.0.2.16 nodo1
10.0.2.17 nodo2

INSTALACIÓN Y CONFIGURACIÓN DE ANSIBLE EN EL NODO CONTROLADOR

Actualizar paquetes:
sudo apt update && sudo apt upgrade -y

Instalar Ansible:
sudo apt install ansible -y

Comprobar la versión instalada:
ansible --version

CONFIGURACIÓN SSH Y PREPARACIÓN DE NODOS GESTIONADOS

Generar clave SSH en el controlador:
ssh-keygen

Copiar la clave a los nodos gestionados:
ssh-copy-id usuario@nodo1
ssh-copy-id usuario@nodo2

Comprobar conexión SSH:
ssh usuario@nodo1
ssh usuario@nodo2

CONFIGURACIÓN DEL ARCHIVO DE INVENTARIO DE ANSIBLE

Editar el archivo /etc/ansible/hosts:
[webservers]
nodo1 ansible_host=10.0.2.16 ansible_user=usuario
nodo2 ansible_host=10.0.2.17 ansible_user=usuario

CREACIÓN DEL PLAYBOOK PARA INSTALAR APACHE

Crear un archivo llamado apache.yml con el siguiente contenido:

name: Instalar y configurar Apache
hosts: webservers
become: yes
tasks:

name: Instalar Apache
apt:
name: apache2
state: present
update_cache: yes

name: Habilitar y arrancar el servicio Apache
service:
name: apache2
state: started
enabled: yes

name: Permitir puerto 80 en UFW
ufw:
rule: allow
port: 80
proto: tcp

name: Personalizar página web
copy:
content: "Servidor web {{ inventory_hostname }}"
dest: /var/www/html/index.html

EJECUCIÓN DEL PLAYBOOK

Ejecutar el playbook:
ansible-playbook apache.yml

Verificar desde un navegador en el host que cada IP del nodo muestre su contenido correspondiente.

VALIDACIÓN DEL DESPLIEGUE

Acceder desde el navegador del host a:
http://10.0.2.16
http://10.0.2.17

Confirmar que cada nodo muestra el contenido personalizado de su propia página.

LECCIONES APRENDIDAS
Automatización: Este proyecto me permitió aprender a usar Ansible para automatizar tareas repetitivas y garantizar la coherencia en múltiples servidores.
Gestión de configuración: Comprendí la estructura de los playbooks y cómo aplicar configuraciones específicas a diferentes hosts.
Eficiencia operativa: La implementación demostró cómo reducir errores humanos y aumentar la eficiencia usando automatización.
Resolución de problemas: Aprendí a solucionar problemas comunes como errores de conexión SSH, sintaxis en los playbooks y permisos al ejecutar tareas con privilegios elevados.
