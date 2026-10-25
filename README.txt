=====================================================================
 ASO - Grupo 2 - Tareas 3, 4 y 5
 Despliegue remoto LAMP + phpMyAdmin + Samba + FTP + PostgreSQL
 con Ansible (roles) sobre openSUSE Leap 16.0
=====================================================================

a) REPOSITORIO GITHUB
---------------------
https://github.com/alis-lia/ansible-tasks-3-4-5
(Compartido con gcussi@yahoo.com)

b) PASOS PARA EJECUTAR EL PROYECTO
----------------------------------
Requisitos:
  - CONTROL: openSUSE Leap 16.0 con ansible, sshpass y git
        zypper in -y ansible sshpass git
  - DESTINO: openSUSE Leap 16.0 LIMPIO (sin aplicaciones), con SSH para
    root habilitado y acceso a internet.

1. Clonar el repositorio:
     git clone https://github.com/alis-lia/ansible-tasks-3-4-5.git
     cd ansible-tasks-3-4-5

2. Configurar el archivo hosts: poner la IP del DESTINO en
     inventory/hosts.yml   (campo ansible_host)

3. Ejecutar:
     ansible-playbook site.yml -k --ask-vault-pass

   Pedira dos contrasenas:
     SSH password   -> contrasena de root del DESTINO
     Vault password -> se entrega junto al README en Classroom

   Opcional, ejecutar solo una parte (tags: lamp, apache, mariadb,
   phpmyadmin, samba, ftp, postgresql):
     ansible-playbook site.yml -k --ask-vault-pass --tags samba

Verificacion (desde otra maquina de la red):
   - Apache/PHP:   http://IP_DESTINO/
   - phpMyAdmin:   http://IP_DESTINO/phpmyadmin   (usuario aso_user)
   - Samba:        smbclient //IP_DESTINO/compartido -U alumno
   - FTP:          ftp IP_DESTINO                 (usuario alumno)
   - PostgreSQL:   psql "host=IP_DESTINO dbname=aso_pgdb user=aso_pg sslmode=require"

Nota: si Ansible avisa que ignora ansible.cfg por estar en un directorio
con permisos de escritura para todos, ejecutar: chmod 755 .

ESTRUCTURA
----------
ansible.cfg              Configuracion (pipelining, forks, tiempos por tarea)
inventory/hosts.yml      Servidores (sin usuario ni contrasena)
group_vars/all/          Variables y vault cifrado
site.yml                 Playbook principal
roles/
  apache_php/            Tarea 3: Apache + PHP
  mariadb/               Tarea 3: configuracion de MariaDB
  phpmyadmin/            Tarea 3: phpMyAdmin
  samba/                 Tarea 3: usuario de sistema, usuario Samba,
                                  directorio y smb.conf (create mask)
  vsftpd/                Tarea 3: FTP (plantilla vsftpd.conf)
  postgresql/            Tarea 5: PostgreSQL, php8-pgsql, usuario, base,
                                  conexion remota cifrada (SSL)

MEJORAS APLICADAS
-----------------
- Inventario sin usuario/contrasena visible: solo IPs; la contrasena SSH
  se pide al ejecutar (-k) y los secretos estan cifrados con Ansible Vault.
- Tiempo de respuesta: pipelining, ControlPersist, forks=10,
  gather_facts desactivado, paquetes instalados en una sola transaccion,
  y sin "zypper update" completo. El callback profile_tasks muestra
  cuanto tarda cada tarea.
- Roles independientes y reutilizables, con plantillas Jinja2.
