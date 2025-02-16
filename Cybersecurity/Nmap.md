# Nmap

La herramienta nmap se utiliza para el mapeo de los puertos correspondientes a una red o servidor, brindando información sobre los puertos abiertos, servicios que se encuentran corriendo en los mismos y dispositivos conectados.
Esta herramienta tiene muchas funcionalidades ya sea para un escaneo sigiloso o agresivo. 

## Escaneo sigiloso

El escaneo sigiloso se realiza enviando paquetes SYN y analizando la respuesta, si esta respuesta es de tipo SYN/ACK significa que el puerto esta abierto y se puede establecer un conexión TCP.

```Bash
nmap -sS example.com
```

Este es un escaneo sigiloso que te mostrara información sobre los puertos abiertos y sus servicios, ten en cuenta que los escaneos sigiloso es mas lento y no tan agresivo por lo cual tal vez tengas que esperar un tiempo para obtener una respuesta.

## Escaneo de versiones

Este escaneo te permite ver las versiones de los servicios que se encuentren activos en los puertos.

```Bash
nmap -sV example.com
```

Esto puede ser muy útil ya que puedes buscar una vulnerabilidad existentes en la base de datos de CVE para una versión en especifico, debes tener en cuenta que estos escaneos no son totalmente precisos pero te pueden dar una idea bastante acertada. Este comando también te brindara información sobre los posibles sistemas operativos que tenga el host, aunque si solo te interesa esta información puedes realizar el siguiente comando.

```bash
nmap -o example.com
```

## Escaneo Agresivo

Nmap cuenta con un modo agresivo que nos brinda mas información que los escaneos regulares, sin embargo un análisis agresivo también envía mas sondeos y es mas probable que se detecta durante una auditoria.

```bash
namp -A example.com
```


## Escaneo de hosts activos

Se puede utilizar nmap para el escaneo escaneo de la red, para esto se puede hacer un escaneo de los host activos con el siguiente comando

```bash
nmap -sn 10.0.0.0/24
```

Como se aprecia en el ejemplo anterior, para esto se necesita la ip de a red y su mascara.