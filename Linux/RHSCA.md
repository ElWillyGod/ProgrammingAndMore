## Trabajo de laboratorio: Gestionar archivos desde la línea de comandos

Gestionar los archivos, redirigir un conjunto específico de líneas de un archivo de texto a otro archivo y editar los archivos de texto.

**Resultados**

- Gestionar archivos desde la línea de comandos.
    
- Capturar contenido específico de un archivo de texto o un comando, y redireccionar la salida a otro archivo.
    
- Editar archivos de texto.
    

### Nota

El tiempo asignado para esta actividad es 15 minutos. Si necesita más tiempo para completar la tarea, debe volver a revisar el contenido del curso o practicar más.

Si no restableció las máquinas `workstation` y ``server_`X`_`` al final del último capítulo, guarde el trabajo que desea mantener de ejercicios anteriores de esas máquinas y restablézcalas ahora.

Con el usuario `student` en la máquina `workstation`, use el siguiente comando `lab` a fin de preparar su entorno para este ejercicio y garantizar que estén disponibles todos los recursos requeridos:

student@workstation:~$ **`lab start rhcsa-rh124-review1`**

**Especificaciones**

Se unió a una organización como administrador de sistemas pasante. Tiene la tarea de completar la capacitación de nivel 1 de la organización para asegurarse de poder gestionar el editor de texto y sus accesos directos, y crear archivos y directorios.

Realice las siguientes tareas en la máquina `serverb`:

- Cree el directorio `/home/student/grading`.
    
- Cree tres archivos vacíos denominados `grade1`, `grade2` y `grade3` en el directorio `/home/student/grading`.
    
- Capture las primeras cinco líneas del archivo `/home/student/bin/manage` en el archivo `/home/student/grading/review.txt`.
    
- Sin sobrescribir ningún texto existente, agregue las últimas tres líneas del archivo `/home/student/bin/manage` al archivo `/home/student/grading/review.txt`.
    
- Copie el archivo `/home/student/grading/review.txt` en el archivo `/home/student/grading/review-copy.txt`.
    
- Duplique la línea `Test JJ` en el archivo `/home/student/grading/review-copy.txt`. Asegúrese de que la línea duplicada aparezca inmediatamente después de la línea original.
    
- Elimine la línea `Test HH` del archivo `/home/student/grading/review-copy.txt`.
    
- Agregue la línea `Level 1 Training` entre la línea `Test BB` y la línea `Test CC` en el archivo `/home/student/grading/review-copy.txt`.
    
- Cree un enlace llamado `data-backup` que apunte a los mismos datos que el archivo `/home/student/grading/grade1`.
    
- Cree un enlace llamado `filename-backup` que apunte al nombre del archivo `/home/student/grading/grade2`.
    
- Enumere el contenido del directorio `/boot` y guarde la salida al archivo `/home/student/grading/longlisting.txt`. La salida debe ser un listado largo que incluye permisos de archivo, propietario y propietario del grupo, tamaño y fecha de modificación de cada archivo. La salida debe omitir los archivos ocultos.
    

1. Inicie sesión en la máquina `serverb` con el usuario `student`.
    
2. Cree el directorio `/home/student/grading` y, dentro del directorio, cree tres archivos vacíos denominados `grade1`, `grade2` y `grade3`.
    
    [student@serverb ~]$ **`mkdir /home/student/grading \ && touch grading/grade{1,2,3}`**
    
3. Cree el archivo `review.txt` con contenido de otro archivo y cree el archivo `review-copy.txt` como una copia del archivo `review.txt`.
    
    [student@serverb ~]$ **`head -5 bin/manage > grading/review.txt \ && tail -3 bin/manage >> grading/review.txt \ && cp grading/review.txt grading/review-copy.txt`**
    
4. Edite el archivo `/home/student/grading/review-copy.txt` y actualice el contenido para que coincida con el siguiente texto:
    
    Test AA
    Test BB
    Level 1 Training
    Test CC
    Test DD
    Test EE
    Test II
    Test JJ
    Test JJ
    
5. Cree los enlaces duros y blandos, y cree el archivo `longlisting.txt` con la salida del contenido del directorio `/boot`.
    
    [student@serverb ~]$ **`ln grading/grade1 data-backup && \ ln -s grading/grade2 filename-backup && \ ls -l /boot > grading/longlisting.txt`**


## Trabajo de laboratorio: Gestión de usuarios y grupos, permisos y procesos

Gestionar cuentas de usuarios y grupos, establecer permisos en archivos y directorios, y gestionar procesos.

**Resultados**

- Gestionar cuentas de usuarios y grupos.
    
- Establecer permisos en archivos y directorios.
    
- Identificar y gestionar los procesos que consumen mucha CPU.
    

### Nota

El tiempo asignado para esta actividad es 15 minutos. Si necesita más tiempo para completar la tarea, debe volver a revisar el contenido del curso o practicar más.

Si no restableció las máquinas `workstation` y ``server_`X`_`` al final del último capítulo, guarde el trabajo que desea mantener de ejercicios anteriores de esas máquinas y restablézcalas ahora.

Con el usuario `student` en la máquina `workstation`, use el siguiente comando `lab` a fin de preparar su entorno para este ejercicio y garantizar que estén disponibles todos los recursos requeridos.

student@workstation:~$ **`lab start rhcsa-rh124-review2`**

**Especificaciones**

Pasó a la siguiente etapa de la pasantía como administrador de systemd, en la que se le solicita que realice algunas tareas administrativas. En su nuevo proyecto, los usuarios de la base de datos tienen acceso a una ubicación específica para crear, editar y almacenar archivos de base de datos. Con el objetivo de permitir que estos usuarios de la base de datos gestionen archivos de forma colaborativa, debe modificar los permisos para que todos los usuarios tengan acceso a todos los archivos que se crean en esta ubicación. La otra restricción que se debe tener en cuenta es que solo los propietarios de los archivos pueden eliminarlos.

Realice las siguientes tareas en la máquina `serverb`:

- Identifique y finalice el proceso que actualmente usa la mayor cantidad de tiempo de CPU.
    
- Cree el grupo `database` con un ID de grupo (GID) de 50000.
    
- Cree el usuario `dbadmin1` y configúrelo con los siguientes requisitos:
    
    - Agregue el grupo `database` como grupo complementario.
        
    - Establezca la contraseña en `redhat` y fuerce un cambio de contraseña en el primer inicio de sesión.
        
    - Permita que se cambie la contraseña 10 días después del último cambio de contraseña.
        
    - Configure la caducidad de la contraseña 30 días después del último cambio de contraseña.
        
    - Permita que el usuario use Sudo para ejecutar cualquier comando como superusuario mediante la creación de su propio archivo sudoers.
        
    - Configure el umask predeterminado como `027` para el usuario `dbadmin1`.
        
    
- Cree el directorio `/home/dbadmin1/grading/review2` y defina su contenido para que sea propiedad del usuario `dbadmin1` y el grupo `database`.
    
- Configure el directorio `/home/dbadmin1/grading/review2`, de modo que los usuarios solo puedan eliminar archivos de su propiedad. Configure los permisos del directorio para permitir que los miembros del grupo `database` accedan al directorio y creen contenido en él. Todos los demás usuarios deben tener permisos de lectura y ejecución en el directorio.
    

1. Inicie sesión en la máquina `serverb` con el usuario `student`.
    
2. Identifique y finalice el proceso `dd` que actualmente usa la mayor cantidad de recursos de CPU.
    
3. Cambie al usuario `root`, cree el grupo `database` con un ID de grupo (GID) de 50000 y cree el usuario `dbadmin1`. Establezca las políticas de caducidad de la contraseña.
    
    [student@serverb ~]$ **`sudo -i`**
    [sudo] password for student: **`student`**
    [root@serverb ~]# **`groupadd -g 50000 database \ && useradd -G database dbadmin1 \ && chage -d 0 -m 10 -M 30 dbadmin1`**
    
4. Actualice la contraseña del usuario `dbadmin1` a `redhat`.
    
5. Cree el archivo `/etc/sudoers.d/dbadmin1` con el siguiente contenido:
    
    dbadmin1 ALL=(ALL) ALL
    
6. Cambie al usuario `dbadmin1`. Anexe la línea `umask 027` al archivo `/home/dbadmin1/.bashrc`.
    
    [root@serverb ~]# **`su - dbadmin1`**
    [dbadmin1@serverb ~]$ **`echo "umask 027" >> .bashrc`**
    [dbadmin1@serverb ~]$ **`source ~/.bashrc`**
    
7. Cree el directorio `/home/dbadmin1/grading/review2` y establezca los permisos correspondientes.
    
    [dbadmin1@serverb ~]$ **`mkdir -p /home/dbadmin1/grading/review2 \ && chown -R dbadmin1:database /home/dbadmin1/ \ && chmod o+t /home/dbadmin1/grading/review2 \ && chmod g+w /home/dbadmin1/grading/review2 \ && chmod o+r /home/dbadmin1/grading/review2`**

## Trabajo de laboratorio: Configuración y gestión de un servidor

Configurar, proteger y usar el servicio SSH para acceder a una máquina remota.

Configurar dnf y gestionar paquetes con la utilidad `dnf`.

**Resultados**

- Crear un par de claves SSH.
    
- Deshabilitar los inicios de sesión de SSH con el usuario `root`.
    
- Deshabilitar los inicios de sesión SSH basados en contraseña.
    
- Configurar los repositorios de software en la máquina `servera` para obtener actualizaciones.
    
- Instalar paquetes y módulos de paquetes con el comando `dnf`.
    

### Nota

El tiempo asignado para esta actividad es 10 minutos. Si necesita más tiempo para completar la tarea, debe volver a revisar el contenido del curso o practicar más.

Si no restableció las máquinas `workstation` y ``server_`X`_`` al final del último capítulo, guarde el trabajo que desea mantener de ejercicios anteriores de esas máquinas y restablézcalas ahora.

Con el usuario `student` en la máquina `workstation`, use el siguiente comando `lab` a fin de preparar su entorno para este ejercicio y garantizar que estén disponibles todos los recursos requeridos.

student@workstation:~$ **`lab start rhcsa-rh124-review3`**

**Especificaciones**

Como administrador de sistemas, debe encargarse de poner en marcha, gestionar y brindar soporte a los desarrolladores que usan servidores RHEL 10. El equipo de administración del sistema recientemente aprovisionó una máquina y compartió una lista de usuarios y directorios para iniciar sesión y gestionar servidores. Para evitar compartir contraseñas, debe configurar la autenticación segura basada en claves. También debe configurar un repositorio personalizado y garantizar que estén instaladas las herramientas comunes de administración de sistemas.

Realice las siguientes tareas en la máquina `serverb`:

- Genere un par de claves SSH para el usuario `student`. No proteja la clave privada con una frase de contraseña. Guarde la clave privada como el archivo `/home/student/.ssh/review3_key` y guarde la clave pública como el archivo `/home/student/.ssh/review3_key.pub`.
    
- Configure SSH para evitar que el usuario `root` inicie sesión.
    
- Configure SSH para que los usuarios inicien sesión mediante la autenticación basada en claves y no mediante la autenticación basada en contraseñas.
    
- Configure DNF para obtener paquetes de `http://repo.example.com/rhel10.0/x86_64/rhcsa-practice/errata`. Deshabilite la verificación de GPG para este repositorio.
    
- Instale los paquetes `zsh` y `rht-system`.
    
- El usuario `student` en la máquina `serverb` debe poder iniciar sesión en la máquina `servera` con la clave SSH `review3_key.pub`.
    

1. Inicie sesión en `serverb` con el usuario `student`.
    
2. Genere el par de claves SSH `/home/student/.ssh/review3_key` para el usuario `student`. No proteja la clave privada con una frase de contraseña.
    
    [student@serverb ~]$ **`ssh-keygen -f /home/student/.ssh/review3_key`**
    _...output omitted..._
    
3. Exporte la clave pública `review3_key` de la máquina `serverb` a la máquina `servera`.
    
    [student@serverb ~]$ **`ssh-copy-id -i .ssh/review3_key.pub student@servera`**
    
4. Edite el archivo `/etc/ssh/sshd_config` y establezca los parámetros `PermitRootLogin` y `PasswordAuthentication` en `no`. Recargue el servicio `sshd`.
    
5. Configure el repositorio personalizado en `http://repo.example.com/rhel10.0/x86_64/rhcsa-practice/errata` e instale los paquetes `zsh` y `rht-system`.
    
    [student@serverb ~]$ **`sudo dnf config-manager --add-repo \ "http://repo.example.com/rhel10.0/x86_64/rhcsa-practice/errata" \ && sudo dnf config-manager --save \ --setopt=repo.example.com_rhel10.0_x86_64_rhcsa-practice_errata.gpgcheck=0 \ && sudo dnf install rht-system zsh -y`**


## Trabajo de laboratorio: Gestión de redes

Configurar y probar la conectividad de red.

**Resultados**

- Configurar los ajustes de red.
    
- Probar la conectividad de red.
    
- Establecer un nombre de host estático.
    
- Usar nombres de host canónicos que se puedan resolver localmente para conectarse a los sistemas.
    

### Nota

El tiempo asignado para esta actividad es 10 minutos. Si necesita más tiempo para completar la tarea, debe volver a revisar el contenido del curso o practicar más.

Si no restableció las máquinas `workstation` y ``server_`X`_`` al final del último capítulo, guarde el trabajo que desea mantener de ejercicios anteriores de esas máquinas y restablézcalas ahora.

Con el usuario `student` en la máquina `workstation`, use el siguiente comando `lab` a fin de preparar su entorno para este ejercicio y garantizar que estén disponibles todos los recursos requeridos.

student@workstation:~$ **`lab start rhcsa-rh124-review4`**

### Importante

Cuando ajusta la configuración de red, ejecutar un comando erróneo podría bloquear o suspender su sesión, y provocar que no pueda accederse a su sistema. Para evitar quedarse fuera del sistema, considere ajustar la configuración de red a través de la consola remota.

**Especificaciones**

Como administrador de sistemas, tiene la tarea de configurar los ajustes de red y el nombre de host en una máquina recién aprovisionada. Completar la configuración de red de un servidor es un requisito previo para delegar la máquina a los desarrolladores y usuarios de aplicaciones a fin de configurar y probar sus aplicaciones.

Realice las siguientes tareas en la máquina `serverb`:

- Busque el dispositivo de red no utilizado y utilícelo para crear y activar un perfil de conexión `static`. Configure el perfil `static` para establecer de manera estática la configuración de la red y no usar DHCP. Use la información de la siguiente tabla para otros ajustes de conexión:
    
    |Parámetro|Configuración|
    |:--|:--|
    |Dirección IPv4|172.25.250.111|
    |Máscara de red|255.255.255.0|
    |Puerta de enlace|172.25.250.254|
    |Servidor DNS|172.25.250.254|
    
- Establezca el nombre de host en `server-review4.lab4.example.com`.
    
- Establezca `client-review4` como el nombre de host canónico para la dirección IPv4 `servera` `172.25.250.10`.
    
- Configure el perfil de conexión `static` con una dirección IPv4 adicional de `172.25.250.211` con una máscara de red de `255.255.255.0`. No elimine la dirección IPv4 existente. Garantice que la máquina `serverb` responda a todas las direcciones cuando la conexión `static` está activa.
    

1. Use la consola `serverb` para iniciar sesión con el usuario `student` en la máquina `serverb` y cambie al usuario `root`.
    
2. Use el comando `nmcli device status` para determinar el dispositivo Ethernet no utilizado.
    
    ### Importante
    
    Es posible que el nombre de la interfaz de red asociado con la dirección MAC proporcionada varíe según la plataforma del curso y el hardware en uso. Esta actividad supone que el nombre de la interfaz es `ens4`.
    
3. Cree un perfil de conexión `static`, active el nuevo perfil de conexión y configure un nuevo nombre de host.
    
    [root@serverb ~]# **`nmcli connection add con-name static type ethernet \ ifname ens4 ipv4.addresses '172.25.250.111/24' ipv4.gateway '172.25.250.254' \ ipv4.dns '172.25.250.254' ipv4.method manual \ && nmcli connection up static \ && hostnamectl hostname server-review4.lab4.example.com`**
    
4. Actualice el archivo `/etc/hosts` con el nombre del servidor `client-review4` para la dirección IPv4 `172.25.250.10`.
    
5. Modifique el perfil de conexión `static`, configure la dirección IPv4 `172.25.250.211/24` adicional y active la nueva dirección IP.
    
    [root@serverb ~]# **`nmcli connection modify static \ +ipv4.addresses '172.25.250.211/24' \ && nmcli connection up static`**