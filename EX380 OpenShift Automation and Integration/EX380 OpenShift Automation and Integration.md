# RED HAT CERTIFIED SPECIALIST IN OPENSHIFT AUTOMATION AND INTEGRATION EX380

Duración: 4hs

Puntos para pasar: 210/300

# Auth and Identity Management

## LDAP

### Add new authentication provider LDAP

En consola web ir a **Administration → Cluster Settings → Configuration → OAuth**
Desplegamos Add y seleccionamos el provider LDAP.
Esto nos generara un formulario para la carga de los datos correspondientes.

Recomendable hacer pruebas antes de la implementación con `ldapsearch` para confirmar que se esta agregando los usuarios esperados unicamente.

Luego de configurado el Provider podremos validar desde el login de la consola que nos aparece este como alternativa del login.

### Automate LDAP Group Synchronization

Se requiere ya tener configurado el Provider de LDAP.

Generar un proyecto para los recursos de sincronizacion.
En este generar una SA dedicada para esta tarea y asignarle permisos correspondientes:

```bash
oc new-project auth-rhds-sync
oc create sa rhds-group-syncer
oc create clusterrole rhds-group-syncer \
  --verb get,list,create,update --resource groups
oc adm policy add-cluster-role-to-user \
  rhds-group-syncer -z rhds-group-syncer
```

Generar secret en el proyecto para autenticacion con LDAP:

```bash
oc create secret generic ldap-secret \
  --from-literal bindPassword='redhatocp'
```

Crear archivo con la declaración del objeto LDAPSyncConfig (A continuación dejo ejemplo de labs):

```yaml
kind: LDAPSyncConfig
apiVersion: v1
url: ldaps://rhds.ocp4.example.com:636
bindDN: 'cn=Directory Manager'
bindPassword:
  file: /etc/secrets/bindPassword
ca: /etc/config/ca.crt
augmentedActiveDirectory:
    groupsQuery:
        baseDN: "ou=people,dc=example,dc=com"
        scope: sub
        derefAliases: never
        pageSize: 0
    groupUIDAttribute: dn
    groupNameAttributes: [ cn ]
    usersQuery:
        baseDN: "ou=people,dc=example,dc=com"
        scope: sub
        derefAliases: never
        filter: (objectclass=account)
        pageSize: 0
    userNameAttributes: [ uid ]
    groupMembershipAttributes: [ memberOf ]
```

Luego de generado el archivo proceder con la creacion de un CM incluyendo el LDAPSyncConfig y los certificados:

```bash
oc create configmap rhds-config \
  --from-file rhds-sync.yaml=rhds-sync.yaml,ca.crt=rhds_ca.crt
```

Se debera posterior a la configuración generar un cronjob para que ejecute la sincronización:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: rhds-group-sync
  namespace: auth-rhds-sync
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: Never
          containers:
            - name: ldap-group-sync
              image: "registry.ocp4.example.com:8443/openshift4/ose-cli:v4.12"
              command:
                - "/bin/sh"
                - "-c"
                - "oc adm groups sync --sync-config=/etc/config/rhds-sync.yaml --confirm"
              volumeMounts:
                - mountPath: "/etc/config"
                  name: "ldap-sync-volume"
                - mountPath: "/etc/secrets"
                  name: "ldap-bind-password"
          volumes:
            - name: "ldap-sync-volume"
              configMap:
                name: "rhds-config"
            - name: "ldap-bind-password"
              secret:
                secretName: "ldap-secret"
          serviceAccountName: rhds-group-syncer
          serviceAccount: rhds-group-syncer
```

## OIDC

### OIDC Authentication and Group Claims

### Solve User Sync Conflicts

## Token and Client Certificate Authentication

# OADP (OpenShift API for Data Protection)

> OADP uses several components such as Velero and Kopia to back up all the Kubernetes resources, container images from the internal registry, and persistent volumes that are associated with an application.
>
> **Velero**
> Velero is the main upstream component of OADP. It provides the backup API to the users and uses plug-ins to add extended capabilities to OADP.
>
> **Data Mover**
> This feature enables exporting snapshot content to object storage by using Kopia and the CSI plug-in.
>
> **Kopia**
> Open source backup tool that Velero and Data Mover use to back up persistent volumes.

## Operator Deployment and Features

Se procedera a la instalacion del operador desde el OperatorHub. Para esto se debera acceder a **Operators → OperatorHub** y buscar en el catalogo OADP.

Para continuar con la configuracion del operador se requiere contar con un bucket S3 compatible. En este caso nos brindan ODF para el aprovisionamiento del mismo, esta creación se realiza aplicando el siguiente YAML de ejemplo (Tambien se puede crear desde el dashboard por UI):

```yaml
apiVersion: objectbucket.io/v1alpha1
kind: ObjectBucketClaim
metadata:
  name: backup
  namespace: openshift-adp # Se creo en el mismo NS que OADP
spec:
  storageClassName: openshift-storage.noobaa.io
  generateBucketName: backup
```

Luego desde **Storage → Object Storage → Claim Name** vamos a encontrar generado el Storage generado en el paso previo y podremos exportar los datos necesarios para la configuración de OADP (Estos datos se alojaron en un CM y Secret con el nombre del Claim en el NS indicado).

Luego de obtenido los datos se debe generar el secret para OADP, por defecto lo llamaremos `cloud-credentials` con una key llamada `cloud` con valor un archivo de la siguiente manera:

```ini
[default]
aws_access_key_id=AWS_ACCESS_KEY_ID
aws_secret_access_key=AWS_SECRET_ACCESS_KEY
```

```bash
oc create secret generic cloud-credentials -n openshift-adp --from-file cloud=credentials-velero
```

Luego de aprovisionado estos prerequisitos aplicamos el yaml para la creacion del DPA:

```yaml
apiVersion: oadp.openshift.io/v1alpha1
kind: DataProtectionApplication
metadata:
  name: oadp-backup
  namespace: openshift-adp
spec:
  configuration:
    nodeAgent:
      enable: true
      uploaderType: kopia
    velero:
      defaultPlugins:
        - aws
        - openshift
        - csi
      defaultSnapshotMoveData: true
  backupLocations:
    - velero:
        config:
          profile: "default"
          region: "us-east-1"
          s3Url: https://s3.openshift-storage.svc
          s3ForcePathStyle: "true"
          insecureSkipTLSVerify: "true"
        provider: aws
        default: true
        credential:
          key: cloud
          name: cloud-credentials
        objectStorage:
          bucket: backup-name
          prefix: oadp
```

Hay que configurar las VolumeSnapshotClass que seran usadas por OADP agregando la siguiente label:

```bash
oc label volumesnapshotclass <nombre> velero.io/csi-volumesnapshot-class=true
```

Luego de esta configuración se podrá proceder con la creación de objetos Backup (Estos se pueden generar via UI con formulario).

Ejemplo de YAML:

```yaml
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: backup-production
  namespace: openshift-adp
spec:
  includedNamespaces:
  - production
```

## Velero Tool

```bash
alias velero='oc -n openshift-adp exec deployment/velero -c velero -it -- ./velero'
```

Alias para el uso del comando de Velero. Ejemplo de usos:

```bash
velero get restore
velero get backup
velero describe backup website --details
velero describe restore website-dev --details
```

## Backups

> [!info] Hooks
> **Pre backup hooks**
> A `pre` backup hook is executed before any other backup action on the pod. If the command fails, then the backup stops immediately with the `Failed` status.
> As an example, you can use this type of hook to quiesce and prepare the application for backup.
>
> **Post backup hooks**
> A `post` backup hook is executed after the backup of the pod and its attached volumes. If the command fails, then the backup stops immediately with the `PartiallyFailed` status.
> As an example, you can use this type of hook to resume or unlock the application after the backup is complete.
>
> **Init restore hooks**
> An `init` restore hook is executed after the pod and its attached volumes are restored, but before any container on that pod starts. The `init` restore hook defines one or more init containers that follow the same specification as the init container in a pod definition.
> OADP does not monitor the status of the init container. Therefore, if the command fails, then the restore continues without any error or warning, but the application pod is in error with the `Init:Error` status.
> As an example, you can use this type of hook to restore a database that the application requires and that is external to the OpenShift cluster.
>
> **Post restore hooks**
> A `post` restore hook is executed when the application pod is restored and running. If the command fails, then the error is logged and the restore continues

### Scheduling

Existe un objeto Schedule el cual declara que componentes a respaldar y con que periodicidad, este utiliza labels para la busqueda de que componente respaldar.

Ejemplo de YAML:

```yaml
apiVersion: velero.io/v1
kind: Schedule
metadata:
  name: website-daily
  namespace: openshift-adp
  labels:
    app: hugo
spec:
  schedule: "0 7 * * *"
  paused: false
  template:
    includedNamespaces:
    - website
    labelSelector:
      matchLabels:
        app: hugo
    includedResources:
    - imagestreams
    - buildconfigs
    - deployments
    - services
    - routes
    ttl: 720h0m0s
```

Este objeto segun el Cron establecido ira generando los objetos Backup correspondientes.
Se puede generar un respaldo a partir de esta programacion con el siguiente comando:

```bash
velero create backup <backup-name> --from-schedule <schedule-name>
```

### Backup Volumes with File System

Con esta caracteristica (File System Backup) nos permite respaldar los volumenes que no son compatibles con snapshots.

Esta se puede configurar para que respalde todos los volumenes del objeto respaldo utilizando este metodo de la siguiente manera:

```yaml
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: <backup-name>
  namespace: openshift-adp
spec:
  defaultVolumesToFsBackup: true
```

Tambien se puede indicar puntualmente cierto volumen de X Deployment, esto se realiza agregando una annotation en el deployment.
Ejemplo:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: website-nginx
  namespace: org-website
spec:
  template:
    metadata:
      annotations:
        backup.velero.io/backup-volumes: wwwdata # Lista de volumenes a respaldar con FSB
    spec:
      containers:
      - name: web
        image: registry.access.redhat.com/ubi9/nginx-120
        # ...output omitted...
        volumeMounts:
        - mountPath: /opt/app-root/src
          name: wwwdata
      # ...output omitted...
      volumes:
      - name: wwwdata
        persistentVolumeClaim:
          claimName: nginx-wwwdata
# ...output omitted...
```

# Cluster Partitioning

## Node Pools

> Es una agrupación logica de nodos de OCP. Los cuales comparten configuraciones similares de hardware.
> Estos grupos nos permiten segun sus características asignar determinadas cargas de trabajo (Ej. base de datos).

### Node Provisioning

### UPI (User-Provisioned Infrastructure)

> La instalación de las instancias nuevas se realizan de manera manual.

### IPI (Installer-Provisioned Infrastructure)

> La instalación de nuevas instancias se realiza de manera desatendida gracias a integración de la plataforma con la infraestructura.

### Node Labels

> Son objetos key-value los cuales permiten a traves de la asignación la incorporación de un pool de nodos especifico.
> En caso de IPI, esta config va en MachineSet.
> En caso de UPI, esta se realiza en Nodes.

## Node Configuration with the Machine Configuration Operator

> OpenShift uses Red Hat Enterprise Linux CoreOS (RHCOS) as the underlying operating system in the hosts. The entire operating system is updated as a single image, instead of on a package-by-package basis. The machine configuration operator (MCO) manages the RHCOS operating system upgrades and configuration changes.

### Machine Configurations

> [!info] 📖 MachineConfig
> A machine configuration (MC) CR declares instance customizations by using the Ignition configuration format.

### Machine Configuration Pools

> [!info] 📖 MachineConfigPool
> A machine configuration pool (MCP) CR uses labels to match one or more MCs to one or more nodes by means of the `machineConfigSelector` and `nodeSelector` parameters, respectively. This resource creates a pool of nodes with the same configuration. The MCO uses the MCP to track status when the MCO applies MCs to the nodes.

## Node Configuration with Special Purpose Operators

# Pod Scheduling

## Default Pod Scheduler

> The default OpenShift pod scheduler determines the placement of pods onto nodes within the cluster. The scheduler reads data from the pod and identifies a suitable node based on configured profiles. After identifying the most suitable node, the scheduler creates a binding that associates the pod with a specific node, without modifying the pod.

Para la determinación de asignación de un pod en un nodo se sigue el siguiente procedimiento:

- Node Filtering
- Prioritize the filtered list of nodes
- Select the best node

**Scheduler Profiles**

*LowNodeUtilization:* Reparte carga entre nodos intentando dejar lo mas bajo posible el uso de recursos por nodo. Configuracion por defecto en el scheduler.

*HighNodeUtilization:* Intenta que la carga quede en la menor cantidad de nodos posibles. Esto disminuye la cantidad de nodos pero aumenta la carga de los operativos.

*NoScoring:* Deshabilita todas las mediciones para el scheduling y asigna lo mas rapido posible. No tiene criterio, es el primero que encuentre.

## Advanced Pod Scheduling

### Node Selectors

Esto permite en el recurso marcado que unicamente se pueda schedulear en un nodo con la label indicada.

### Affinity and Anti-affinity Rules

**Affinity**
refers to a pod property that controls the pod preference for a node.

**Anti-affinity**
is a pod property that restricts the placement of the pod on a node.

**Node Affinity**
Es similar a Node Selector con mas flexibilidad, permitiendo agregar un condicional con operaciones logicas en el proceso de seleccion de nodo.

**Pod affinity and anti-affinity**
Esta propiedad permite el juntar pods en el mismo nodo (affinity) o separar estos (anti-affinity). Esta herramienta es util para separar ejemplo carga de trabajo que tiene alto consumo de CPU entre los nodos y dejar en el mismo nodo carga de trabajo de un mismo producto.

Estas se separan en dos grupos de reglas required y preferred. Required es un si o no, si no se cumple no se realiza. Mientras que preferred como su nombre indica es la preferencia, que aunque no se llegue a cumplir se levantara en otro nodo.

### Taints and Tolerations

### Pod Disruption Budget

# Preguntas

## Configurar LDAP como Identity Provider en OCP (vía UI)

> [!tip]- 💡 Solución
> #### Ir a la configuración de OAuth
>
> 1. Ingresá a la **consola web de OCP** como `cluster-admin`
> 2. En el menú lateral izquierdo: **Administration** → **Cluster Settings**
> 3. Clickear en la pestaña **Configuration**
> 4. Buscar y clickear **OAuth**
>
> #### Agregar el Identity Provider
>
> 1. Bajar hasta la sección **Identity Providers**
> 2. Clickear **Add** → seleccionar **LDAP**
>
> #### Completar el formulario LDAP
>
> | Campo | Valor |
> | --- | --- |
> | **Name** | `ldap-corporativo` |
> | **URL** | `ldap://ldap.empresa.com:389/ou=users,dc=empresa,dc=com?uid?sub` |
> | **Bind DN** | `cn=admin,dc=empresa,dc=com` |
> | **Bind Password** | contraseña del usuario de servicio |
> | **CA Certificate** | *(dejar vacío)* |
> | **Insecure** | ✅ activar este checkbox |
>
> ⚠️ El checkbox **Insecure** es obligatorio marcarlo en puerto 389, de lo contrario OCP intentará validar un certificado TLS que no existe y fallará la conexión.
>
> El scope `sub` al final de la URL hace que busque en todo el árbol desde el base DN, y al no tener filtro **todos los usuarios del LDAP podrán autenticarse**.
>
> #### Configurar atributos
>
> En la sección **Attributes** del formulario:
>
> | Atributo OCP | Campo LDAP |
> | --- | --- |
> | **ID** | `dn` |
> | **Preferred Username** | `uid` |
> | **Name** | `cn` |
> | **Email** | `mail` |
>
> #### Guardar y verificar
>
> 1. Clickear **Add** para guardar
> 2. El OAuth operator se reiniciará automáticamente (tarda 1-2 minutos)
> 3. Verificar en **Cluster Settings** → **Configuration** → **OAuth** que aparezca el IDP listado
>
> #### Verificar que funciona
>
> Desde la consola web:
>
> 1. Abrir una ventana de incógnito
> 2. Ir a la consola de OCP
> 3. Debería aparecer el botón **ldap-corporativo** en la pantalla de login
> 4. Intentar loguearse con un usuario del LDAP

## Certificado de Usuario con CA Interna del Clúster OCP

> [!tip]- 💡 Solución
> #### Generar la clave y el CSR del usuario
>
> Crear clave privada
> ```bash
> openssl genrsa -out usuario.key 2048
> ```
> Crear el CSR
> ```bash
> openssl req -new -key usuario.key -out usuario.csr \
>   -subj "/CN=nombre-usuario/O=nombre-grupo"
> ```
>
> #### Enviar el CSR al clúster para que lo firme la CA interna
>
> ```bash
> cat usuario.csr | base64 | tr -d '\n'
> ```
> Crear el objeto `CertificateSigningRequest` en el clúster:
> ```yaml
> # csr-usuario.yaml
> apiVersion: certificates.k8s.io/v1
> kind: CertificateSigningRequest
> metadata:
>   name: nombre-usuario-csr
> spec:
>   request: <BASE64_DEL_CSR_AQUI>
>   signerName: kubernetes.io/kube-apiserver-client
>   expirationSeconds: 31536000  # 1 año
>   usages:
>   - client auth
> ```
> ```bash
> oc apply -f csr-usuario.yaml
> ```
>
> #### Aprobar el CSR desde el clúster
>
> ```bash
> # Ver el CSR pendiente
> oc get csr
>
> # Aprobar
> oc adm certificate approve nombre-usuario-csr
> ```
>
> #### Descargar el certificado firmado
>
> ```bash
> oc get csr nombre-usuario-csr \
>   -o jsonpath='{.status.certificate}' | base64 -d > usuario.crt
>
> # Verificar el certificado
> openssl x509 -in usuario.crt -noout -text | grep -E "Subject:|Issuer:|Not After"
> ```
>
> #### Obtener la CA del clúster (para que el usuario pueda verificar el servidor)
>
> ```bash
> oc get cm kube-root-ca.crt -n default \
>   -o jsonpath='{.data.ca\.crt}' > ca-cluster.crt
> ```

## Arreglar taint en nodos

## Restaurar aplicación de velero

## Configurar ClusterLogForwarder (CLF) con múltiples Syslog

> [!tip]- 💡 Solución
> #### ClusterLogForwarder YAML
>
> ```yaml
> # clusterlogforwarder.yaml
> apiVersion: logging.openshift.io/v1
> kind: ClusterLogForwarder
> metadata:
>   name: instance
>   namespace: openshift-logging
> spec:
>   outputs:
>     - name: syslog-app-infra        # Syslog X → app e infra
>       type: syslog
>       syslog:
>         facility: user
>         severity: informational
>         rfc: RFC5424
>         appName: openshift
>         msgID: "-"
>         procID: "-"
>       url: tcp://X.X.X.X:514        # IP/host del syslog X
>     - name: syslog-audit            # Syslog Y → audit
>       type: syslog
>       syslog:
>         facility: auth
>         severity: warning
>         rfc: RFC5424
>         appName: openshift-audit
>         msgID: "-"
>         procID: "-"
>       url: tcp://Y.Y.Y.Y:514        # IP/host del syslog Y
>   pipelines:
>     - name: pipeline-app-infra
>       inputRefs:
>         - application       # logs de app
>         - infrastructure    # logs de infra
>       outputRefs:
>         - syslog-app-infra
>     - name: pipeline-audit
>       inputRefs:
>         - audit             # logs de audit
>       outputRefs:
>         - syslog-audit
> ```
>
> #### Aplicar y verificar
>
> ```bash
> # Aplicar
> oc apply -f clusterlogforwarder.yaml
>
> # Verificar que el CLF no tenga errores
> oc get clusterlogforwarder instance -n openshift-logging -o yaml | grep -A 20 "status:"
>
> # Ver los pods del colector (vector/fluentd)
> oc get pods -n openshift-logging
>
> # Ver logs del colector para detectar errores de conexión
> oc logs -n openshift-logging -l component=collector --tail=50
> ```
>
> #### Verificar que los logs llegan al Syslog
>
> ```bash
> # Desde el servidor Syslog X, filtrar por app/infra
> tail -f /var/log/syslog | grep openshift
>
> # Desde el servidor Syslog Y, filtrar por audit
> tail -f /var/log/syslog | grep openshift-audit
> ```

## Instalar operador de GitOps y configurar instancia de Argo

## Agregar Banner de Login en Nodos OCP via MachineConfig

> [!tip]- 💡 Solución
> #### MachineConfig para el Banner
>
> `/etc/issue` es el banner que se muestra **antes** del login; `/etc/motd` se muestra **después** de loguearse. Usar el que pida el ejercicio (o ambos).
>
> ```yaml
> # machineconfig-banner.yaml
> apiVersion: machineconfiguration.openshift.io/v1
> kind: MachineConfig
> metadata:
>   name: 99-worker-login-banner
>   labels:
>     machineconfiguration.openshift.io/role: worker
> spec:
>   config:
>     ignition:
>       version: 3.2.0
>     storage:
>       files:
>         - path: /etc/motd
>           mode: 0644
>           overwrite: true
>           contents:
>             source: data:text/plain;charset=utf-8;base64,<BASE64_DEL_BANNER>
> ```
>
> #### Generar el BASE64 del texto del banner
>
> ```bash
> cat << 'EOF' > banner.txt
> *******************************************************************************
> *                                                                             *
> *   ACCESO RESTRINGIDO - SOLO PERSONAL AUTORIZADO                            *
> *                                                                             *
> *   El uso de este sistema es monitoreado. Todo acceso no autorizado         *
> *   será reportado y puede resultar en acciones legales.                     *
> *                                                                             *
> *******************************************************************************
> EOF
>
> # Generar el base64 para pegar en el YAML
> cat banner.txt | base64 | tr -d '\n'
> ```
>
> Pegá el resultado en el campo `source`:
> ```yaml
> source: data:text/plain;charset=utf-8;base64,KioqKioqKioq...
> ```
>
> #### Aplicar el MachineConfig
>
> ```bash
> oc apply -f machineconfig-banner.yaml
> ```
>
> #### Verificar el rollout
>
> ```bash
> # Ver el estado del MachineConfigPool
> oc get mcp
>
> # Ver detalle (los nodos se van reiniciando de a uno)
> oc get mcp worker -o wide
>
> # Ver el progreso en los nodos
> oc get nodes
> ```
>
> La salida de `oc get mcp` debería mostrar algo así durante el proceso:
> ```text
> NAME     CONFIG                UPDATED   UPDATING   DEGRADED
> master   rendered-master-...   True      False      False
> worker   rendered-worker-...   False     True       False  # <-- actualizando
> ```
>
> #### Verificar que el banner quedó aplicado
>
> ```bash
> # Entrar a un nodo y verificar
> oc debug node/<nombre-nodo>
> chroot /host
> cat /etc/motd
> cat /etc/issue
> ```
