## Ejercicio guiado: Creación y gestión de sistemas de archivos en particiones estándar

Crear particiones de almacenamiento, formatearlas con sistemas de archivos y montar estos últimos cuando arranca la computadora.

**Resultados**

- Crear una partición con el esquema de partición de MBR.
    
- Formatear la nueva partición con el sistema de archivos XFS.
    
- Montar de manera persistente el sistema de archivos.
    

Con el usuario `student` en la máquina `workstation`, ejecute el siguiente comando `lab` a fin de preparar su entorno para este ejercicio y garantizar que estén disponibles todos los recursos requeridos:

student@workstation:~$ **`lab start storage-partitions`**

**Instrucciones**

1. Inicie sesión en la máquina `servera` con el usuario `student` y cambie al usuario `root`. Use `student` como contraseña.
    
    student@workstation:~$ **`ssh student@servera`**
    _...output omitted..._
    [student@servera ~]$ **`sudo -i`**
    [sudo] password for student: **`student`**
    [root@servera ~]#
    
2. Cree el esquema de partición de MBR en el dispositivo `/dev/sdb`.
    
    [root@servera ~]# **`parted /dev/sdb mklabel msdos`**
    Information: You may need to update /etc/fstab.
    
3. Cree una partición principal de 1 GB. Para una correcta alineación, inicie la partición en el sector 2048. Establezca el tipo de sistema de archivo de la partición en XFS. Use el comando `parted` en modo interactivo para crear la partición.
    
    Como alternativa, puede realizar la misma operación con el comando no interactivo `parted /dev/sdb mkpart primary xfs 2048s 1001 MB`.
    
    1. Use el comando `parted` para crear la partición en el disco `/dev/sdb`.
        
        [root@servera ~]# **`parted /dev/sdb`**
        GNU Parted 3.6
        Using /dev/sdb
        Welcome to GNU Parted! Type 'help' to view a list of commands.
        
    2. Use el subcomando `mkpart` para crear una partición principal.
        
        (parted) **`mkpart`**
        Partition type?  primary/extended? **`primary`**
        
    3. Establezca la etiqueta de tipo de sistema de archivos en `xfs`.
        
        File system type?  [ext2]? **`xfs`**
        
    4. Inicie la partición en el sector `2048s`.
        
        Start? **`2048s`**
        
    5. Dado que la partición comienza en el sector 2048, establezca la posición final en 1001 MB para configurar un tamaño de partición de 1000 MB (1 GB).
        
        End? **`1001MB`**
        
    6. Salga del comando `parted`.
        
        (parted) **`quit`**
        Information: You may need to update /etc/fstab.
        
4. Verifique su trabajo enumerando las particiones en el dispositivo `/dev/sdb`.
    
    [root@servera ~]# **`parted /dev/sdb print`**
    Model: QEMU QEMU HARDDISK (scsi)
    Disk /dev/sdb: 5369MB
    Sector size (logical/physical): 512B/512B
    Partition Table: `msdos`
    Disk Flags:
    
    Number  Start   End     Size    Type     File system  Flags
     1      1049kB  1001MB  `1000MB`  `primary`  `xfs`
    
5. Use el comando `udevadm` para registrar la nueva partición.
    
    [root@servera ~]# **`udevadm settle`**
    
6. Formatee la nueva partición con el sistema de archivos XFS.
    
    [root@servera ~]# **`mkfs.xfs /dev/sdb1`**
    meta-data=/dev/sdb1         isize=512    agcount=4, agsize=61056 blks
             =                  sectsz=512   attr=2, projid32bit=1
             =                  crc=1        finobt=1, sparse=1, rmapbt=1
             =                  reflink=1    bigtime=1 inobtcount=1 nrext64=1
             =                  exchange=0
    data     =                  bsize=4096   blocks=244224, imaxpct=25
             =                  sunit=0      swidth=0 blks
    naming   =version 2         bsize=4096   ascii-ci=0, ftype=1, parent=0
    log      =internal log      bsize=4096   blocks=16384, version=2
             =                  sectsz=512   sunit=0 blks, lazy-count=1
    realtime =none              extsz=4096   blocks=0, rtextents=0
    Discarding blocks...Done.
    
7. Configure el nuevo sistema de archivos para montarlo de forma constante en el directorio `/archive`.
    
    1. Cree el directorio `/archive`.
        
        [root@servera ~]# **`mkdir /archive`**
        
    2. Imprima el UUID del dispositivo `/dev/sdb1` y tome nota del UUID para pasos futuros. El UUID del dispositivo podría ser diferente en su sistema.
        
        [root@servera ~]# **`lsblk --fs /dev/sdb`**
        NAME   FSTYPE FSVER LABEL UUID ...
        sdb
        └─sdb1 xfs                `291bb68e-91b9-4522-8566-7cc1d848babd`
        
    3. Agregue una entrada al archivo `/etc/fstab`. Reemplace el UUID con el que recuperó en el paso anterior.
        
        _...output omitted..._
        UUID=_`291bb68e-91b9-4522-8566-7cc1d848babd`_ /archive xfs defaults  0 0
        
    4. Actualice el daemon `systemd` para que el sistema registre la configuración del archivo `/etc/fstab`.
        
        [root@servera ~]# **`systemctl daemon-reload`**
        
    5. Monte el nuevo sistema de archivos que agregó al archivo `/etc/fstab`.
        
        [root@servera ~]# **`mount /archive`**
        
    6. Verifique que el nuevo sistema de archivos esté montado en el directorio `/archive`.
        
        [root@servera ~]# **`mount | grep /archive`**
        /dev/sdb1 on /archive type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
        
8. Reinicie la máquina `servera`. Inicie sesión en la máquina `servera` con el usuario `student`. Verifique que la partición `/dev/sdb1` esté montada en el directorio `/archive`.
    
    1. Reinicie la máquina `servera`.
        
        [root@servera ~]# **`systemctl reboot`**
        Connection to servera closed by remote host.
        Connection to servera closed.
        student@workstation:~$
        
    2. Espere a que la máquina `servera` se reinicie e inicie sesión con el usuario `student`.
        
        student@workstation:~$ **`ssh student@servera`**
        _...output omitted..._
        [student@servera ~]$
        
    3. Verifique que la partición `/dev/sdb1` esté montada en el directorio `/archive`. Para mostrar la salida del comando `mount`, no proporcione una barra final con la partición.
        
        [student@servera ~]$ **`mount | grep /archive`**
        /dev/sdb1 on /archive type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
        
9. Regrese a la máquina `workstation` con el usuario `student`.
    
    [student@servera ~]$ **`exit`**
    logout
    Connection to servera closed.
    student@workstation:~$
    

**Finalizar**