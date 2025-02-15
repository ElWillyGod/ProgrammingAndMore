## Control de Versiones 

Es un programa que controla los cambios en una selección de archivos. Uno de los objetivos de VCS es recuperar fácilmente versiones anteriores de un proyecto también dando la posibilidad de crear ramas y versiones experimentales con la opción de agregar etiquetas y comentarios a cada una de estas versiones o modificaciones, el autor principal de Git VCS es Linux Torvalds, creador de linux

## Control de versiones distribuido 

Las instancias anteriores de los VCS, como CVS, Subversion (SVN) y Perforce, usaban un servidor centralizado para almacenar el historial de un proyecto. Esta centralización significaba que un solo servidor también era un único punto de error en potencia.

Git es un sistema _distribuido_, lo que significa que el historial completo de un proyecto se almacena en el cliente _y_ en el servidor. Se pueden editar archivos sin conexión de red, protegerlos localmente y sincronizarlos con el servidor cuando una conexión esté disponible. Si un servidor deja de funcionar, todavía tendrá una copia local del proyecto. Técnicamente, ni siquiera es necesario tener un servidor. Los cambios pueden pasarse por correo electrónico o compartirse mediante medios extraíbles, pero, en la práctica, nadie usa Git así.

## Terminología

- **Arbol de trabajo** archivos y directores que contienen el proyecto en el que se trabaja 
- **Repositorio (repo)** directorio situado un nivel superior a un arbol de trabajo, un repositorio vacio es aquel que no pertenece a ningun arbol; se usa para compartir o  realizar copias de seguridad. Un repositorio suele terminar su nombre en .git
- **Hash** numero generado por una funcion que representa el contenido de un archivo. Git usa hasta 160 bits  de longitud.Una ventaja de usar códigos hash es que Git puede indicar si un archivo ha cambiado aplicando un algoritmo hash a su contenido y comparando el resultado con el hash anterior. Si se cambia la marca de fecha y hora del archivo, pero el hash del archivo no, Git sabe que el contenido del archivo no se ha modificado.
- **Objeto** un repositorio de Git contiene cuatro tipos de _objetos_, cada uno identificado de forma única por un hash SHA-1. Un objeto _blob_ contiene un archivo normal. Un objeto _árbol_ representa un directorio, y contiene nombres, valores hash y permisos. Un objeto de _confirmación_ representa una versión específica del árbol de trabajo. Una _etiqueta_ es un nombre asociado a una confirmación.
- **Confirmación** basicamente se confirman los cambios y se aplican a la rama general
- **Rama** - serie con nombre de confirmaciones vinculadas. La confirmación más reciente en una rama se denomina _nivel superior_. La rama predeterminada, que se crea al inicializar un repositorio, se denomina `main`, y suele tener el nombre `master` en Git. El nivel superior de la rama actual se denomina `HEAD`. Las ramas son una característica increíblemente útil de Git porque permiten a los desarrolladores trabajar de forma independiente (o conjunta) en ramas y luego combinar los cambios en la rama predeterminada.
- **Remoto**: referencia con nombre a otro repositorio de Git. Cuando se crea un repositorio, Git crea un remoto denominado `origin`, que es el remoto predeterminado para las operaciones de envío e incorporación de cambios.
- **Comandos**, **subcomandos** y **opciones**: las operaciones de Git se realizan mediante comandos como `git push` y `git pull`. `git` es el comando, mientras que `push` o `pull` es el subcomando. El subcomando especifica la operación que quiere que Git realice. Los comandos suelen ir acompañados de opciones, que usan guiones (-) o guiones dobles (--). Por ejemplo`git reset --hard`

## Comandos de Configuración

```Bash
git config --global user.name "<USER_NAME>"

git config --list

git init -b main

git commit -m "Mensaje"

git clone https://{YOUR_PERSONAL_TOKEN}@github.com/{YOUR_USERNAME}/{YOUR_REPO}.git 
```

## Comandos básicos de Git

Git recuerda los cambios efectuados en los archivos como si tomara instantáneas del sistema de archivos.

Vamos a hablar de algunos comandos básicos para iniciar el seguimiento de los archivos del repositorio. Luego va a guardar la primera "instantánea" con la que Git va a comparar.

### git status

El primer comando de Git, y el que se usa con más frecuencia, es `git status`. Ya lo ha usado una vez en el ejercicio anterior para ver que había inicializado correctamente el repositorio de Git.

`git status` muestra el estado del árbol de trabajo (y del área de almacenamiento provisional, de la que pronto hablaremos más). Permite ver los cambios que Git está siguiendo en ese momento para poder decidir si quiere pedir a Git que tome otra instantánea.

### git add

`git add` es el comando que se usa para indicar a Git que empiece a realizar un seguimiento de los cambios en determinados archivos.

El término técnico es _almacenamiento provisional_ de estos cambios. Va a usar `git add` para almacenar provisionalmente los cambios a fin de prepararse para una confirmación. Todos los cambios en los archivos que se han agregado pero que aún no se han confirmado se almacenan en el _área de almacenamiento provisional_.

### git commit

Después de haber almacenado provisionalmente algunos cambios para su confirmación, puede guardar el trabajo en una instantánea si invoca al comando `git commit`.

_Confirmar_ (o "commit") es un verbo y un sustantivo. Básicamente tiene el mismo significado que cuando se confirma en un plan o se confirma un cambio en una base de datos. Como verbo, la confirmación de cambios significa que se coloca una copia (del archivo, el directorio u otra "cosa") en el repositorio como una nueva versión. Como sustantivo, una confirmación es el pequeño fragmento de datos que asigna una identidad única a los cambios que se confirman. Los datos que se guardan en una confirmación incluyen el nombre del autor y la dirección de correo electrónico, la fecha, los comentarios sobre lo que se ha hecho (y por qué), una firma digital opcional y el identificador único de la confirmación anterior.

### git log

El comando `git log` permite ver información sobre las confirmaciones anteriores. Cada confirmación tiene un mensaje adjunto (un mensaje de confirmación), y el comando `git log` permite imprimir información sobre las confirmaciones más recientes, como su marca de tiempo, el autor y un mensaje de confirmación. Este comando ayuda a realizar un seguimiento de lo que ha estado haciendo y de los cambios que se han guardado.

### git help

Ya ha probado el comando `git help`, pero vale la pena recordar ciertas cosas. Use este comando para obtener información fácilmente sobre todos los comandos que conoce hasta ahora y más.

Recuerde que cada comando incluye también su _propia_ página de ayuda. Para encontrar estas páginas de ayuda, escriba `git <command> --help`. Por ejemplo, `git commit --help` muestra una página que proporciona más información sobre el comando `git commit` y cómo usarlo.

### Recursos

Si quiere profundizar más, aquí tiene más recursos:

- Ejecute los comandos `git help tutorial` y `git help tutorial-2`.
- Visite el sitio [Everyday Git](https://git-scm.com/docs/everyday) o use el comando `git help everyday`.
- Revise los [recursos de aprendizaje de Git y GitHub](https://help.github.com/en/articles/git-and-github-learning-resources).
- Vea el vídeo de [introducción al resumen de Git](https://www.youtube.com/watch?v=9uGS1ak_FGg%3Fazure-portal%3Dtrue).
- Vea la [sección de documentación](https://git-scm.com/doc) del [sitio web oficial de Git](https://git-scm.com/).
