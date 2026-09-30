# RED HAT CERTIFIED ENGINEER EX294

Duración: 4hs

Puntos para pasar: 210/300

> [!tip] 💡 Tips
> Tener claro el uso de la documentación de RedHat, muchos ejemplos de los modulos a utilizar pueden ser reutilizados con un par de cambios.

# Preguntas examen RHCE

## Preparar ambiente

- Login en repositorio de imagenes
- Creacion de archivo `ansible.cfg` con los parametros indicados
- Instalacion de `ansible-navigator` y `ansible-core`
- Ejecucion de `ansible-navigator` y validacion de generacion de contenedor

## Armado de inventario

- Se debe armar un inventario con anidacion de grupos

## Configurar repositorio YUM en los host gestionados

- Se debe usar el modulo de ansible `ansible.builtin.yum_repository`

## Creacion de rol "Apache"

- Debe instalar el paquete de httpd
- Iniciar y habilitar el servicio httpd
- Usar template para generar el archivo de conf
- Usar copy para generar el archivo html
- Iniciar y habilitar el servicio de firewalld
- Habilitar en FW el acceso via http

## Importacion y uso de Collections

- Se debe instalar las collections en path custom:
    ```bash
    ansible-galaxy collection install *collection* -p ./mypath
    ```
- Se debe usar un rol de la coleccion, esto funcionara siempre y cuando se tenga en el archivo `.cfg` el path custom de collection.

## Importacion y uso de Rol

- Se debe instalar un rol desde URL
    - Generar el archivo `requirement.yml` donde se especifique la URL y el nombre a tener.
    - Posteriormente se debe ejecutar:
        ```bash
        ansible-galaxy install -r requirement.yml
        ```

## Cambio de key en VAULT

- Se debe ejecutar el comando e ingresar las contraseñas solicitadas:
    ```bash
    ansible-vault rekey file.yml
    ```

## Cambiar el contenido de un archivo

- Con el modulo copy se remplaza el contenido de los arhivos

## Crear VAULT nuevo

- Se puede generar el yml y luego encriptarlo con `ansible-vault encrypt file.yml`
- Se puede generar de entrada encriptado con `ansible-vault create file.yml`

## Reporte de HW

- Usando `ansible_facts` y un template se debe armar un reporte de recursos del servidor

## Generar archivo hosts dinamico

- Usando `hostvars` se arma dinamico el archivo host con un for en un template

## Generar LVM segun paramentros indicados

- Pide generar en un VG existente un LV de 1500MiB si no tiene espacio de 800MiB y si no existe el VG indicar esto y no hacer nada mas

## Generar Usuarios y asignarles contraseña a partir del vault generado

- Tiene q tomar la password del vault de un archivo y usar `password_hash('sha512')`

## Generar Cron en un usuario

- `ansible.builtin.cron`
