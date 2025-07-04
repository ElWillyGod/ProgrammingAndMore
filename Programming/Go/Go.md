# Go

Todo un tema Go, tiene unas cosas raras con los arrays que estan copadas, ademas de los arrays comunes, tiene algo llamado...

## Slices

Esto es una "mejor" forma para manejar arrays, que basicamente te da una especie de interfaz para poder utilizarlos es simple pero interesante, la sintaxis es `slice[low:high]` es lo que indica básicamente, acá te dejo los ejemplos.

```Go
package main

import (
    "fmt"
    "slices"
)

func main() {

    var s []string
    fmt.Println("uninit:", s, s == nil, len(s) == 0)

    s = make([]string, 3)
    fmt.Println("emp:", s, "len:", len(s), "cap:", cap(s))

    s[0] = "a"
    s[1] = "b"
    s[2] = "c"
    fmt.Println("set:", s)
    fmt.Println("get:", s[2])

    fmt.Println("len:", len(s))

    s = append(s, "d")
    s = append(s, "e", "f")
    fmt.Println("apd:", s)

    c := make([]string, len(s))
    copy(c, s)
    fmt.Println("cpy:", c)

    l := s[2:5]
    fmt.Println("sl1:", l)

    l = s[:5]
    fmt.Println("sl2:", l)

    l = s[2:]
    fmt.Println("sl3:", l)

    t := []string{"g", "h", "i"}
    fmt.Println("dcl:", t)

    t2 := []string{"g", "h", "i"}
    if slices.Equal(t, t2) {
        fmt.Println("t == t2")
    }

    twoD := make([][]int, 3)
    for i := range 3 {
        innerLen := i + 1
        twoD[i] = make([]int, innerLen)
        for j := range innerLen {
            twoD[i][j] = i + j
        }
    }
    fmt.Println("2d: ", twoD)
}
```

Y esto es la salida

```bash
$ go run slices.go
uninit: [] true true
emp: [  ] len: 3 cap: 3
set: [a b c]
get: c
len: 3
apd: [a b c d e f]
cpy: [a b c d e f]
sl1: [c d e]
sl2: [a b c d e]
sl3: [c d e f]
dcl: [g h i]
t == t2
2d:  [[0] [1 2] [2 3 4]]
```

Es bastante claro el ejemplo.

## Multi-threading

ESTO SE VA A DESCONTROLARRRRR
Ahora si podemos ver miltihilo no como la cagada que tiene [python](/Programming/Python/README.md) todo esta sección se divide en dos partes, las [Gorutinas](#Gorutinas) y las soluciones al problema de selección critica como los [Canales](#Canales)  y otros, con estos elementos go maneja la concurrencia y sus problemas.

### Gorutinas

Las gorutinas son funciones concurrentes que son gestionadas por el entorno de ejecución de go, no por el sistema operativo, estas gorutinas con ejecutadas mediante un modelo de M:N donde M es el numero de gorutinas y N es es el numero de hilos del sistema.
Para crear gorutinas se usa la palabra reservada `go` antes del nombre de la función.

```Go
package main

import (
    "fmt"
    "time"
)

func f(from string) {
    for i := range 3 {
        fmt.Println(from, ":", i)
    }
}

func main() {

    f("direct")

    go f("goroutine")

    go func(msg string) {
        fmt.Println(msg)
    }("going")

    time.Sleep(time.Second)
    fmt.Println("done")
}
```

Salida:

```bash
$ go run goroutines.go
direct : 0
direct : 1
direct : 2
goroutine : 0
going
goroutine : 1
goroutine : 2
done
```

### Canales

Estas cosas existen para solucionar el problema de **selección critica**, todos los programas tienen una parte llamada **sección critica** en la que acceden a un recurso compartido, el problema esta en que si dos procesos modifican este recurso compartido al mismo tiempo, puede llevar a errores críticos, para gestionar este problema este problema existen varias soluciones, entre ellas los Canales (en Go).
Los canales garantizan una comunicaron y sincronizacion segura entre gorutinas esto se logra mediante la espera en un canal hasta que un dato este disponible o un receptor este listo.
La sintaxis es simple, se crea el nuevo canal con `make(chan val-type`  y se envian los valores por el canal usando `<-`.

```Go
package main

import "fmt"

func main() {

    messages := make(chan string)

    go func() { messages <- "ping" }()

    msg := <-messages
    fmt.Println(msg)
}
```

Salida:

```bash
$ go run channels.go 
ping
```

Se le pueden pasar mas de un valor al canal, así:

```Go
package main

import "fmt"

func main() {

    messages := make(chan string, 2)

    messages <- "buffered"
    messages <- "channel"

    fmt.Println(<-messages)
    fmt.Println(<-messages)
}
```

Es relativamente simple, solo se tiene que especificar el valor en el momento que ese cera el canal `make(chan string, 2)` y así el canal puede "amortiguar" n cantidad de valores.


#### Canales para Sincronizar

Los canales se pueden usar para sincronizar la ejecución de gorutinas, pero cuidado que si se están realizando múltiples ejecuciones conviene usar [Wait Group](#Wait-Group),

### Wait Group
