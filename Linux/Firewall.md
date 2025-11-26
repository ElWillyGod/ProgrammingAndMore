## Ejercicio guiado: Etiquetado de puertos de SELinux

Verificar y ajustar las etiquetas de SELinux en puertos de red específicos para que los servicios de red permitidos puedan vincularse a esos puertos y escuchar las conexiones de los clientes.

**Resultados**

- Configurar un servidor web para alojar contenido por medio de un puerto no estándar.
    

Con el usuario `student` en la máquina `workstation`, ejecute el siguiente comando `lab` a fin de preparar su sistema para este ejercicio y garantizar que estén disponibles todos los recursos requeridos:

student@workstation:~$ **`lab start netsecurity-ports`**

**Instrucciones**

Su organización está implementando una nueva aplicación web personalizada que se ejecuta en un puerto no estándar, el puerto 82/TCP.

Un administrador junior ya ha configurado la aplicación en la máquina `servera`. Sin embargo, no se puede acceder al contenido del servidor web. Resuelva el problema de acceso al servidor web.

1. Inicie sesión en la máquina `servera` con el usuario `student` y cambie al usuario `root`. Use la contraseña `student`.
    
    student@workstation:~$ **`ssh student@servera`**
    _...output omitted..._
    [student@servera ~]$ **`sudo -i`**
    [sudo] password for student: `student`
    [root@servera ~]#
    
2. Intente corregir el problema del contenido web al reiniciar el servicio `httpd`.
    
    1. Reinicie el servicio `httpd`. Este comando falla.
        
        [root@servera ~]# **`systemctl restart httpd`**
        Job for httpd.service failed because the control process exited with error code.
        See "systemctl status httpd.service" and "journalctl -xeu httpd.service" for details.
        
    2. Visualice el estado del servicio `httpd`. Observe el error `permission denied`. Presione **Q** para salir de la sesión.
        
        [root@servera ~]# **`systemctl status -l httpd`**
        × httpd.service - The Apache HTTP Server
             Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
             Active: `failed` (Result: exit-code) since Sun 2025-06-29 20:27:35 UTC; 56s ago
         Invocation: 70d8f71da2694aba94b4f0a2612d9e2e
               Docs: man:httpd.service(8)
            Process: 5998 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
           Main PID: 5998 (code=exited, status=1/FAILURE)
             Status: "Reading configuration..."
           Mem peak: 3.8M
                CPU: 54ms
        
        Jun 29 20:27:35 servera systemd[1]: Starting httpd.service - The Apache HTTP Server...
        Jun 29 20:27:35 servera (httpd)[5998]: httpd.service: Referenced but unset environment variable evaluates to an empty string: OPTIONS
        Jun 29 20:27:35 servera httpd[5998]: (13)`Permission denied: AH00072: make_sock: could not bind to address [::]:82`
        Jun 29 20:27:35 servera httpd[5998]: (13)`Permission denied: AH00072: make_sock: could not bind to address 0.0.0.0:82`
        Jun 29 20:27:35 servera httpd[5998]: `no listening sockets available, shutting down`
        Jun 29 20:27:35 servera httpd[5998]: AH00015: Unable to open logs
        Jun 29 20:27:35 servera systemd[1]: httpd.service: Main process exited, code=exited, status=1/FAILURE
        Jun 29 20:27:35 servera systemd[1]: httpd.service: Failed with result 'exit-code'.
        Jun 29 20:27:35 servera systemd[1]: `Failed to start httpd.service - The Apache HTTP Server.`
        
    3. Verifique si SELinux está impidiendo que el servicio `httpd` haga referencia al puerto 82/TCP.
        
        [root@servera ~]# **`sealert -a /var/log/audit/audit.log`**
        100% done
        found 1 alerts in /var/log/audit/audit.log
        --------------------------------------------------------------------------------
        
        `SELinux is preventing /usr/sbin/httpd from name_bind access on the tcp_socket port 82.`
        
        *****  Plugin bind_ports (99.5 confidence) suggests   ************************
        
        If you want to allow /usr/sbin/httpd to bind to network port 82
        Then you need to modify the port type.
        Do
        `# semanage port -a -t PORT_TYPE -p tcp 82     where PORT_TYPE is one of the following: http_cache_port_t, http_port_t, jboss_management_port_t, jboss_messaging_port_t, ntop_port_t, puppet_port_t.`
        _...output omitted..._
        Raw Audit Messages
        type=AVC msg=audit(1751228855.940:1271): avc:  denied  { name_bind } for  pid=5998 comm="httpd" src=82 scontext=system_u:system_r:httpd_t:s0 tcontext=system_u:object_r:reserved_port_t:s0 tclass=tcp_socket permissive=0
        
        _...output omitted..._
        
3. Configure SELinux para que permita que el servicio `httpd` se enlace con el puerto 82/TCP y luego reinicie el servicio `httpd`.
    
    1. Encuentre un tipo de puerto apropiado para el puerto 82/TCP.
        
        La etiqueta de contexto `http_port_t` incluye los puertos predefinidos 80/TCP y 443/TCP para HTTP. Este tipo es el tipo de puerto correcto para el servidor web.
        
        [root@servera ~]# **`semanage port -l | grep http`**
        http_cache_port_t              tcp      8080, 8118, 8123, 10001-10010
        http_cache_port_t              udp      3130
        `http_port_t`                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
        pegasus_http_port_t            tcp      5988
        pegasus_https_port_t           tcp      5989
        
    2. Asigne el puerto 82/TCP con la etiqueta de contexto `http_port_t`.
        
        [root@servera ~]# **`semanage port -a -t http_port_t -p tcp 82`**
        
    3. Reinicie el servicio `httpd`.
        
        [root@servera ~]# **`systemctl restart httpd.service`**
        
4. Verifique si ahora puede acceder al servidor web que se ejecuta en el puerto 82/TCP.
    
    [root@servera ~]# **`curl http://servera.lab.example.com:82`**
    Hello
    
5. Abra otra ventana de terminal en la máquina `worstation`. Intente acceder al nuevo servicio web desde la máquina `workstation`.
    
    student@workstation:~$ **`curl http://servera.lab.example.com:82`**
    curl: (7) Failed to connect to servera.lab.example.com port 82 after 2 ms: Could not connect to server
    
6. En la máquina `servera`, abra el puerto 82/TCP en el firewall.
    
    1. Abra el puerto 82/TCP de forma persistente para la zona predefinida del firewall.
        
        [root@servera ~]# **`firewall-cmd --permanent --add-port=82/tcp`**
        success
        
    2. Cargue el servicio `firewalld` con los cambios.
        
        [root@servera ~]# **`firewall-cmd --reload`**
        success
        
7. En la máquina `workstation`, verifique que pueda acceder al servidor web.
    
    student@workstation:~$ **`curl http://servera.lab.example.com:82`**
    Hello
    
8. Regrese al sistema `workstation` con el usuario `student`.
    
    [root@servera ~]# **`exit`**
    logout
    [student@servera ~]$ **`exit`**
    logout
    Connection to servera closed.
    student@workstation:~$