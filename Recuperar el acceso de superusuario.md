## Ejercicio guiado: Recuperar el acceso de superusuario y restablecer la contraseña root.

Obtener acceso administrativo a un sistema cuando la contraseña de superusuario se desconoce o está bloqueada.

**Resultados**

- Restablecer la contraseña del usuario `root`.
    

Con el usuario `student` en la máquina `workstation`, ejecute el siguiente comando `lab` a fin de preparar su entorno para este ejercicio y garantizar que estén disponibles todos los recursos requeridos:

student@workstation:~$ **`lab start rootpw-recover`**

**Instrucciones**

1. La contraseña del usuario `root` en la máquina `servera` debe ser `redhat`.
    
    Ubique la consola de la máquina `servera` e intente iniciar sesión como el usuario `root`. Use la contraseña `redhat`.
    
    servera login: **`root`**
    Password: **`redhat`**
    Login incorrect
    
2. Restablezca la contraseña del usuario `root` a `redhat` sin usar medios de rescate. Primero, reinicie la máquina `servera` e interrumpa la cuenta regresiva en el menú del gestor de arranque.
    
    1. Ubique la consola de `servera`, según corresponda para el entorno del aula y luego abra la consola.
        
        Envíe **Ctrl**+**Alt**+**Del** a su sistema usando la entrada del menú o el botón relevantes.
        
    2. Cuando el menú del gestor de arranque aparezca, presione **Esc** para interrumpir la cuenta regresiva.
        
3. Edite la entrada resaltada del gestor de arranque del kernel para detener el proceso de arranque justo después de que el kernel haya montado todos los sistemas de archivos, pero antes de transferir el control a la unidad `systemd`.
    
    1. Use las teclas del cursor para seleccionar la entrada del kernel.
        
    2. Presione **E** para editar la entrada actual.
        
    3. Mueva el cursor hasta la línea que comienza con el texto `linux`.
        
    4. Elimine la opción `console=` de la línea.
        
        ### Importante
        
        Si no elimina las opciones `console=` de la entrada de GRUB2 para el kernel de rescate, es posible que el prompt `root` se inicie en la consola incorrecta y no pueda acceder a él.
        
    5. Presione **Ctrl**+**E** para mover el cursor hasta el final de la línea.
        
    6. Agregue un espacio seguido de la opción `init=/bin/bash` al final de la línea.
        
        ### Nota
        
        Si el texto en la consola es difícil de ver, considere cambiar la resolución cuando edite la línea del kernel en la entrada del gestor de arranque.
        
        Para cambiar la resolución de la consola, agregue el parámetro `video=640x480` o `vga=ask` en la línea que comienza con la palabra `linux`, después del parámetro `rd.break`. Para la mayoría de las consolas, una resolución de `640x480` es suficiente. Si usa el parámetro `vga=ask`, puede elegir una resolución más adecuada para su entorno.
        
    7. Presione **Ctrl**+**X** para realizar el arranque con la configuración modificada.
        
4. Espere a que el sistema termine de arrancar. En el prompt `bash-5.2#`, vuelva a montar el sistema de archivos `root` (`/`) como lectura/escritura.
    
    bash-5.2# **`mount -o remount,rw /`**
    
5. Cambie la contraseña `root` a `redhat`.
    
    bash-5.2# **`passwd`**
    New password: **`redhat`**
    BAD PASSWORD: The password is shorter than 8 characters
    Retype new password: **`redhat`**
    passwd: password updated successfully
    
6. Configure el sistema para que realice automáticamente un etiquetado nuevo de SELinux completo después del arranque. Este paso es necesario porque el comando `passwd` recrea el archivo `/etc/shadow` sin un contexto SELinux.
    
    bash-5.2# **`touch /.autorelabel`**
    
7. Reinicie la máquina `servera`.
    
    El sistema ejecuta una operación de reetiquetado de SELinux y luego se reinicia automáticamente. Espere a que la máquina `servera` arranque.
    
    bash-5.2# **`exec /sbin/init`**
    
8. Cuando el sistema esté disponible, verifique que pueda iniciar sesión con el usuario `root` y la contraseña `redhat`.
    
    servera login: **`root`**
    Password: **`redhat`**
    _...output omitted..._
    [root@servera ~]#
    
9. Cierre la consola `servera`.