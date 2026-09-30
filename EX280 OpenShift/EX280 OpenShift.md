# OPENSHIFT ADMINISTRATOR EX280

Duración: 3hs

Puntos para pasar: 210/300

> [!tip] 💡
> Al inicio te darán una **ubicación** en donde obtendrás la contraseña del usuario **kubeadmin**. De ahí puedes desde la consola web a través de un link que te dan modificar los recursos para evitar errores de indexación.
>
> Leer los ejercicio completo previo a comenzar con las tareas.
>
> No remover parametros de los objetos generados por defecto, unicamente modificar valores a menos que sea pedido explicito.
>
> Si es posible realizar tareas desde Web para evitar errores de sintaxis.

## Configura el proveedor de identidad de openshift

Crea los **usuarios:**

- Jobs con la password deluges.
- Wozniak con la password grannies.
- Collins con la password culverins.
- Adlerin con la password artiste.
- Armstrong con la password spacesuits.

Configura **Htpasswd** como proveedor de identidad, el nombre del secreto debe ser "**htpass-idp-ex280**" y el nombre del proveedor "**ex280-htpasswd**".

> [!tip]- 💡 Solución
> Crear los usuarios con el archivo (`-c` se usa solo la primera vez para crear el archivo)
> ```bash
> htpasswd -c -B -b users.htpasswd jobs deluges
> htpasswd -B -b users.htpasswd wozniak grannies
> htpasswd -B -b users.htpasswd collins culverins
> htpasswd -B -b users.htpasswd adlerin artiste
> htpasswd -B -b users.htpasswd armstrong spacesuits
>
> oc create secret generic htpass-idp-ex280 --from-file=htpasswd=users.htpasswd -n openshift-config
> ```
> **Administration → Cluster Settings → Configuration**, buscar oauth y agregar el Identity provider htpasswd (**recomendado**)

> [!check]- 🧪 Testeo
> Se recomienda realizarlo con todos los usuarios y al final del examen.
> ```bash
> oc get pods -n openshift-authentication
> oc login -u user -p password
> oc get users
> ```

---

## Configura permisos de Cluster

**Permisos:**

- jobs puede modificar el cluster.
- wozniak no debe de poder aprovisionar proyectos.
- collins debe de poder aprovisionar proyectos.
- kubeadmin debe ser eliminado (no es recomendable hacerlo al inicio del examen, de ser posible realizarlo al final).

> [!tip]- 💡 Solución
> Agregar permisos de cluster a Jobs
> ```bash
> oc adm policy add-cluster-role-to-user cluster-admin jobs
> ```
> Eliminar rol de aprovisionamiento que está por defecto para todos los usuarios.
> ```bash
> oc adm policy remove-cluster-role-from-group self-provisioner system:authenticated:oauth
> ```
> Asignar el rol de aprovisionamiento a collins
> ```bash
> oc adm policy add-cluster-role-to-user self-provisioner collins
> ```
> Eliminar el usuario kubeadmin
> ```bash
> oc delete secrets kubeadmin -n kube-system
> ```

> [!check]- 🧪 Testeo
> Mira los Cluster Role Binding que se crearon
> ```bash
> oc get clusterrolebinding
> ```
> Prueba aprovisionar un proyecto con collins y otro usuario que no tenga permisos
> ```bash
> oc new-project test   # con collins y otro usuario
> ```

---

## Configura permisos de Proyectos

**Crea los proyectos:**

- apollo
- titan
- gemini
- bluebook
- apache

**Permisos:**

- armstrong es administrador del proyecto apollo y titan
- collins puede ver el proyecto apollo.

> [!tip]- 💡 Solución
> Crea los proyectos:
> ```bash
> oc new-project apollo
> oc new-project titan
> oc new-project gemini
> oc new-project bluebook
> oc new-project apache
> ```
> collins puede ver el proyecto apollo:
> ```bash
> oc policy add-role-to-user view collins -n apollo
> ```
> armstrong es administrador del proyecto apollo y titan:
> ```bash
> oc policy add-role-to-user admin armstrong -n apollo
> oc policy add-role-to-user admin armstrong -n titan
> ```

---

## Manejo de grupos

Crea dos grupos commander y pilot.

Añade el usuario adlerin como parte del grupo commander.

Añade a armstrong y wozniak como parte del grupo pilot.

**Permisos:**

- El grupo commander puede administrar el proyecto gemini.
- El grupo pilot puede ver el proyecto gemini.

> [!tip]- 💡 Solución
> ```bash
> # Crear los grupos
> oc adm groups new commander
> oc adm groups new pilot
>
> # Añadir usuarios
> oc adm groups add-users commander adlerin
> oc adm groups add-users pilot armstrong wozniak
>
> # commander administra gemini, pilot puede verlo
> oc policy add-role-to-group admin commander -n gemini
> oc policy add-role-to-group view pilot -n gemini
> ```

> [!check]- 🧪 Testeo
> Observa los grupos creados
> ```bash
> oc get groups
> ```

---

## Cuotas de recursos

Crea una cuota de recursos llamada "ex280-quota" en el proyecto "manhattan".

- La cantidad de memoria consumida de todos los contenedores no puede exceder 1Gi.
- La cantidad de CPU de todos los contenedores no debe exceder 2 cores.
- El número máximo del controlador de replicación no puede exceder los 3.
- El número máximo de pods no puede exceder 3.
- El número máximo de servicios no pueden ser más de 6.
- El número máximo de secretos debe ser 6.

> [!tip]- 💡 Solución
> ```bash
> oc create quota ex280-quota --hard=cpu=2,memory=1Gi,pods=3,services=6,replicationcontrollers=3,secrets=6 -n manhattan
> ```

> [!check]- 🧪 Testeo
> ```bash
> oc get quota -n manhattan
> ```

---

## Rango de límites de un proyecto

Crea un "LimitRange" llamado "ex280-limits" en el proyecto "bluebook".

- La cantidad de memoria consumida por un solo pod es entre 100Mi y 300Mi.
- La cantidad de cpu consumida un solo pod es entre 10m y 500m.
- La cantidad de cpu consumida por un solo contenedor debe ser entre 10m y 500m con un valor por defecto de 100m.
- La cantidad de memoria consumida por un solo contenedor es entre 100Mi y 300Mi con un valor por defecto de 100Mi.

> [!tip]- 💡 Solución
> ```yaml
> apiVersion: v1
> kind: LimitRange
> metadata:
>   name: ex280-limits
>   namespace: bluebook
> spec:
>   limits:
>   - max:
>       memory: 300Mi
>       cpu: 500m
>     min:
>       memory: 100Mi
>       cpu: 10m
>     type: Pod
>   - max:
>       memory: 300Mi
>       cpu: 500m
>     min:
>       memory: 100Mi
>       cpu: 10m
>     defaultRequest:
>       memory: 100Mi
>       cpu: 100m
>     type: Container
> ```

> [!check]- 🧪 Testeo
> ```bash
> oc get limitranges -n bluebook
> oc describe limitranges ex280-limits -n bluebook
> ```

---

## Escalar una aplicación

Escala una aplicación llamada "hydra" en el proyecto llamado "lerna" a 5.

> [!tip]- 💡 Solución
> ```bash
> oc scale deploy/hydra --replicas=5 -n lerna
> ```

---

## Autoescalado

Configura el autoescalado para una aplicación llamada minion en el proyecto "gru" con:

- Número mínimo de 6 réplicas.
- Número máximo de 40 réplicas.
- Límite de uso de porcentaje de cpu en 60
- Recursos de cpu para la aplicación requeridos: 25m
- Limites de cpu para la aplicación: 100m
- Recursos de memoria para la aplicación requeridos: 64Mi
- Limites de memoria para la aplicación: 256Mi

> [!tip]- 💡 Solución
> Verifica que no haya una taint que impida que el pod esté en estado "Running".
> ```bash
> oc describe nodes | grep -i "Taints"
> ```
> Si hay un taint configurado agrega una configuración de tolerancia en el deployment en la sección de especificaciones (key/value deben coincidir exactamente con la taint, distingue mayúsculas):
> ```yaml
> tolerations:
> - effect: NoSchedule
>   key: Node
>   value: Worker
>   operator: Equal
> ```
> Configurar el autoescalado y los límites:
> ```bash
> oc autoscale deploy/minion --min=6 --max=40 --cpu-percent=60 -n gru
> oc set resources deploy/minion --limits=cpu=100m,memory=256Mi --requests=cpu=25m,memory=64Mi -n gru
> ```

---

## Secrets

Crea un Secret en el proyecto "math", el nombre debe ser "magic".

El secreto debe de contener el siguiente par claves valor:

**Decoder_Ring: ASDA142hfh-gfrhhueo-erfdk345v**

> [!tip]- 💡 Solución
> ```bash
> oc create secret generic magic --from-literal=Decoder_Ring=ASDA142hfh-gfrhhueo-erfdk345v -n math
> ```

---

## Usa el secreto

Usa el secreto en la aplicación llamada "qed" en el proyecto math para que use el secreto "magic". Luego de configurar la variable de entorno debería de dejar de aparecer el mensaje "App is not configured properly".

> [!tip]- 💡 Solución
> ```bash
> oc set env --from=secret/magic deployment/qed -n math
> ```

---

## Crea una ruta segura

Crea una ruta segura para la aplicación corriendo en el proyecto "area51" con el nombre "oxcart". La aplicación debe estar disponible en el url dada y la aplicación debe usar certificados autofirmados con la información de: "/C=SI/ST=Ljubljana/L=Ljubljana/O=Security/OU=IT Department/CN=scale-area51.apps.ocp4.example.com"

> [!tip]- 💡 Solución
> Crea la key y el certificado
> ```bash
> openssl req -x509 -newkey rsa:2048 -keyout training.key -out req.crt -nodes -days 365 -subj "/C=SI/ST=Ljubljana/L=Ljubljana/O=Security/OU=IT Department/CN=scale-area51.apps.ocp4.example.com"
>
> oc get svc -n area51
>
> oc create route edge oxcart --service=httpd --hostname=scale-area51.apps.ocp4.example.com --key=training.key --cert=req.crt -n area51
>
> oc get route -n area51
> ```
> El resultado debería de ser la ip del server.

---

## Crea una sa

Crea un sa con el nombre "ex280-sa" para que la aplicación pueda correr con "any user id"

> [!tip]- 💡 Solución
> ```bash
> oc create sa ex280-sa
> oc adm policy add-scc-to-user anyuid -z ex280-sa
> ```

---

## Helm chart

Instala un helm chart etherpad del repositorio http://helm.ocp4.example.com/charts

> [!tip]- 💡 Solución
> ```bash
> helm repo add do280-repo http://helm.ocp4.example.com/charts
> helm search repo --versions
> helm install example-app do280-repo/etherpad
> oc get all
> ```

---

## Operador

Instala un operador llamado file-integrity-manager en plan automático y usando el proyecto openshift-file-integrity

> [!tip]- 💡 Solución
> Usando la interfaz web. El modo de instalación es en todos los namespaces.

---

## Cronjob

Crea un cronjob llamado test-cron:

- Hora 04:05
- Cada dos días todos los meses
- La service account y el nombre de la service account debe se project1-sa
- Usa el registro de imagenes registry.io/imagename
- Limite de 14 de histroia exitosa
- El nombre del proyecto debe ser cron-test

> [!tip]- 💡 Solución
> Crea el cronjob usando un .yaml de la documentación o desde la interfaz web.
> - Cambia el `image:` de spec con la imagen que te dan.
> - Cambia el `schedule` con la fecha y hora apropiada: `"5 4 */2 * *"`
> - Cambia el `successfulJobsHistoryLimit` a 14 (el que te dan)
>
> ```bash
> oc create sa project1-sa -n cron-test
> oc adm policy add-scc-to-user anyuid -z project1-sa -n cron-test
> oc adm policy add-cluster-role-to-user cluster-admin -z project1-sa -n cron-test
> oc set sa cronjob.batch/test-cron project1-sa -n cron-test
> ```

---

## Crea una network policy para conectar dos pods de diferentes proyectos

- El primer pod está en el namespace "database" y el segundo en el proyecto "checker"
- El pod en el proyecto checker debe comunicarse con el pod de database
- Crea una network policy con el nombre de "mysql-db-conn"
- La policy usa labels para especificar el namespace con "team=devsecops" y para los selector de pods usa el label deployment=web-mysql
- Usa el puerto 3306/tcp
- Verifica los logs del proyecto "checker" para verificar que todo funcione correctamente.

> [!tip]- 💡 Solución
> ```yaml
> apiVersion: networking.k8s.io/v1
> kind: NetworkPolicy
> metadata:
>   name: mysql-db-conn
>   namespace: database  # Namespace donde se encuentra el pod de base de datos
> spec:
>   podSelector:
>     matchLabels:
>       deployment: web-mysql  # Label del pod objetivo en el namespace database
>   ingress:
>   - from:
>     - namespaceSelector:
>         matchLabels:
>           team: devsecops  # Label del namespace 'checker'
>     ports:
>     - protocol: TCP
>       port: 3306  # Puerto MySQL
> ```
> Si el namespace checker no tiene el label: `oc label namespace checker team=devsecops`

---

## Crea volúmenes persistentes

**Crea un pv:**

- Nombre es landing-pv
- Size es de 1Gi
- Policy: retain
- Mode: ReadOnlyMany

**Crea un pvc:**

- Nombre es landing-pvc
- Size es de 1Gi
- Mount pvc to /usr/share/nginx/html
- El nombre del proyecto es "page"
- Mode: ReadOnlyMany

**Crea un deployment:**

- Nombre es landing
- El registro de la imgen es: registry.ocp4.example.com:8443/redhattraining/hello-world-nginx:v1.0
- La aplicacion usa el link "http://content.example.ocp4.com" para mostrar el output.
- Luego de atachear el storage debería de mostrar un output válido.

> [!tip]- 💡 Solución
> Chequear el storageclass
> ```bash
> oc describe storageclass nfs2
> ```
> Observa el path "path=/open001" y el server "server=192.168.50.254"
>
> Crea el pv desde la interfaz web:
> ```yaml
> apiVersion: v1
> kind: PersistentVolume
> metadata:
>   name: landing-pv
> spec:
>   capacity:
>     storage: 1Gi
>   accessModes:
>     - ReadOnlyMany
>   persistentVolumeReclaimPolicy: Retain
>   storageClassName: nfs2  # Debe coincidir con el del PVC para que se enlacen
>   nfs:
>     path: /open001  # Ruta del servidor NFS
>     server: 192.168.50.254  # IP del servidor NFS
> ```
> Crea el pvc desde la interfaz web:
> ```yaml
> apiVersion: v1
> kind: PersistentVolumeClaim
> metadata:
>   name: landing-pvc
>   namespace: page
> spec:
>   accessModes:
>     - ReadOnlyMany
>   resources:
>     requests:
>       storage: 1Gi
>   storageClassName: nfs2
>   volumeName: landing-pv  # Fuerza que use el PV creado
> ```
> El deployment también desde la interfaz web:
> - Nombre: landing
> - ImageName: registry.ocp4.example.com:8443/redhattraining/hello-world-nginx:v1.0
>
> Luego ve al apartado de "Add storage" desde la interfaz web.
> Usa el pvc existente llamada landing-pvc
> Especifica el mountpath de: /usr/share/nginx/html
> ```bash
> oc expose deployment landing --name=landing-service --port=80 --target-port=80 --type=ClusterIP -n page
> oc expose svc landing-service --hostname=content.example.ocp4.com -n page
> ```
> Chequea la ruta

---

## Project Template y Limitrange

- Crea un project template con un limit range:
- La memoria mínima en 5Mi y al máxima es 1Gi. El default request es 254Mi y el defualtlimit es 512Mi
- Asegúrate de que el template esté disponible por defecto al crear un nuevo proyecto para cada usuario.

> [!tip]- 💡 Solución
> Crea un limit range con los valores pedidos desde la consola, llámalo mem-limit-range.
> Luego obtenemos el template por defecto y ese limit range con:
> ```bash
> oc adm create-bootstrap-project-template -o yaml > template.yaml
> oc get limitranges mem-limit-range -o yaml > file
>
> vim template.yaml   # une el limit range con el template
> ```
> En la parte de metadata del limit range pon `name: ${PROJECT_NAME}-limits`
> ```bash
> oc create -f template.yaml -n openshift-config
> oc edit projects.config.openshift.io cluster
> ```
> En spec añade:
> ```yaml
> spec:
>   projectRequestTemplate:
>     name: project-request
> ```
> ```bash
> oc get pods -n openshift-apiserver   # esperar a que se reinicien
> oc new-project 9988
> oc get limitranges -n 9988
> ```

---

## Crear un liveness probe

Crea liveness health probe en el proyecto "tuesday" que tienen un pod corriendo en el puerto 8443 con un initial delay de 3 sec, un timeout de 10 segundos y que la prueba sobreviva al menos 3 crashes.

> [!tip]- 💡 Solución
> Desde la interfaz web en la parte de deployments del proyecto selecciona "Add Health Checks".
> Crea el liveness probe.

---

## Must gather

Recolecta toda la información del cluster y crea una archivo tar con el nombre de system10<cluster.id>.tar.gz y envialo al soporte de redhat. Ejecuta /usr/bin/script system10<cluster.id>.tar.gz

> [!tip]- 💡 Solución
> ```bash
> oc adm must-gather
> tar -cvaf system10-asd-asd-asd-asd.tar.gz must-gather.local.23423423455234
> /usr/bin/script system10-asd-asd-asd-asd.tar.gz
> ```

---

## Despliega una aplicación 1

Despliega una aplicación llamada "oranges" en el proyecto "apples".

La aplicación debe de usar la sa "ex-280-sa" y el secreto magic.

La aplicación debe de dar un output válido.

> [!tip]- 💡 Solución
> Si la aplicación no funciona agrega una configuración de tolerancia en el deployment, justo arriba de `dnsPolicy: ClusterFirst`:
> ```yaml
> tolerations:
> - effect: NoSchedule
>   key: Node
>   value: Worker
>   operator: Equal
> ```
> ```bash
> oc set env deploy/oranges --from=secret/magic
> oc set sa deploy/oranges ex280-sa
> oc edit deploy oranges   # Chequea que: Selector: orange
> oc edit svc oranges      # Chequea que: Selector: oranges y cambialo a orange
> ```

> [!note]
> Puede pedir que arregles la aplicación y sea porque no existe una sa que está definida en el deployment, ahí solamente hay que crearla y listo.

---

## Despliega una aplicación 2

Despliega una aplicación llamada "voyager" en el proyecto "path-finder".

No añada ninguna nueva configuración.

La aplicación debe de generar un output válido

> [!tip]- 💡 Solución
> Si la aplicación no funciona agrega una configuración de tolerancia en el deployment, justo arriba de `dnsPolicy: ClusterFirst`:
> ```yaml
> tolerations:
> - effect: NoSchedule
>   key: Node
>   value: Worker
>   operator: Equal
> ```
> ```bash
> oc edit deploy voyager   # Chequea el selector
> oc edit svc voyager      # Chequea que el selector coincida con los labels del pod
> ```

---

## Despliega una aplicación 3

Despliega una aplicación llamada "mercury" en el proyecto "atlas".

No añada ninguna nueva configuración.

La aplicación debe de generar un output válido

> [!tip]- 💡 Solución
> Si la aplicación no funciona agrega una configuración de tolerancia en el deployment, justo arriba de `dnsPolicy: ClusterFirst`:
> ```yaml
> tolerations:
> - effect: NoSchedule
>   key: Node
>   value: Worker
>   operator: Equal
> ```
> ```bash
> oc edit deploy/httpd -n atlas
> ```

> [!note]
> Posiblemente el request de memoria sea demasiado alto como 80Gi y hay que cambiarlo a 1Gi ya que el nodo no contiene 80Gi de memoria.

---

## Despliega una aplicación 4

Despliega una aplicación llamada "Rocky" en el proyecto "bluewills". La aplicación debe de dar un output válido en "http://rocky22.apps.ocp4.example.com".

> [!tip]- 💡 Solución
> Lo más probable es que en el nodo exista una taint configurada por lo que antes que nada hay que aplicar el toleration en el deployment.
> ```bash
> oc describe nodes | grep -i taints
> ```
> Si la aplicación no funciona agrega una configuración de tolerancia en el deployment, justo arriba de `dnsPolicy: ClusterFirst`:
> ```yaml
> tolerations:
> - effect: NoSchedule
>   key: Node
>   value: Worker
>   operator: Equal
> ```
> Luego de aplicado el toleration posiblemente el pod esté corriendo pero la ruta sea inaccesible. Hay que eliminar la ruta y recrearla con el hostname.
> ```bash
> oc delete route httpd
> oc expose svc httpd --hostname=rocky22.apps.ocp4.example.com
> ```
> Verifica la ruta en internet.
