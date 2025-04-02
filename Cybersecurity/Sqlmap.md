# Sqlmap

Sqlmap es una herramienta de código abierto que automatiza el proceso de detección y explotación de fallas de inyección de SQL y toma de servidores de base de datos. Tiene soporte para mas de veinte gestores de base de datos y seis tipos de inyección SQL distintos.

El uso es relativamente simple, para poder realizar un escaneo simple podemos usar :

```
sqlmap -u https://www.url.com/todoLoDemas
```

Esto nos dirá si la URL es vulnerable a inyecciones SQL, una vez confirmado eso podemos ver el nombre de las bases de datos, la estructura de las mismas, o la información que tienen dentro