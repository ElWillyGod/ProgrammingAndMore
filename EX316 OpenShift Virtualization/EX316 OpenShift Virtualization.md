# RED HAT CERTIFIED SPECIALIST IN OPENSHIFT VIRTUALIZATION EX316

Duración: 4hs

Puntos para pasar: 210/300

> [!tip] 💡 Tips
> Leer todo el ejercicio antes de empezar.
>
> - Se solicita en ejercicios la creación de los recursos con determinados usuarios.
>
> Varias VMs se tienen que crear desde URLs, esto demora unos minutos, ver si en el siguiente ejercicio o mas adelante tenemos que crear alguna otra VM para ahorrar tiempo.
>
> Tener claro cuales son los roles que se necesitan para administrar y monitorear VMs.
>
> Para varios ejercicios te dan YAML de ejemplo, pero puede haber alguno muy básico que no, como livenessProbe.
>
> Tener claro cómo instalar servicios y configurarlos para que inicien automáticamente.
>
> - El repo de yum se configura en varias VMs
>
> La instalación de todos los operadores no esta solicitado al comienzo. Pero se recomienda instalar los mismos al comienzo para posteriormente no perder tiempo dado que algunos pueden causar que sea necesario recargar la interfaz grafica.

## Instalar operadores

Instalar y configurar los siguientes operadores:
- Virtualization
- OADP
- MTV

> [!tip]- 💡 Solución
> Acceder a consola Web de OCP.
>
> Ir al apartado OperatorHub, buscar el operador a instalar.
>
> Seguir asistente de instalación.
>
> Para el caso de Virtualization y MTV generar posteriormente a la instalación el recurso necesario, esta instalación tambien es via interfaz grafica.

> [!check]- 🧪 Validación
> Se podrá chequear en los NS donde se instalaron los operadores estos en el apartado **Operators → Installed Operators**.
> Ademas se podrá validar el estado de los mismos e identificar si se esta presentando algún inconveniente.

---

## Configurar permisos sobre recursos de Virtualization

Asignar permisos a usuarios para crear y administrar VMs en distintos namespaces

> [!tip]- 💡 Solución
> Se debe generar RoleBinding para los usuarios y grupos asignando a nivel de NS en los indicados con los siguientes roles:
> - `kubevirt.io:admin`
> - `kubevirt.io:edit`
> - `kubevirt.io:view`
>
> Estos roles unicamente dan permisos sobre los recursos de Virtualization, en caso de necesitar algo mas general se debera optar por los roles estandar de Openshift:
> - `admin`
> - `edit`
> - `view`

> [!check]- 🧪 Validación
> Posterior a la asignación de permisos se podría validar desde el usuario poder realizar las acciones necesarias.

---

## Configuración de Network Policies

Crear configuración de politicas de red para la habilitación del acceso HTTP a una VM

> [!tip]- 💡 Solución
> Generar Network Policy para permitir el acceso al puerto indicado referenciando a Label asignada a VM.
> En caso de no contar con la label se deberá de agregar en el objeto VM y posteriormente reiniciar para impactar el cambio.
> Esta label se debe asignar en: `.spec.template.metadata.labels`

> [!check]- 🧪 Validación
> Posterior a la generación de la regla, seria posible chequear que el trafico mencionado llegue correctamente a destino.
>
> Y se puede chequear en el Service que vea en la pestaña pods los virt-launcher de las VMs

---

## Configuración de redes externas

Asignar una interfaz de red externa a una VM

> [!tip]- 💡 Solución
> Asignar en los nodos con red externa una label.
>
> Crear Politica, esto se realiza desde **Networking → NodeNetworkConfigurationPolicy**.
> Se debe completar los datos necesarios y especificar la label generada en el paso previo.
> Posterior a su configuración se debe esperar que se encuentre disponible el recurso.
>
> Luego se debe crear la definicion de network attachment, para esto nos dirigimos a **Networking → NetworkAttachmentDefinitions**.
> Seleccionamos el proyecto y creamos el recurso completando con los datos indicados.
>
> Luego de generado el Attachment se deberá ir a la VM y adjuntarle una segunda placa de red asociando a la network que generamos en los pasos previos.

> [!check]- 🧪 Validación
> Se deberá poder chequear que la VM tiene dos interfaces de red conectadas en distintos rangos y podremos desde la VM validar que ambas interfaces estan tomando correctamente IP.

---

## Agregar storage a VM

- Agregar storage desde una URL
- Agregar storage desde un PVC

Montar los volumenes de forma persistente (fstab)

> [!tip]- 💡 Solución
> Buscar la VM a agregar disco.
>
> Ir al apartado **Configuration → Disks** y seleccionar agregar.
>
> Luego se deberá con el asistente indicar el Source, este puede ser blank para la generación de un PVC nuevo, utilizar uno ya existente o indicar una URL.
>
> Posterior al montado del disco se debera acceder a la VM para configurar el fstab para que el montado quede permanente.

> [!check]- 🧪 Validación
> Posterior a la creación del disco y montado se deberá reiniciar la VM para confirmar la persistencia del mismo.

---

## Configuración de VMs

Configurar repositorios YUM e instalación de httpd.

Validar el funcionamiento del servicio exponiendo esto via SVC y Routes

> [!tip]- 💡 Solución
> Ir al objeto VM (**Virtualization → Virtual Machines**) y agregar la Label correspondiente para utilizar de selector en el SVC, esta se debe añadir en: `.spec.template.metadata.labels`
>
> Luego de agregada la label reiniciar la VM para que tome el cambio realizado la VMI y el pod virt-launcher
>
> Generar el SVC (**Networking → Services**) con el selector generado en el paso previo. Validar que los Pods se vean en la pestaña pods.
>
> Generar ruta (**Networking → Routes**), esta debe referenciar al SVC y al puerto indicado. Si solicita agregar TLS realizar la configuración necesaria.

> [!check]- 🧪 Validación
> Luego de generada la ruta se deberia poder validar el funcionamiento accediendo a esta via web y el sitio responda correctamente.

---

## Manejo de discos en VMs

Mover disco de Datos de VM1 a VM2
Crear nuevo disco de datos para VM1 a partir de URL

> [!tip]- 💡 Solución
> Apagado de VM1 y VM2.
>
> Acceder a VM1 apartado de Discos.
> Unattach disco indicado de VM.
>
> Acceder a VM2 apartado de Discos.
> Attach disco desasignado de VM1 desde PVC.
>
> Crear disco a partir de URL.
> Attach disco generado en VM1.
>
> Encender VMs y acceder en las mismas via consola.
> Validar en las mismas que se vea correctamente los discos.
> Agregar en `/etc/fstab` el montado permanente de los discos.

> [!check]- 🧪 Validación
> Reiniciar VMs y acceder via consola.
> Se deberían ver los discos montados en el sistema operativo sin realizar ninguna acción.

---

## Crear templates

Crear un template de VM

- Un template viene que repos yum y un init script

> [!tip]- 💡 Solución
> Clonar Template de VM.
> Para esto hay que ir a **Virtualization → Templates**.
> En el template a clonar seleccionar la opción clonar.
>
> Esto generara un Template customizable por el usuario.
> Se deberá ajustar las configuraciones a las solicitadas (cloud-init, instance type, storageclass).
>
> En caso de solicitar que la VM tenga determinada configuración se debe generar VM y realizar las configuraciones necesarias.
> Luego de generada la VM se procedera al apagado de la misma.
>
> Tras el apagado de la VM se deberá aplicar con `virtctl guestfs` la herramienta `virt-sysprep` el cual deja el disco pronto para ser usado en el template nuevamente.
>
> Posterior al aplicado del virt-sysprep se deberá editar el template cambiando el disco de boot en lugar de utilizar from URL cambiar a clonado de PVC, indicando el PVC donde se aplico el sysprep.

> [!check]- 🧪 Validación
> Cuando se genere una VM se deberá poder chequear que las configuraciones custom realizadas persisten.

---

## Configurar asignación de nodos

Configurar NodeAffinity y NodeSelector

> [!tip]- 💡 Solución
> Accediendo la VM tenemos un apartado donde nos permitira realizar la configuración de Scheduling (**Virtualization → Virtual Machine → VM Name → Configuration → Scheduling**).
> En esta sección se podrá configurar de manera sencilla la configuración de Taints y Affinity.

> [!check]- 🧪 Validación
> Durante la configuración al agregar las labels indicadas aparecera en la parte inferior los Nodos que estarán disponibles o preferidos para la VMI.
>
> Luego de reiniciada la VM se debería poder chequear que esta se levanto en uno de los Nodos permitidos.

---

## OADP

- Crear un bucket claim.
- Configurar el DataProtectionApplication.
- Crear un backup de un namespace que previamente se uso en otros ejercicios.

> [!tip]- 💡 Solución
> Crear en ODF BucketClaim.
>
> Obtener datos del bucket para el aprovisionamiento de OADP.
>
> En maquina Workbench configurar para acceder al bucket por s3cmd
>
> Configurar Secret `cloud-credentials` con los valores del bucket en NS `openshift-adp`.
>
> Configurar dpa (Se brinda archivo de configuración de ejemplo) ajustando a los valores correspondientes del bucket generado.
>
> Esperar que todos los componentes se encuentren operativos y generar objeto backup, en este se debe incluir unicamente los NS indicados.

> [!check]- 🧪 Validación
> En los pedidos no se requiere restaurar los recursos, aunque esto es la validacion correcta.
>
> Como alternativa se puede chequear en el Bucket se hayan creado objetos que corresponden al backup generado. Esto no asegura que se haya hecho el respaldo correctamente, pero seria suficiente para estar tranquilo.

---

## Creación de VM y Snapshot

Crear VM desde template y catalogo

Crear snapshot de la VM

> [!tip]- 💡 Solución
> Acceder al NS indicado y dirigirse al apartado **Virtualization → Virtual Machines**
>
> Seleccionar Create from [Catalog o Template]
>
> Completar el formulario correspondiente con los datos indicados.
>
> Esperar el aprovisionamiento de la VM y validar que se encuentre accesible con las credenciales de cloud-init.
>
> Luego desde dentro de la VM acceder a la pestaña Snapshots y crear recurso especificando el nombre indicado en el ejercicio.

> [!check]- 🧪 Validación
> Luego que se termino el proceso de snapshot, debes en la pestaña correspondiente de la VM ver el recurso generado.

---

## Clonar VM con DataVolume

- Clonar una VM con un DataVolume directo del PVC

> [!tip]- 💡 Solución
> Acceder a Creacion de VM del template correspondiente e ir a personalizacion de instalacion.
>
> Ir al apartado de discos y desasignar el disco rootdisk.
>
> Seleccionar la opción agregar disco nuevo a partir de PVC o Clonado de PVC segun corresponda.
>
> - Marcar este como disco de Boot
> - Realizar la configuración de StorageClass y access mode indicado
> - Asignar el espacio indicado
>
> Esperar que se aprovisione la VM y validar que sea accesible con las credenciales correspondientes.

> [!check]- 🧪 Validación
> Validar en la VM cual es el nombre del PVC correspondiente.
>
> Validar la existencia del mismo.

---

## Crear VM a partir de OVA

- Importar un OVA de una VM y desplegarla en otro namespace

> [!tip]- 💡 Solución
> Instalar Operador MTV.
>
> Configurar Provider de OVA en el mismo NS donde esta instalado el Operador.
>
> Crear plan de migración y aplicarlo.

> [!check]- 🧪 Validación
> Luego de validar que la migración se realizo completa, se podrá chequear en el NS destino indicado que la VM este creada.
>
> Esta hay que encenderla y validar el login con las credenciales que te indiquen.

---

## Configurar LivenessProbe

- Instalar Servicio (MariaDB, HTTPD) en una VM
- Configurar LivenessProbe para el servicio en la VM

> [!tip]- 💡 Solución
> En YAML de la VM configurar el LivenessProbe. Se te brinda un ejemplo de la estructura a añadir.
>
> Esto se agrega en `.spec.template.spec.livenessProbe`
>
> Estructura mínima por si no te dan el ejemplo:
> ```yaml
> livenessProbe:
>   initialDelaySeconds: 120
>   periodSeconds: 20
>   tcpSocket:
>     port: 80
>   timeoutSeconds: 10
>   failureThreshold: 3
> ```

> [!check]- 🧪 Validación
> Se puede bajar el servicio en la VM para validar que el comportamiento del chequeo sea el esperado.
