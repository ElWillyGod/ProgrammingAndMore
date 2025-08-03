# Objetivo general del Sprint **🥇**

- Entender conceptos básicos de protocolos de red y comunicación
- Programar aplicaciones que se comuniquen mediante la red
- Entender como se comunican dos procesos mediante una conexión TCP/IP
## Objetivos mínimos del Proyecto

- Lograr que dos o más procesos se comuniquen entre sí utilizando alguno de los mecanismos vistos en el Sprint, sin utilizar capas de abstracción provistas por los lenguajes.
    - Estos procesos pueden ser parte de una aplicación cliente/servidor, o puede ser un proceso que interactúa con otro mediante una API.
- Ser capaz de mostrar los mensajes intercambiados por los procesos a nivel de red.

## Duración 3 semanas

# Tareas **📝**

- ~~¿Qué es una IP y qué es un puerto?~~
- ~~TCP y UDP: ¿qué son? ¿en qué casos se aplica cada uno? ¿ como se usan ?
- ~~Entender el protocolo ICMP y qué rol cumple.~~
- HTTP/S: ¿qué es? ¿para qué sirve? ¿cómo se usa? ¿qué versiones hay y en qué se diferencian?
- ¿Qué es REST y gRPC?
- Publish - Subscribe (Pub-Sub) / Message Brokers (MQTT / AMQ): ¿qué son? ¿para qué sirven? ¿cómo se usan?
- Protocolo SSH, port Forwarding con SSH, uso de clave privada/publica con SSH para acceder sin contraseñas.
- SSL/TLS, qué versiones se consideran inseguras, certificados y entidades certificadoras.
- Estudiar tráfico que viaja sobre una red TLS en Wireshark, qué se ve y qué no se ve.
- Herramientas: ping, ss, nc, ip (con addr, link, route), telnet, traceroute, tracepath, mtr, curl (opciones -v, -H, -X, -k), iptraf-ng.
- Conceptos de Firewall y Proxy, diferencias, cuando se usa uno y el otro.
- ~~DNS y registries: ¿cómo funciona el DNS? ¿qué es una registry? Herramientas: dig (ver opción -X también), nslookup, whois~~
- Troubleshooting: ¿cómo distinguir entre un problema de conexión y un problema de DNS?

# Ejercicios **🏋️**

- Capturar paquetes con tcpdump y analizarlos con Wireshark.
- Hacer un programa que envíe datos mediante TCP a un socket y hacer otro que escuche en un socket.
- Usar Wireshark, filtrando, para ver cómo viajan los datos entre las dos aplicaciones del punto anterior, sin ver el tráfico de otras aplicaciones que también están corriendo.
- Hacer prototipo en Go como cliente o servidor HTTP.

# Materiales sugeridos **📚**

[https://www.youtube.com/watch?v=b9HafRqtVWc](https://www.youtube.com/watch?v=b9HafRqtVWc) “Introduction to TCP/IP”

[https://www.cloudflare.com/en-gb/learning/ddos/glossary/open-systems-interconnection-model-osi/](https://www.cloudflare.com/en-gb/learning/ddos/glossary/open-systems-interconnection-model-osi/)

[https://github.com/gsahinpi/acm361/blob/master/Computer%20Networks%20-%20A%20Tanenbaum%20-%205th%20edition.pdf](https://github.com/gsahinpi/acm361/blob/master/Computer%20Networks%20-%20A%20Tanenbaum%20-%205th%20edition.pdf)
