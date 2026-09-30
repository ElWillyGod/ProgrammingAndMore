# Expose registry

**Procedimiento 3.2. Instrucciones**

1. Compruebe que el registro interno del clúster de OpenShift esté expuesto.
    1. Cargue la configuración de su entorno de aula.

        Ejecute el siguiente comando para cargar las variables de entorno creadas en el primer ejercicio guiado:

        ```bash
        [student@workstation ~]$ source /usr/local/etc/ocp4.config
        ```

    2. Para facilitar la escritura, guarde el nombre de host de la ruta del registro interno en una variable de shell.

        El administrador de sistemas debe proporcionar el valor de esta ruta.

        ```bash
        [student@workstation ~]$ INTERNAL_REGISTRY=\
        default-route-openshift-image-registry.apps.ocp4.example.com
        ```

        > [!note] NOTA
        > La ruta predeterminada al registro interno de RHOCP está deshabilitada de manera predeterminada. Un administrador de sistemas habilitó explícitamente la ruta para el entorno de laboratorio.
        >
        > Para obtener más información sobre habilitar la ruta predeterminada al registro interno, consulte el capítulo *Exposición del registro* en la documentación de Red Hat OpenShift Container Platform 4.10 en https://access.redhat.com/documentation/en-us/openshift_container_platform/4.10/html/registry/securing-exposing-registry.

    3. Verifique la disponibilidad de la ruta al registro interno de RHOCP iniciando sesión en el registro y usando el token de autenticación del desarrollador y el nombre de host del registro. Si se expone la ruta predeterminada al registro interno de RHOCP, el usuario desarrollador puede iniciar sesión con `podman`.

        ```bash
        [student@workstation ~]$ podman login -u ${RHT_OCP4_DEV_USER} \
        -p $(oc whoami -t) $INTERNAL_REGISTRY
        Login Succeeded!
        ```

2. Envíe una imagen al registro interno de OpenShift usando su cuenta de usuario de desarrollador.
    1. Inicie sesión en OpenShift con su cuenta de usuario de desarrollador.

        ```bash
        [student@workstation ~]$ oc login -u ${RHT_OCP4_DEV_USER} \
        -p ${RHT_OCP4_DEV_PASSWORD} ${RHT_OCP4_MASTER_API}
        Login successful.
        ...output omitted...
        ```

    2. Cree un proyecto para alojar los flujos de imágenes que gestionan las imágenes de contenedor que enviará al registro interno de OpenShift:

        ```bash
        [student@workstation ~]$ oc new-project ${RHT_OCP4_DEV_USER}-common
        Now using project "youruser-common" on server
        "https://api.cluster.domain.example.com:6443".
        ```

    3. Obtenga el token de autenticación de OpenShift de su cuenta de usuario de desarrollador para usar en comandos posteriores:

        ```bash
        [student@workstation ~]$ TOKEN=$(oc whoami -t)
        ```

    4. Verifique que la carpeta `ubi-info` contenga una imagen de contenedor con formato OCI:

        ```bash
        [student@workstation ~]$ ls ~/DO288/labs/expose-registry/ubi-info
        blobs   index.json   oci-layout
        ```

    5. Copie la imagen de OCI en el registro interno del clúster del aula con Skopeo y etiquétela como `1.0`. Use el nombre de host y el token recuperados en los pasos anteriores.

        Puede cortar y pegar el comando `skopeo copy` siguiente del script `push-image.sh` en la carpeta `/home/student/DO288/labs/expose-registry`.

        ```bash
        [student@workstation ~]$ skopeo copy --format v2s1 \
        --dest-creds=${RHT_OCP4_DEV_USER}:${TOKEN} \
        oci:/home/student/DO288/labs/expose-registry/ubi-info \
        docker://${INTERNAL_REGISTRY}/${RHT_OCP4_DEV_USER}-common/ubi-info:1.0
        ...output omitted...
        Writing manifest to image destination
        Storing signatures
        ```

    6. Verifique que se haya creado un flujo de imágenes para gestionar la nueva imagen de contenedor. La salida del comando `oc get is` se editó para mostrar cada columna en una sola línea con fines de legibilidad porque se espera que el nombre del repositorio de imágenes sea demasiado largo para ajustarse al ancho del papel.

        ```bash
        [student@workstation ~]$ oc get is
        NAME       IMAGE REPOSITORY
        ubi-info   default-route-openshift-image-registry.apps...
        ...output omitted...
        ```

3. Cree un contenedor local a partir de la imagen del registro interno de OpenShift.
    1. Descargue la imagen de contenedor `ubi-info:1.0` en el motor de contenedores local.

        ```bash
        [student@workstation ~]$ podman pull \
        ${INTERNAL_REGISTRY}/${RHT_OCP4_DEV_USER}-common/ubi-info:1.0
        ...output omitted...
        Writing manifest to image destination
        Storing signatures
        ...output omitted...
        ```

    2. Inicie un nuevo contenedor desde la imagen de contenedor `ubi-info:1.0`. En el contenedor se muestra información del sistema, como el nombre de host y la memoria libre; luego, se sale. En la siguiente salida, se omite información específica para el contenedor en ejecución:

        ```bash
        [student@workstation ~]$ podman run --name info \
        ${INTERNAL_REGISTRY}/${RHT_OCP4_DEV_USER}-common/ubi-info:1.0
        ...output omitted...
        --- Host name:
        ...output omitted...
        --- Free memory
        ...output omitted...
        --- Mounted file systems (partial)
        ...output omitted...
        ```
