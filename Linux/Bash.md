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

	# lo asi saco la ruta para desmontarlo
	# df | tail -n 1 | cut -d ' ' -f1
	# saco la ruta accesible
	# df | tail -n 1 | cut -d '%' -f2 | tr -d ' '
	
	umount $(df | tail -n 1 | cut -d '%' -f2 | tr -d ' ')

}

main
```

En este ejercicio se trabaja el filtrado de información y el pasaje por tubería de un proceso a otro, utilice `read -s -n` el `-s` es para que no se vea la entrada que ingresa el usuario y el `-n 1` indica la cantidad de  