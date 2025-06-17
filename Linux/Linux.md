# Linux y Shell

- [ ] Kernel y Distribuciones: poder describir qué es el kernel, sus funciones principales, qué es el user-space y qué es una distribución de Linux
- [ ] Saber nombrar las principales diferencias entre Debian/Ubuntu y entre RedHat/Fedora/CentOS/RockyLinux/AlmaLinux.
- [ ] Entender la shell, el ambiente del usuario, variables, funciones y aliases.
- [ ] Conocer los comandos básicos: ls, cd, pwd, date, who, echo, top, ps, man, apropos, entre otros.
- [ ] Familiarizarse con la estructura de directorios estándar (FHS).
- [ ] Manejo de archivos, conocer los comandos básicos: cp, mv, rm, mkdir, chmod, chown, find, entre otros.
- [ ] Ejecución y parada de programas, entender los distintos tipos de señales, programas en background y volver a foreground.
- [ ] Tuberías y redireccionamiento, entender los flujos estándar y como conectar procesos usando tuberías.
- [ ] Expresiones regulares, uso de egrep.


## Kernel

El kernel en encuentra dentro del sistema operativo y controla todas las funciones principales del hardware, ya sea un teléfono, una computadora o cualquier equipo
Se encarga principalmente de la gestión de memoria, supervisando cuanta memoria se utiliza para almacenar que tipo de archivos, se encarga de gestionar los procesos ya sea el tiempo como cuando estos procesos pueden usar el CPU, actúa como medidor o interprete entre el hardware y los procesos y recibe solicitudes de servicios por parte de los procesos.
Si el kernel es implementado de forma correcta es invisible par el usuario y funciona en su propio mundo pequeño conocido como espacio de kernel  donde asigna memoria y supervisa el lugar en el que se almacena cada elemento, lo que esta a la vista del usuario, como los exploradores y los archivos, se conoce como el espacio del usuario que es básicamente todo el código que esta fuera del kernel desde lo antes mencionado hasta diversos programas y bibliotecas, todas estas aplicaciones se comunican con el kernel a través de una interfaz de llamadas al sistema(SCI)
Una llamada al sistema es una rutina que permite a una aplicacion de usuario solicitar acciones que requieren privilegios especiales. La adicion de llamadas al sistema es una de la varias maneras de ampliar las funciones proporcionadas por el kernel.
La diferencia entre una llamada al sistema y una llamada de funcion ordinaria solo es importante en el entorno de programacion del kernel.