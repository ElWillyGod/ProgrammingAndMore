
## ??????????????????????????????????
1. Desde un explorador web en la máquina `workstation`, vea la página web `http://serverb/lab.html`. Debe ver el siguiente mensaje de error: `You do not have permission to access this resource.`
    
2. Investigue e identifique el problema de SELinux que impide que el servicio Apache proporcione el contenido web.
    
    1. Vea el contenido del archivo `/var/log/messages`. Use la tecla **/** y busque la cadena `sealert`. Use la tecla **q** para salir del comando `less`.
        
        [root@serverb ~]# **`less /var/log/messages`**
        _...output omitted..._
        Jun 12 23:20:07 serverb setroubleshoot[2089]: failed to retrieve rpm info for path '/lab-content/lab.html':
        Jun 12 23:30:32 serverb setroubleshoot[2282]: SELinux is preventing /usr/sbin/httpd from getattr access on the file /lab-content/lab.html. For complete SELinux messages run: `sealert -l 3ba31d72-a61a-4162-8064-a961581b9b6a`
        Jun 12 23:30:32 serverb setroubleshoot[2282]: SELinux is preventing /usr/sbin/httpd from getattr access on the file /lab-content/lab.html.
        _...output omitted..._
        
    2. Ejecute el comando sugerido `sealert`. Tenga en cuenta el contexto de origen, los objetos de destino, la política y el modo de aplicación.
        
        [root@serverb ~]# **``sealert -l _`3ba31d72-a61a-4162-8064-a961581b9b6a`_``**
        `SELinux is preventing /usr/sbin/httpd from getattr access on the file /lab-content/lab.html`.
        
        *****  Plugin catchall_labels (83.8 confidence) suggests   *******************
        
        If you want to allow httpd to have getattr access on the lab.html file
        Then you need to change the label on /lab-content/lab.html
        Do
        # semanage fcontext -a -t FILE_TYPE '/lab-content/lab.html'
        where FILE_TYPE is one of the following:
        _...output omitted..._
        
        Additional Information:
        Source Context                system_u:system_r:httpd_t:s0
        Target Context                unconfined_u:object_r:default_t:s0
        Target Objects                /lab-content/lab.html [ file ]
        Source                        httpd
        Source Path                   /usr/sbin/httpd
        _...output omitted..._
        Local ID                      3ba31d72-a61a-4162-8064-a961581b9b6a
        
        Raw Audit Messages
        type=AVC msg=audit(1749771030.133:236): avc:  denied  { getattr } for  pid=1911 comm="httpd" path="/lab-content/lab.html" dev="sda3" ino=8640016 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:default_t:s0 tclass=file permissive=0
        
        
        type=SYSCALL msg=audit(1749771030.133:236): arch=x86_64 syscall=newfstatat success=no exit=EACCES a0=ffffff9c a1=7fa59c004928 a2=7fa5aafd49f0 a3=100 items=0 ppid=1908 pid=1911 auid=4294967295 uid=48 gid=48 euid=48 suid=48 fsuid=48 egid=48 sgid=48 fsgid=48 tty=(none) ses=4294967295 comm=httpd exe=/usr/sbin/httpd subj=system_u:system_r:httpd_t:s0 key=(null)
        
        Hash: httpd,httpd_t,default_t,file,getattr
        
    3. La sección `Raw Audit Messages` del comando `sealert` contiene información del archivo `/var/log/audit/audit.log`. Busque ese archivo. La opción `-m` busca en el tipo de mensaje. La opción `ts` busca en función del tiempo. En la siguiente entrada, se identifican el proceso relevante, el archivo y los contextos que causan la alerta. El proceso es el servidor web Apache `httpd`, el archivo es `/lab-content/lab.html` y el contexto es `system_r:httpd_t`.
        
        [root@serverb ~]# **`ausearch -m AVC -ts recent`**
        _...output omitted..._
        ----
        time->Thu Jun 12 23:30:30 2025
        type=PROCTITLE msg=audit(1749771030.133:236): proctitle=2F7573722F7362696E2F6874747064002D44464F524547524F554E44
        type=SYSCALL msg=audit(1749771030.133:236): arch=c000003e syscall=262 success=no exit=-13 a0=ffffff9c a1=7fa59c004928 a2=7fa5aafd49f0 a3=100 items=0 ppid=1908 pid=1911 auid=4294967295 uid=48 gid=48 euid=48 suid=48 fsuid=48 egid=48 sgid=48 fsgid=48 tty=(none) ses=4294967295 comm="httpd" exe="/usr/sbin/httpd" subj=system_u:system_r:httpd_t:s0 key=(null)
        type=AVC msg=audit(1749771030.133:236): avc:  denied  { getattr } for  pid=1911 comm="httpd" path="/lab-content/lab.html" dev="sda3" ino=8640016 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:default_t:s0 tclass=file permissive=0
        
    
    [](https://rol.redhat.com/rol/app/#)
    
3. Muestre el contexto de SELinux del nuevo directorio de documentos HTTP y el directorio de documentos HTTP original. Resuelva el problema de SELinux que evita que el servidor Apache proporcione el contenido web.
    
    1. Compare el contexto de SELinux para los directorios `/lab-content` y `/var/www/html`.
        
        [root@serverb ~]# **`ls -dZ /lab-content /var/www/html`**
              unconfined_u:object_r:`default_t`:s0 `/lab-content`
        system_u:object_r:`httpd_sys_content_t`:s0 `/var/www/html`
        
    2. Cree una regla de contextos de archivos que establezca el tipo predeterminado en `httpd_sys_content_` para el directorio `/lab-content` y todos los archivos en él.
        
        [root@serverb ~]# **`semanage fcontext -a -t httpd_sys_content_t '/lab-content(/.*)?'`**
        
    3. Corrija el contexto de SELinux para los archivos en el directorio `/lab-content`.
        
        [root@serverb ~]# **`restorecon -Rv /lab-content/`**
        Relabeled /lab-content from unconfined_u:object_r:default_t:s0 to unconfined_u:object_r:httpd_sys_content_t:s0
        Relabeled /lab-content/lab.html from unconfined_u:object_r:default_t:s0 to unconfined_u:object_r:httpd_sys_content_t:s0