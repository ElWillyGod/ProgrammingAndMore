# Kotlin
## Variables

Tenemos 5 tipos de variables en kotlin, casi los mismos que en Java:

1. Int
2. Boolean
3. Char
4. Double
5. String

También tenemos tipos de datos extendidos o científicos para datos de mayor complejidad:

1. Float
2. Long

Podemos declararlas de dos maneras distintas, podemos especificar el tipo de variable al momento de declararla, si se hace esto no es necesario asignarle el valor

 ``` Kotlin
 var Variable: String
 var Variable: Int = 5
```


De esta manera si no se asigna el valor a la variable a la hora de declararla no genera errores de compilación, la otra manera de declarar las variables es sin especificar el tipo de dato, pero asignando el valor en el mismo momento que se declara:

````` Kotlin
var Variable = "Hola Mundo"
`````

Si se intenta asignar en otro momento esto dará error de compilación.

# Estructuras de selección

Contamos con dos estructuras de selección, estas son el clásico `if{...}else{...}` y el `When (var){ 1-> ... else -> ..}` 

## If ... else

La estructura de selección if consta de una condición que de ser verdadera se ejecutara el bloque de código contenido dentro del mismo, de ser falsa la evaluación de la condición if, se ejecutara el bloque de código contenido dentro de la estructura else.
```kotlin
if (condition) {
  // block of code to be executed if the condition is true
} else {
  // block of code to be executed if the condition is false
}
///////////////////////////////////////////////////////////////////////////////
val time = 20

if (time < 18) {
	println("Good day.")
	
} else {
	println("Good evening.")
}
// Salida "Good evening."

```

## When()

El When  es lo que para java seria el clásico switch, se según el valor de una variable es la respuesta que genera, si el valor de la variable a evaluar no esta contemplado entre las opciones se devuelve el valor establecido en el campo else.

```kotlin
val day = 4

val result = when (day) {
	1 -> "Monday"
	2 -> "Tuesday"
	3 -> "Wednesday"
	4 -> "Thursday"
	5 -> "Friday"
	6 -> "Saturday"
	7 -> "Sunday"
	else -> "Invalid day."
}

println(result)

// Salida "Thursday" (day 4)
```

En el ejemplo anterior si el valor de la variable `day` se fuera del rango `1-7` se mostraría el resultado `"Invalid day.`.

# Estructuras Iterativas

Kotlin cuenta con tres estructuras iterativas, `While`,  `Do ... While` y `For`.

## While

Esta estructura repite todo lo contenido en el bloque de código mientras la condición a evaluar sea verdadera.

```kotlin
while (condition) {
  // code block to be executed
}
```

Por ejemplo este código imprimirá todos los números del 0 al 4:

```kotlin
var i = 0

while (i < 5) {

	println(i)
	i++
	
} 
```

## Do ... While

La estructura de repeticion Do... While funciona de la misma manera que el While, con la gran diferencia de que el bloque de código del While puede ser evitado si la condición es falsa al inicio de la validación, el Do While obliga la ejecución al menos una vez del bloque de código.

```kotlin
do {
  // code block to be executed
}
while (condition);
```

Como se puede ver en la estructura de código se ejecuta el bloque de código antes de validar la condición, es implica al menos una ejecución.

```kotlin
var i = 0

do {
	println(i)
	i++
	
}
while (i < 5) 
```

Este ejemplo hace lo mismo que el while, muestra todos los números del `0-4`, pero si la variable `i` fuera inicializada con el valor de 7, el código anterior mostrara por pantalla el numero 7, sumara uno con la linea `i++`,  al ejecutar la validación esta daria un resultado de false al evaluar `8 < 5` y no se iteraría dentro de la estructura.

Existen formas de cortar un bucle o saltearse el código en determinados momentos.

## Break

Esta instrucción permite cortar la iteración de un bucle, sin alterar la condición y evitando todas las líneas siguientes en el bloque de código.

```kotlin
var i = 0

while (i < 10) {
	println(i)
	i++
	
	if (i == 4) {
	    break
	}

}
```

En el ejemplo anterior si no tomamos la condición del while nos indica que debería detenerse en cuanto la variable `i` tenga el valor de 10, siendo 9 el ultimo numero impreso por pantalla, pero como consecuencia del if cuando `i` contenga el valor de 4 este ejecutara la instrucción `break` esto ara que el bucle se corte, por lo tanto imprimirá los números del 0 al 4 y no del 0 al 9 como se espera debido a la condición del while.

## Continue

La función de esta instrucción es evitar una iteración del bucle, no cortarlo como pasaba con el `break`.

```kotlin
var i = 0

while (i < 10) {

	if (i == 4) {
		i++
		continue
	}
	
	println(i)
	i++
}
```

Note dos cosas importantes, la primera es la colocación del `continue` y la otra es la posición de la instrucción `println(i)`, estad dos cosas combinadas hacen que cuando la variable `i` sea igual a 4, se ejecute el incrementador `i++` y posteriormente el `continue`, este `continue` permitirá saltear el resto del bloque de código sin terminar las iteraciones del bloque, lo que nos da una salida de todos los números del 0 al 9 excluyendo únicamente al numero 4.

# For

La estructura de iteración For funciona como en el lenguaje Python, la estructura se basa en recorrer un rango o conjunto de elementos tomando temporalmente el valor de los elementos de forma consecutiva en una variable, de esta forma puede sonar confuso, ahora veremos de forma mas ejemplificada el funcionamiento:

```Kotlin
var cars = arrayOf("Volvo", "BMW", "Ford", "Mazda")
// cars es un array, mas adelante explicare que es

for (i in cars){
	println(i)
}
```

Este código imprimirá todos los valores de cars, pero como funciona?, la lógica detrás de ese código es que la variable `i` toma el primer valor del array `Volvo` posteriormente lo imprime con el `println` y se repite el For, pero en esta ocasión el valor de `i` es el siguiente valor del que contiene el array (`BMW`). Esto continua hasta que se terminen los valores de el array, cuando esto sucede se termina el bucle For, recordemos que como todo bucle se puede saltear una iteración con `Continue` o terminarlo con un `Break`.


# Arrays

Las estructuras de datos `array` son una colección de datos que constan con un índice, un índice es el valor posicional dentro del `array`, esos valores se requieren para poder acceder a determinado dato, debemos tener en cuenta que los índices siempre comienzan en cero, por ejemplo:

```Kotlin
val cars = arrayOf("Volvo", "BMW", "Ford", "Mazda")

println(cars[0])

// Salida Volvo 
```

Notar dos cosas, la declaración del array es con la palabra reservada `arrayOf` seguida de un paréntesis curvo y los valores que contiene separados por comas, luego accedemos a estos poniendo el nombre del array paréntesis rectos y el índice al que queremos acceder dentro del array, en la sección For se recorrió un `array` sin usar los valores posicionales (índices), debido a que la estructura lo hace de forma interna y la variable toma los valores dela array de forma consecutiva, ahora aremos el mismo ejemplo pero con un `While` para poder apreciar mejor el funcionamiento de los índices.

```Kotlin
var cars = arrayOf("Volvo", "BMW", "Ford", "Mazda")
// cars es un array, mas adelante explicare que es

var i = 0

while (i <= 3) {

	println(cars[i])
	
}
```

Ese código imprime todos los valores del array, no notar que la condición del `while` es `i <= 3`, pero porque es tres y no cuatro?, tendría lógica que sea `i` menor igual a `4` siendo cuatro el numero de elementos del array?, lamentablemente no, recordemos que los índices de un array comienzan en cero, por lo tanto los índices que contiene el array anterior van desde el cero hasta el 3, haciendo una totalidad de cuatro elemento, de todas formas se puede reescribir la condición del bucle `while` para poder colocar el numero cuatro en ella, podrías darte cuenta como reescribir la condición para que recorra la misma cantidad de valores? `i < 4`.


# Ranges

Los rangos son lo que indica el nombre justamente, un rango de valores desde un punto a otro.

```Kotlin
for (chars in 'a'..'x') {

  println(chars)
  
}
```

En el ejemplo anterior se muestra como recorrer un rango de `a` hasta `x`, el rango se crea utilizando los `..` entre el inicio y fin de un rango, el ejemplo anterior mostrara todos todas las letras desde la `a` hasta la `x`.


# Funciones

Las funciones son bloques de código que se pueden llamar desde cualquier parte, mientras la función esa accesible 