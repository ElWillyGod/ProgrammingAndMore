
## Revicion 3

**Especificaciones**

Usted es administrador de sistemas Linux en Quasar Technologies, una empresa de servicios de TI que proporciona hosting gestionado e infraestructura de nube a los clientes. Usted es responsable de implementar cambios críticos de seguridad y configuración para garantizar el acceso remoto seguro, el montaje adecuado del sistema de archivos y la creación de informes web.

- La empresa requiere acceso seguro y sin contraseña de la máquina `serverb` a la máquina `servera` para las transferencias de datos automatizadas. El usuario `student` en la máquina `serverb` debe ser capaz de iniciar sesión en la máquina `servera` con SSH sin ingresar una contraseña.
    
    - En la máquina `serverb`, genere un par de llaves SSH para el usuario `student`. No proteja la clave privada con una frase de contraseña.
        
    - Envíe la clave pública del par de claves SSH recientemente generado al usuario `student` en la máquina `servera`.
        
    - Verifique que el usuario `student` pueda iniciar sesión en la máquina `servera` desde la máquina `serverb` sin ingresar una contraseña.
        
    
- Está investigando posibles problemas de permisos con el directorio `/user-homes/production5` en la máquina `servera`. Ha decidido atenuar SELinux para la depuración.
    
    - Verifique los permisos del directorio `/user-homes/production5`.
        
    - Configure SELinux para que se ejecute en el modo permissive (permisivo) de manera predeterminada.
        
    - Reinicie la máquina `servera` y confirme que SELinux esté en modo permissive (permisivo).
        
    
- La máquina `servera` exporta el directorio de inicio del usuario `production5` como la exportación de NFS `servera:/user-homes/production5`. Configure el servicio `autofs` para montar el recurso compartido de red en el directorio `/localhome/production5` en la máquina `serverb`.
    
    - Instale el paquete requerido para configurar el servicio `autofs`.
        
    - Configure el servicio `autofs` para montar la exportación NFS `servera:/user-homes/production5` en el directorio `/localhome/production5` en la máquina `serverb`.
        
    
- En la máquina `serverb`, ajuste el SELinux Boolean correspondiente para que el usuario `production5` pueda usar el directorio de inicio montado en NFS después de la autenticación con una llave SSH. Use `redhat` como contraseña del usuario `production5`.
    
    - En la máquina `servera`, con el usuario `production5`, genere un nuevo par de llaves SSH para la autenticación. Use `redhat` como contraseña para el usuario `production5`.
        
    - Transfiera la clave pública del par de claves SSH al usuario `production5` en la máquina `serverb`. Use `redhat` como contraseña para el usuario `production5`.
        
    - Use la autenticación basada en llave pública SSH en lugar de la autenticación basada en contraseña para iniciar sesión en la máquina `serverb` con el usuario `production5`. Verifique que el método falle.
        
    - Ajuste el SELinux Boolean `use_nfs_home_dirs` en la máquina `serverb` para permitir que NFS use directorios de inicio. Use la contraseña `redhat`.
        
    - Verifique y confirme que ahora puede usar la autenticación basada en llave pública SSH en lugar de la autenticación basada en contraseña para iniciar sesión en la máquina `serverb` con el usuario `production5`.
        
    
- Por razones de seguridad, debe bloquear todas las solicitudes de conexión de la máquina `servera` a la máquina `serverb`.
    
    - En la máquina `serverb`, ajuste la configuración del firewall para bloquear todas las solicitudes de conexión que se originan de la máquina `servera`. Use la dirección IPv4 `servera` (`172.25.250.10`) para configurar la regla de firewall.
        
    
- Un administrador junior configuró el servidor HTTP Apache en la máquina `serverb` con el fin de escuchar en el puerto 30080/TCP para las conexiones. Sin embargo, el servicio no puede iniciarse. Se le ha pedido que investigue y solucione este problema. También se le pide que ajuste la configuración del firewall según corresponda a fin de que el puerto 30080/TCP esté abierto para conexiones entrantes.
    
    - Intente reiniciar el servicio web. Inspeccione los archivos de registro adecuados para determinar el motivo de la falla del servicio, incluidas las posibles infracciones de la política de SELinux. Cuando se identifique, corrija el problema y asegúrese de que se inicie el servicio.
        
    - Agregue el puerto 30080/TCP a la zona `public` predeterminada para permitir conexiones entrantes.
        
    

Se brinda una posible solución para este ejercicio. Puede usar un método diferente para lograr el resultado especificado.

A fin de prepararse mejor para los desafíos del mundo real, es posible que la solución que se ofrece no incluya comandos, bloques de código o pasos para todas las tareas.

Si falla el script de calificación, el sistema muestra una sugerencia sobre el contenido que debe revisarse.

1. Inicie sesión en la máquina `serverb` con el usuario `student`.
    
2. Genere un par de claves SSH. No proteja la clave privada con una frase de contraseña.
    
    [student@serverb ~]$ **`ssh-keygen`**
    Generating public/private ed25519 key pair.
    Enter file in which to save the key (/home/student/.ssh/id_ed25519):
    Created directory '/home/student/.ssh'.
    Enter passphrase for "/home/student/.ssh/id_ed25519" (empty for no passphrase):
    Enter same passphrase again:
    _...output omitted..._
    
3. Envíe la clave pública del par de claves SSH recientemente generado al usuario `student` en la máquina `servera`.
    
    [student@serverb ~]$ **`ssh-copy-id -i ~/.ssh/id_ed25519.pub student@servera`**
    _...output omitted..._
    
4. Use la llave SSH recién creada para iniciar sesión en la máquina `servera` con el usuario `student`.
    
    [student@serverb ~]$ **`ssh student@servera`**
    _...output omitted..._
    Last login: Mon Jul 21 19:39:08 2025 from 172.25.250.11
    [student@servera ~]$
    
5. En la máquina `servera`, establezca el parámetro `SELINUX` con el valor `permissive` en el archivo `/etc/selinux/config`. Luego, reinicie el sistema con el comando `sudo systemctl reboot`.
    
    _...output omitted..._
    SELINUX=`permissive`
    _...output omitted..._
    
6. Con el usuario `root` en la máquina `serverb`, instale el paquete `autofs`, cree los archivos de asignación `autofs` y reinicie el servicio `autofs`.
    
    [root@serverb ~]# **`dnf install -y autofs && \ echo "/- /etc/auto.production5" > /etc/auto.master.d/production5.autofs`**
    _...output omitted..._
    Complete!
    [root@serverb ~]# **`getent passwd production5`**
    production5:x:5001:5001::`/localhome/production5`:/bin/bash
    [root@serverb ~]# **`echo \ "/localhome/production5 -rw servera.lab.example.com:/user-homes/production5" \ > /etc/auto.production5 && \ systemctl restart autofs`**
    
7. Abra una nueva ventana de terminal e inicie sesión en la máquina `servera` con el usuario `student`. Cambie al usuario `production5`, genere un par de llaves SSH sin una frase de contraseña y envíe el par de llaves SSH al usuario `production5` en la máquina `serverb`.
    
    [student@servera ~]$ **`su - production5`**
    Password: **`redhat`**
    [production5@servera ~]$ **`ssh-keygen`**
    _...output omitted..._
    The key's randomart image is:
    +--[ED25519 256]--+
    |      ..E=+=.. . |
    |       +.@=o+ X  |
    |     o..Oo=o * o |
    |    o.=o+oo o .  |
    |   . .o*S. o o   |
    |    ....    o    |
    |    ..     . .   |
    |     o.     .    |
    |    ...          |
    +----[SHA256]-----+
    [production5@servera ~]$ **`ssh-copy-id production5@serverb`**
    _...output omitted..._
    Number of key(s) added: 1
    
    Now try logging into the machine, with: "ssh 'production5@serverb'"
    and check to make sure that only the key(s) you wanted were added.
    
    Salga y cierre el terminal que está conectado a la máquina `servera` con el usuario `production5`. Mantenga abierto el terminal que está conectado a la máquina `serverb` con el usuario `root`.
    
8. Con el usuario `root` en la máquina `serverb`, establezca el SELinux Boolean `use_nfs_home_dirs` en `true`.
    
    [root@serverb ~]# **`setsebool -P use_nfs_home_dirs true`**
    
9. En la máquina `serverb`, ajuste y vuelva a cargar la configuración del firewall para bloquear todas las solicitudes de conexión que se originan de la máquina `servera`.
    
    [root@serverb ~]# **`firewall-cmd --add-source=172.25.250.10/32 \ --zone=block --permanent && \ firewall-cmd --reload`**
    success
    success
    
10. En la máquina `serverb`, use los comandos `systemctl restart` y `systemctl status` en la unidad `httpd.service` para investigar el problema con el servidor HTTP de Apache. Luego, use el comando `sealert -a /var/log/audit/audit.log` para determinar si una política de SELinux está impidiendo que el servicio `httpd` se vincule con el puerto 30080/TCP.
    
    [root@serverb ~]# **`systemctl restart httpd.service`**
    Job for httpd.service failed because the control process exited with error code.
    See "systemctl status httpd.service" and "journalctl -xeu httpd.service" for details.
    [root@serverb ~]# **`systemctl status httpd.service`**
    × httpd.service - The Apache HTTP Server
         Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
         Active: failed (Result: exit-code) since Mon 2025-07-21 19:49:23 UTC; 25s ago
    _...output omitted..._
    Jul 21 19:49:23 serverb systemd[1]: Starting httpd.service - The Apache HTTP Server...
    Jul 21 19:49:23 serverb (httpd)[3077]: httpd.service: Referenced but unset environment variable evaluates >
    Jul 21 19:49:23 serverb httpd[3077]: (13)Permission denied: AH00072: make_sock: could not bind to address >
    Jul 21 19:49:23 serverb httpd[3077]: (13)Permission denied: AH00072: make_sock: could not bind to address >
    Jul 21 19:49:23 serverb httpd[3077]: no listening sockets available, shutting down
    Jul 21 19:49:23 serverb httpd[3077]: AH00015: Unable to open logs
    Jul 21 19:49:23 serverb systemd[1]: httpd.service: Main process exited, code=exited, status=1/FAILURE
    Jul 21 19:49:23 serverb systemd[1]: httpd.service: Failed with result 'exit-code'.
    Jul 21 19:49:23 serverb systemd[1]: Failed to start httpd.service - The Apache HTTP Server.
    
11. Use los siguientes comandos para establecer el contexto de SELinux adecuado, reiniciar el servicio `httpd` y actualizar las reglas de firewall.
    
    [root@serverb ~]# **`semanage port -a -t http_port_t -p tcp 30080`**
    [root@serverb ~]# **`systemctl restart httpd`**
    [root@serverb ~]# **`firewall-cmd --add-port=30080/tcp --permanent`**
    success
    [root@serverb ~]# **`firewall-cmd --reload`**
    success
    

[](https://rol.redhat.com/rol/app/#)