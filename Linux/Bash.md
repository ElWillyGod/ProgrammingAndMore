# Bash

Distintos tipos de ejercicios:
```bash
#!/bin/bash

respaldo() {
	cat $0 > backup.sh
}
respaldo
```
en ese ejercicio se realizan copias de si mismo cuando el archivo es ejecutado,
notar que el uso de `$0` este parámetro contiene el nombre del archivo actual.

```bash
#!/bin/bash

recursiveDirectiryListing() {
	# tengo que listar de forma recursiva los los directorios de usuario
	# ir guardando esa info en un file y luego comprimirlo
	
	ls -R >> listFile.txt

}

main() {
	echo 'Conecte un USB'
	echo 'Presione ENTER luego de contectar el USB'

	read -s -n 1 entrada

	while [[ "$entrada" != '' ]]; do
		echo 'vos sos normal?'
		echo 'Presione ENTER luego de contectar el USB'

		read -s -n 1 entrada
	done

	recursiveDirectiryListing

	gzip listFile.txt

	mv listFile.txt.gz $(df | tail -n 1 | cut -d '%' -f2 | tr -d ' ')
	
	umount $(df | tail -n 1 | cut -d ' ' -f1)

}

main
```

En este ejercicio se trabaja el filtrado de información y el pasaje por tubería de un proceso a otro, utilice `read -s -n` el `-s` es para que no se vea la entrada que ingresa el usuario y el `-n 1` indica la cantidad de caracteres que se quiero leer en este caso uno porque debe ser un enter.
Se usa el redireccionador de salida doble (`>>`) que indica que si el archivo no esta creado que lo cree y si esta creado que lo agregue debajo (esto no es correcto, debería ser un redireccionador simple `>` para que no lo agregue ), use `gzip` para comprimir el archivo y luego lo muevo con `df | tail -n 1 | cut -d '%' -f2 | tr -d ' '` como se pueden ver son 4 comandos distintos que se concatenan, el primero `df`:
	Este comando muestra los dispositivos de almacenamientos junto a mucha mas información como el tipo de partición el almacenamiento usado y el punto de anclaje y la ruta de acceso.

El segundo comando `tail`:
	Este se usa para saber cuantas lineas quieres mostrar de un archivo, en esta ocasión le pase la salida del `df` y le indique que me muestre la primera linea (`-n 1`) de abajo hacia arriba.
	
El tercer comando `cut`:
	este sirve para extraer partes de un fichero o en este caso de la salida de otro comando, en este caso lo use para indicar que extraiga la segunda sección `-f2` que esta delimitado por el % `-d %`
El ultimo comando `tr`:
	Este lo use porque tenia un espacio al inicio de la linea que me devolvió el  `cut`, entonces le dije que me borre el carácter de espacio (`-d ' '`)

Luego de mover el archivo desmonto la unidad, para eso es `umount` y uso una expresión parecida a la anterior, lo único  es que la idea es extraer el punto de anclaje para poder usar el comando, creo que también lo podes usar con la ruta de acceso (la primera expresión nos da eso mismo) pero para entender un poco mas los comandos decidí sacar el punto de anclaje.


## Arreglos Asociativos

Esto parece copado, diría que es como un diccionario en bash, la idea es key=value, igual que un diccionario de toda la vida, también son dinámicos, puedo agregar key y values mientras esta en ejecución.

```bash
declare -A example_array=(["key1"]="value1" ["key2"]="value2" ["key3"]="value3")
```

se necesita el `declare -A` que es para indicar que es un arrays, se pueden agregar mas que los definidos al inicio colocando una key nueva y el nuevo valor.

```bash
example_array["new_key"]="new_value"
```

Y esta, esto esta copado, podemos hacer los alias asi, no se si las variables de entorno también se podrán


#### Ejemplos de redireccionamiento de salidas

Use el redireccionamiento para simplificar muchas tareas de administración de rutina. Revise la tabla anterior y analice los siguientes ejemplos.

Guarde una marca de tiempo en el archivo `/tmp/saved-timestamp` para su posterior consulta.

 **`date > /tmp/saved-timestamp`**

Copie las últimas 100 líneas del archivo `/var/log/secure` a otro archivo `/tmp/last-100-log-secure`.

 **`tail -n 100 /var/log/secure > /tmp/last-100-log-secure`**

Concatenar los cuatro archivos `step` en un archivo en el directorio `tmp`.

 **`cat step1.sh step2.log step3 step4 > /tmp/all-four-steps-in-one`**

Enumere los nombres de archivo regulares y ocultos del directorio de inicio y guarde la salida en el archivo `my-file-names`.

 **`ls -a > my-file-names`**

Agregue una línea al archivo existente `/tmp/many-lines-of-information`.

 **`echo "new line of information" >> /tmp/many-lines-of-information`**

Los siguientes comandos generan mensajes de error porque algunos directorios del sistema son inaccesibles para usuarios normales. Observe cómo se redirigen los mensajes de error.

Redirija los errores desde el comando `find` al archivo `/tmp/errors` cuando visualice la salida de un comando normal en el terminal.

 **`find /etc -name passwd 2> /tmp/errors`**

Guarde la salida de un proceso en el archivo `/tmp/output` y los mensajes de error en el archivo `/tmp/errors`.

 **`find /etc -name passwd > /tmp/output 2> /tmp/errors`**

Guarde la salida de un proceso en el archivo `/tmp/output` y descarte los mensajes de error.

 **`find /etc -name passwd > /tmp/output 2> /dev/null`**

Almacene la salida y los errores de forma conjunta en el archivo `/tmp/all-message-output`.

 **`find /etc -name passwd &> /tmp/all-message-output`**

Adjunte la salida y los errores al archivo `/tmp/all-message-output`.

 **`find /etc -name passwd >> /tmp/all-message-output 2>&1`**