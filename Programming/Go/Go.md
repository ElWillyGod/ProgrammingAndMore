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

Es bastante claro el ejemplo

## Multi-threading

ESTO SE VA A DESCONTROLARRRRR
Ahora si podemos ver miltihilo no como la cagada que tiene [python](/Programming/Python/README.md) todo esta sección se divide en dos partes, las [Gorutinas](#Gorutinas) y los [Canales](#Canales) con estos elementos go maneja la concurrencia.

### Gorutinas

Las gorutinas son funciones concurrentes





### Canales