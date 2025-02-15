Gobuster es una herramienta de fuerza bruta que sirve para la enumeración de directorios ocultos, subdominios, búsqueda de archivos en servidores web con extensiones especificas y enumeración de claves en servicios como Amazon S3. Entre sus puntos destacables esta su rapidez ya que se basa en Go, un lenguaje de programación desarrollado por Google orientado al desarrollo de aplicaciones, sistemas y servicios web, esto lo hace eficiente en el manejo de concurrencia y velocidad en ataques de fuerza bruta.

## Modos de operación

Gobuster cuenta con siete modos.

### Enumeración de directorios

El modo `dir` sirve para buscar directorios y archivos ocultos en un servidor web.

Ejemplo de uso básico.
```bash
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt
```

Este comando mostraría los directorios y archivos que coincidan con los nombre que contiene `common.txt`
En el ejemplo anterior se utilizaron dos opciones, `-u` para señalar la URL objetivo y `-w` para el diccionario de palabras a buscar, podemos especificar el tipo de archivo que estamos buscando con la opción `-x`, también se puede especificar la cantidad de hilos que se utilizan en la búsqueda con el parámetro `-t`, los hilos permiten realizar múltiples solicitudes simultáneamente, lo que acelera significativamente el proceso de enumeración aunque representa una mayor carga en la red y en el servidor objetivo.

Ejemplo.
```bash
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt -x php,html -t 100
```


### Enumeración de Subdominios

Esta opción se encarga de la búsqueda de subdominios de un dominio especifico. Funciona de forma similar a la opción `dir`

Ejemplo.
```bash
gobuster dns -d example.com -w subdomains.txt
```

Desglosando el ejemplo anterior no encontramos con el parámetro `-d` para especificar el dominio objetivo y el parámetro `-w` para el diccionario de palabras a buscar como subdominio. Este modo también soporta la opción `-t` para ajustar el numero de hilos a utilizar.


### Enumeración de host virtuales

Este modo sirve para identificar subdominios en un servidor web configurados s nivel de host virtuales, esto es especialmente útil para la búsqueda de aplicaciones internas en servidores compartidos.

Ejemplo.
```bash
gobuster vhost -u http://example.com -w vhosts.txt
```

Como se puede apreciar en el ejemplo funciona de la misma manera que la 