## Trabajo de laboratorio: Acceso al almacenamiento conectado a la red

Acceder al almacenamiento conectado a la red que proporciona el protocolo del NFS, ya sea manualmente o mediante el servicio de automontaje.

**Resultados**

- Instalar los paquetes requeridos para configurar el servicio de automontaje.
    
- Configurar la asignación indirecta del servicio de automontaje con recursos de un servidor NFSv4 preconfigurado.
    

Con el usuario `student` en la máquina `workstation`, ejecute el siguiente comando `lab` a fin de preparar su entorno para este ejercicio y garantizar que estén disponibles todos los recursos requeridos:

student@workstation:~$ **`lab start nfsclient-review`**

**Instrucciones**

Una empresa de soporte de TI usa la máquina `serverb` para compartir los subdirectorios `management`, `production` y `operation` del directorio `/shares`. Configure la máquina `servera` para acceder a los directorios exportados en el directorio `/remote`.

Los usuarios de los grupos `managers`, `production` y `operators` deben tener permisos de lectura y escritura para los directorios exportados `/shares/management`, `/shares/production` y `/shares/operation`, respectivamente. El usuario `manager1` pertenece al grupo `managers`, el `dbuser1` pertenece al grupo `production` y el usuario `contractor1` pertenece al grupo `operators`.

1. En la máquina `servera`, instale el paquete `autofs`.
    
    [](https://rol.redhat.com/rol/app/#)
    
2. En la máquina `servera`, monte manualmente la exportación del NFS `serverb:/shares` en el directorio `/mnt`. Esta tarea lo ayuda a verificar que las exportaciones estén configuradas con el acceso y los permisos correctos. Desmonte el directorio `/mnt`.
    
    1. En la máquina `servera`, monte la exportación del NFS `serverb:/shares` en el directorio `/mnt`.
        
        [root@servera ~]# **`mount -t nfs serverb:/shares /mnt`**
        
    2. Enumere el contenido del directorio `/mnt`.
        
        [root@servera ~]# **`ls -l /mnt`**
        total 0
        drwxrws---. 2 manager1    managers   25 Jul 14 20:23 management
        drwxrws---. 2 contractor1 operators  25 Jul 14 20:23 operation
        drwxrws---. 2 dbuser1     production 25 Jul 14 20:23 production
        
    3. Desmonte la exportación.
        
        [root@servera ~]# **`umount /mnt`**
        
    
    [](https://rol.redhat.com/rol/app/#)
    
3. En la máquina `servera`, cree una asignación indirecta de automontaje para montar exportaciones del NFS junto con todos los subdirectorios en el directorio `/remote`.
    
    Use el archivo `/etc/auto.master.d/shares.autofs` para la asignación maestra y el archivo `/etc/auto.shares` para el archivo de asignación. Monte sincrónicamente la exportación del NFS `serverb:/shares/&` en modo lectura y escritura.
    
    Inicie el servicio `autofs` y configúrelo para que se inicie automáticamente.
    
    1. Cree un archivo de asignación maestra llamado `/etc/auto.master.d/shares.autofs` con el siguiente contenido:
        
        /remote	/etc/auto.shares
        
    2. Cree un archivo de asignación indirecta llamado `/etc/auto.shares` con el siguiente contenido:
        
        * -rw,sync,fstype=nfs4 serverb:/shares/&
        
    3. Inicie y habilite el servicio `autofs`.
        
        [root@servera ~]# **`systemctl enable --now autofs`**
        Created symlink '/etc/systemd/system/multi-user.target.wants/autofs.service' → '/usr/lib/systemd/system/autofs.service'.
        
    4. Verifique que aún no se haya montado ningún directorio exportado. Los directorios se montan cuando accede a ellos.
        
        [root@servera ~]# **`mount | grep nfs`**
        rpc_pipefs on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw,relatime)
        
    
    [](https://rol.redhat.com/rol/app/#)
    
4. Con el usuario `manager1` en la máquina `servera`, verifique que pueda acceder al directorio `/remote/management`.
    
    Enumere el contenido del directorio `/remote/management` y vea el contenido del archivo `/remote/management/Welcome.txt`. Luego, cree el archivo `/remote/management/test.txt` con `TEST` como texto.
    
    Regrese a la máquina `servera` con el usuario `root`.
    
    1. Cambie al usuario `manager1`.
        
        [root@servera ~]# **`su - manager1`**
        
    2. Enumere el contenido del directorio `/remote/management`.
        
        [manager1@servera ~]$ **`ls -l /remote/management`**
        total 4
        -rw-r--r--. 1 manager1 managers 58 Jul 14 20:23 Welcome.txt
        
    3. Verifique que pueda mostrar el contenido del archivo `/remote/management/Welcome.txt`.
        
        [manager1@servera ~]$ **`cat /remote/management/Welcome.txt`**
        ### Welcome to Management Folder on SERVERB ###
        
    4. Verifique el permiso de escritura mediante la creación del archivo `/remote/management/test.txt`.
        
        [manager1@servera ~]$ **`echo TEST1 > /remote/management/test.txt`**
        
    5. Salga de la sesión de usuario `manager1`.
        
        [manager1@servera ~]$ **`exit`**
        logout
        [root@servera ~]#
        
    
    [](https://rol.redhat.com/rol/app/#)
    
5. Con el usuario `dbuser1` en la máquina `servera`, verifique que pueda acceder al directorio `/remote/production`.
    
    Enumere el contenido del directorio `/remote/production` y vea el contenido del archivo `/remote/production/Welcome.txt`. Luego, cree el archivo `/remote/production/test.txt` con `TEST2` como texto.
    
    Regrese a la máquina `servera` con el usuario `root`.
    
    1. Inicie sesión en la máquina `servera` con el usuario `dbuser`.
        
        [root@servera ~]# **`su - dbuser1`**
        
    2. Enumere el contenido del directorio `/remote/production`.
        
        [dbuser1@servera ~]$ **`ls -l /remote/production`**
        total 4
        -rw-r--r--. 1 dbuser1 production 46 Jul 14 20:23 Welcome.txt
        
    3. Verifique que pueda mostrar el contenido del archivo `/remote/production/Welcome.txt`.
        
        [dbuser1@servera ~]$ **`cat /remote/production/Welcome.txt`**
        ### Welcome to Production Folder on SERVERB ###
        
    4. Verifique el permiso de escritura mediante la creación del archivo `/remote/production/test.txt`.
        
        [dbuser1@servera ~]$ **`echo TEST2 > /remote/production/test.txt`**
        
    5. Regrese a la máquina `servera` con el usuario `root`.
        
        [dbuser1@servera ~]$ **`exit`**
        logout
        [root@servera ~]#
        
    
    [](https://rol.redhat.com/rol/app/#)
    
6. Con el usuario `contractor1` en la máquina `servera`, verifique que pueda acceder al directorio `/remote/operation`.
    
    Enumere el contenido del directorio `/remote/operation` y vea el contenido del archivo `/remote/operation/Welcome.txt`. Luego, cree el archivo `/remote/operation/test.txt` con `TEST3` como texto.
    
    Regrese a la máquina `servera` con el usuario `root`.
    
    1. Inicie sesión en la máquina `servera` con el usuario `contractor1`.
        
        [root@servera ~]# **`su - contractor1`**
        
    2. Enumere el contenido del directorio `/remote/operation`.
        
        [contractor1@servera ~]$ **`ls -l /remote/operation`**
        total 4
        -rw-r--r--. 1 contractor1 operators 45 Jul 14 01:13 Welcome.txt
        
    3. Verifique que pueda mostrar el contenido del archivo `/remote/operation/Welcome.txt`.
        
        [contractor1@servera ~]$ **`cat /remote/operation/Welcome.txt`**
        ### Welcome to Operation Folder on SERVERB ###
        
    4. Verifique el permiso de escritura mediante la creación del archivo `/remote/operation/test.txt`.
        
        [contractor1@servera ~]$ **`echo TEST3 > /remote/operation/test.txt`**
        
    5. Regrese a la máquina `servera` con el usuario `root`.
        
        [contractor1@servera ~]$ **`exit`**
        logout
        [root@servera ~]#
        
    
    [](https://rol.redhat.com/rol/app/#)
    
7. Verifique que las exportaciones del NFS ahora estén montadas porque accedió a los directorios exportados.
    
    [root@servera ~]# **`mount | grep nfs`**
    rpc_pipefs on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw,relatime)
    serverb:/shares/management on /remote/management type nfs4 (rw,relatime,sync,vers=4.2,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp, timeo=600,retrans=2,sec=sys,clientaddr=172.25.250.10,local_lock=none, addr=172.25.250.11)
    serverb:/shares/production on /remote/production type nfs4 (rw,relatime,sync,vers=4.2,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp, timeo=600,retrans=2,sec=sys,clientaddr=172.25.250.10,local_lock=none, addr=172.25.250.11)
    serverb:/shares/operation on /remote/operation type nfs4 (rw,relatime,sync,vers=4.2,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp, timeo=600,retrans=2,sec=sys,clientaddr=172.25.250.10,local_lock=none, addr=172.25.250.11)