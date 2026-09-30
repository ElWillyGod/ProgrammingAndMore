# Image Stream, Authenticating OpenShift to Private Registries

## Expose registry

Procedimiento completo del lab en [[Expose registry]].

## Docker

When deploying an app with a docker image its possible that you get the error "unable to locate any local docker image..." for that we need to create a secret

```bash
oc create secret docker-registry my-docker --docker-server docker.io --docker-username USER --docker-password PASSWORD

oc secrets link default my-docker --for pull   # link the secret to the sa

# Try creating the app again
```

## Podman - Quay

```bash
podman login quay.io

oc create secret generic my-quay --from-file .dockerconfigjson=${XDG_RUNTIME_DIR}/containers/auth.json --type kubernetes.io/dockerconfigjson

oc secrets link default my-quay --for pull   # link the secret to the sa
```

## El operador de registro de imágenes

El instalador de OpenShift configura el registro interno para que solo sea accesible desde el interior de su clúster de OpenShift. Exponer el registro interno para el acceso externo es un procedimiento sencillo, pero requiere privilegios de administración del clúster.

El *operador de registro de imágenes* de OpenShift administra el registro interno. Todas las opciones de configuración del operador de registro de imágenes se encuentran en el recurso de configuración del `cluster` en el proyecto `openshift-image-registry`. Cambie el atributo `spec.defaultRoute` a `true` y el operador de registro de imágenes creará una ruta para exponer el registro interno. Una forma de realizar ese cambio es con el siguiente comando `oc patch`:

```bash
oc patch config.imageregistry cluster -n openshift-image-registry --type merge -p '{"spec":{"defaultRoute":true}}'
```

La ruta `default-route` usa el nombre de dominio comodín predeterminado para la aplicación implementada en el clúster:

```bash
oc get route -n openshift-image-registry
NAME            HOST/PORT                                                  ...
default-route   default-route-openshift-image-registry.domain.example.com  ...
```

### Autenticación en un registro interno

Para iniciar sesión en un registro interno con las herramientas de contenedor de Linux, debe capturar el token de autenticación de OpenShift del usuario.

Use el comando `oc whoami -t` para capturar el token. El token es una larga cadena aleatoria. Es más fácil escribir comandos si guarda el token como una variable de shell:

```bash
TOKEN=$(oc whoami -t)
```

Use el token como parte de un subcomando `login` de Podman:

```bash
podman login -u myuser -p ${TOKEN} \
  default-route-openshift-image-registry.domain.example.com
```

También puede usar el token como valor de las opciones `--[src|dest]-creds` de Skopeo.

```bash
skopeo inspect --creds=myuser:${TOKEN} \
  docker://default-route-openshift-image-registry.domain.example.com/...
```

### Acceso al registro interno como registro seguro o inseguro

Si el clúster de OpenShift está configurado con un certificado TLS válido para su dominio comodín, puede usar las herramientas de contenedor de Linux para trabajar con imágenes dentro de cualquier proyecto al que tenga acceso.

En el ejemplo siguiente, se usa Skopeo para inspeccionar la imagen de contenedor de la aplicación `myapp` dentro del proyecto `myproj`. Se supone que un `podman login` anterior se realizó correctamente.

```bash
skopeo inspect \
  docker://default-route-openshift-image-registry.domain.example.com/myproj/myapp
```

Si el clúster de OpenShift usa la entidad de certificación (CA) que el instalador de OpenShift genera de forma predeterminada, debe tener acceso al registro interno como un registro inseguro:

```bash
skopeo inspect --tls-verify=false \
  docker://default-route-openshift-image-registry.domain.example.com/myproj/myapp
```
