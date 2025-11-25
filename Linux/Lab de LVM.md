**Instrucciones**

En la máquina `serverb`, el directorio `/storage/data1` se está quedando sin espacio en disco. El grupo de volúmenes (VG) `vg_01_serverb` no tiene espacio suficiente para ampliar el volumen lógico (LV) `lv_01_serverb`. Para ampliar el VG, primero debe crear una partición secundaria de 1024 MiB en el disco `/dev/sdb`. El VG no debe superar los 2048 MiB.

Luego, amplíe el LV `lv_01_serverb` a 1024 MiB. Garantice que el LV `lv_01_serverb` permanezca montado de manera persistente en el directorio `/storage/data1`.

Por último, cree el LV `lv_02_serverb` con 512 MiB desde el VG `vg_01_serverb`. Cree el sistema de archivos XFS en el volumen creado recientemente. Monte el volumen lógico recién creado de manera persistente en el directorio `/storage/data2`.

### Importante

Preste especial atención a la especificación del tamaño de la partición en MiB (220 bytes). Si crea la partición en MB (106 bytes), no cumple los criterios de evaluación porque 1 MiB = 1.048576 MB.

Aunque la unidad predeterminada cuando se usa el comando `parted` es MB, puede verificar el tamaño de las particiones en unidades MiB con la opción `unit MiB`.

1. En la máquina `serverb`, cree la partición secundaria de 1024 MiB en el disco `/dev/sdb`. Registre la partición con el kernel.
    
    1. Inicie sesión en la máquina `serverb` con el usuario `student` y cambie al usuario `root`. Use `student` como contraseña.
        
        student@workstation:~$ **`ssh student@serverb`**
        _...output omitted..._
        [student@serverb ~]$ **`sudo -i`**
        [sudo] password for student: **`student`**
        [root@serverb ~]#
        
    2. Imprima los tamaños de las particiones en MiB para determinar dónde termina la partición `/dev/sdb`.
        
        [root@serverb ~]# **`parted /dev/sdb unit MiB print`**
        _...output omitted..._
        
        Number  Start    **`End`**     Size    File system  Name     Flags
         1      1.00MiB  **`1025MiB`** 1024MiB              primary
        
    3. Cree la partición secundaria de 1024 MiB en la partición `/dev/sdb`.
        
        [root@serverb ~]# **`parted /dev/sdb mkpart secondary 1025MiB 2049MiB`**
        _...output omitted..._
        
    4. Establezca la segunda partición en el tipo de partición `lvm`.
        
        [root@serverb ~]# **`parted /dev/sdb set 2 lvm on`**
        _...output omitted..._
        
    5. Registre la nueva partición con el kernel.
        
        [root@serverb ~]# **`udevadm settle`**
        
    
    [](https://rol.redhat.com/rol/app/#)
    
2. Inicialice la segunda partición como volumen físico.
    
    1. Inicialice la segunda partición como un PV.
        
        [root@serverb ~]# **`pvcreate /dev/sdb2`**
          Physical volume "/dev/sdb2" successfully created.
        
    
    [](https://rol.redhat.com/rol/app/#)
    
3. Amplíe el grupo de volúmenes `vg_01_serverb` para usar la partición secundaria.
    
    1. Amplíe el VG `vg_01_serverb` con el PV `/dev/sdb2`.
        
        [root@serverb ~]# **`vgextend vg_01_serverb /dev/sdb2`**
          Volume group "vg_01_serverb" successfully extended
        
    
    [](https://rol.redhat.com/rol/app/#)
    
4. Amplíe el volumen lógico `lv_01_serverb` a 1024 MiB y cambie el tamaño del sistema de archivos XFS.
    
    1. Amplíe el LV `lv_01_serverb` a 1024 MiB.
        
        O también puede usar el comando `lvcreate`, opción `-L +624M`, para cambiar el tamaño del LV.
        
        [root@serverb ~]# **`lvextend -L 1024M /dev/vg_01_serverb/lv_01_serverb`**
          Size of logical volume vg_01_serverb/lv_01_serverb changed from 400.00 MiB (100 extents) to 1.00 GiB (256 extents).
          Logical volume vg_01_serverb/lv_01_serverb successfully resized.
        
    2. Amplíe el sistema de archivos XFS que consume el espacio restante del LV.
        
        ### Nota
        
        El comando `xfs_growfs` introduce un paso adicional para ampliar el sistema de archivos. Una alternativa sería usar el comando `lvextend`, opción `-r`.
        
        [root@serverb ~]# **`xfs_growfs /storage/data1`**
        _...output omitted..._
        data blocks changed from 102400 to 262144
        
    
    [](https://rol.redhat.com/rol/app/#)
    
5. En el grupo de volúmenes `vg_01_serverb`, cree el volumen lógico `lv_02_serverb` con 512 MiB. Agregue un sistema de archivos XFS y móntelo de forma constante en el directorio `/storage/data2`.
    
    1. Cree el LV `lv_02_serverb` con 512 MiB desde el VG `vg_01_serverb`.
        
        [root@serverb ~]# **`lvcreate -n lv_02_serverb -L 512M vg_01_serverb`**
          Logical volume "lv_02_serverb" created.
        
    2. Cree el sistema de archivos XFS en el LV `lv_02_serverb`.
        
        [root@serverb ~]# **`mkfs -t xfs /dev/vg_01_serverb/lv_02_serverb`**
        _...output omitted..._
        
    3. Cree el directorio `/storage/data2`.
        
        [root@serverb ~]# **`mkdir /storage/data2`**
        
    4. Agregue la línea siguiente al final del archivo `/etc/fstab`:
        
        /dev/vg_01_serverb/lv_02_serverb /storage/data2 xfs defaults 0 0
        
    5. Actualice el daemon `systemd` con el nuevo archivo de configuración `/etc/fstab`.
        
        [root@serverb ~]# **`systemctl daemon-reload`**
        
    6. Monte el LV `lv_02_serverb`.
        
        [root@serverb ~]# **`mount /storage/data2`**
        
    
    [](https://rol.redhat.com/rol/app/#)
    
6. Verifique que los LV `lv_01_serverb` y `lv_02_serverb` creados recientemente estén montado con el tamaño deseado.
    
    1. Use el comando `df` para verificar el tamaño del LV `lv_01_serverb`.
        
        [root@serverb ~]# **`df -h /storage/data1`**
        Filesystem                               Size  Used Avail Use% Mounted on
        /dev/mapper/vg_01_serverb-lv_01_serverb  `960M`   40M  921M   5% /storage/data1
        
    2. Verifique el tamaño del LV `lv_02_serverb`.
        
        [root@serverb ~]# **`df -h /storage/data2`**
        Filesystem                               Size  Used Avail Use% Mounted on
        /dev/mapper/vg_01_serverb-lv_02_serverb  `448M`   35M  414M   8% /storage/data2