# Proyecto

nececito ideas de proyectos, mi primera opcion seria una shel, que seria algo bastant copado de hacer pero no se.
como podria ejecutar los comandos, usando concurrencia
eso estaria copado, pensando podria hacer la shell si

tambien quiero hacer un script para que me automatice los push al repo
la idea es que se respalde en base a dos criterios, uno seria cada cierto tiempo y cada cantidad
n de caracteres escritos

- [ ] Prompt personalizado con información del usuario o la hora (PS1).

- [ ] Soporte para los comandos internos como cd, pwd, exit.

- [ ] Historial de comandos con timestamps (guardado en archivo).

- [ ] Autocompletado simple o sugerencias con case y egrep.

- [ ] Soporte para tuberías (|) y redirección (>, >>, <).

- [ ] Background y foreground con & y fg, manejando SIGCHLD y SIGINT con trap.

- [ ] Crear alias personalizados temporales dentro de la shell (como una shell interactiva con su propio .bashrc).

- [ ] Agregar un sistema de "plugins" que cargue scripts Bash desde una carpeta específica al iniciar la shell.

- [ ] Comprobación de seguridad: evitar que se ejecuten comandos peligrosos si el usuario no es root.

- [ ] Comando interno tipo search que use expresiones regulares para buscar comandos anteriores o archivos en el sistema.

Módulo 1

Prompt personalizado
Loop de lectura (read)
Soporte para comandos internos: cd, pwd, exit
Búsqueda en $PATH y ejecución
Historial básico en archivo

Módulo 2

Redirección de salida: >, >>
Redirección de entrada: <
Soporte para pipes |
Comandos en background & y fg
Manejadores de señales (trap) para SIGINT, SIGCHLD

Módulo 3

Alias temporales (alias nombre='comando')
Carga de alias desde .minirc o similar
Sugerencias/autocompletado (con case + egrep)
Búsqueda avanzada de historial con regex (grep, egrep)
Soporte para variables de entorno temporales (export VAR=valor)

Módulo 4

Plugin system: al iniciar, se cargan scripts desde ~/.minishell/plugins.d/\*.sh
Comando interno search que use egrep para buscar en archivos, comandos, logs
Comprobación de seguridad:
Evita ejecución de rm -rf /, shutdown, etc.
Solo permite comandos root si el usuario es UID 0

Estructura Basica

shell.sh
bultin.sh
config.sh
tools.sh