# Tips and tricks

## Network

utilizar `nmtui` en vez de `nmcli`.

## Repo

```bash
sudo -i
yum-config-manager --add-repo [URL]
```

luego editar el archivo para que tenga el nombre correcto y el `gpgcheck=0`

## SELinux

si pide cambiar el puerto del apache:

- `man semanage-port | tail -n 15` y abajo del todo aparece el comando
    - No olvidar habilitar el puerto en el firewall.

recordar utilizar `sealert -a /var/log/audit/audit.log` o si no nos acordamos podemos hacer un tail a `/var/log/messages` y ahi aparece el correspondiente `sealert -l [ID]` para obtener el detalle del error.

## Comprimir archivos

- Puede que el algoritmo solicitado no este instalado. Esto va a hacer que tire un error raro. Tenerlo presente.
    - ej: si pide bz2, habria que ver que `bzip2` este instalado.

## Containers

- Previo a levantar un container con Containerfile, hacerle un cat y ver que puertos tiene definidos. En caso de ser necesario, agregarlos al firewall.
- podman unshare: https://rol.redhat.com/rol/app/courses/rh134-9.3/pages/ch13s07
    - Si me piden cambiar el owner del dir de montaje uso `podman unshare chown UID:GID /dir`. Si no puedo encajar `chmod 777 -R` al dir y fue orrivle.

## No olvidar

- Al instalar un servicio, no olvidar ponerle enable para que inicie al reiniciar.
- Reglas del firewall marcarlas como permanent. Los bools de selinux (`setsebool`) tmb necesitan `-P` para ser permanent.
- Al hacer una modificacion, reiniciar el servicio!
