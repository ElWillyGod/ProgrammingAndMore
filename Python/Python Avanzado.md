
# Python Avanzado

Voy a estudiar tres cosas "avanzadas" de Python, los decoradores custom, las meta clases y el procesamiento multi hilo.

## Decorators in Python

Los famosos decoradores, se usan todo el tiempo, pero... Que son??

Básicamente son funciones que reciben como parámetros otras funciones, generalmente  se usa para agregar funcionalidades a esta función que llega como parámetro.
Esto seria un ejemplo básico de como se utiliza.

```python
# decorador

def decorador(funcion):
	def cositas():
		print("antes de la funcion")
		funcion()
		print("despues de la funcion")
	return cositas

@decorador
def funcion_principal():
	print("funcion principal")

funcion_principal()
```

Así se podrían usar de forma básica, cuando se llama a la función principal se ejecuta el decorador que no es mas que otra función que mediante la sub función "cositas" se le agregan funcionalidades a la función principal, difícil de explicar pero fácil de entender con el ejemplo, la salida de ese código seria esta:

```bash
>antes de la funcion
>funcion principal
>despues de la funcion
```

Esta seria la sintaxis de un decorador:
```python
def decorator_name(func):  
	def wrapper(*args, **kwargs):  
		# Las cosas que queres que se hagan antes de la funcion 
		result = func(*args, **kwargs)  
		# y las cosas despues de la funcion
		return result  
	return wrapper

@decorator_name  
def function_to_decorate():  
	# el codigo original
	pass
```

Una función que recibe como parámetro otra función, rompamos la cuarta pared, a vos te suena eso??, tal vez conozcas el termino funciones de orden superior o mayor (Higher-Order Functions), eso es algo muy importante en lo que es programación funcional y clave para entender como funcionan los decoradores.

### Funciones de Orden Mayor

Una función de orden mayor son esas funciones que pueden tomar una o mas funciones como argumento y/o devolver una función como resultado que se puede llamar mas tarde, de forma básica se podría ver así:

```python
# Esta es la funcion de orden mayor
def fun(f, x):
	return f(x)

# Esto es una funcion normal
def square(x):
	return x * x

res = fun(square, 4)
print(res)
```

La salida de ese código es `16`, básicamente estoy llamando a fun y le paso dos parámetros, el primero es la segunda función y el segundo en este caso es el parámetro de la segunda función, cuando se ejecuta fun, retorna la función square con el segundo parámetro, otro ejemplo podría se este:

```python
def fun1(fun):
	return fun("Hola")

def uppercase(txt):
	return txt.upper()


print(fun1(uppercase))
```

Este es otro ejemplo, la salida seria `HOLA`.

Puedes ver las similitudes??, en esencia los decoradores son son funciones de orden mayor que toman como parámetro una función, la modifican y devuelven una nueva función que extiende o cambia su funcionamiento, recordemos también que las funciones son objetos de primera clase (en python todo son objetos igual) esto significa que se pueden tratar como cualquier otro objeto, se le pueden asignar a variables y usarse como cualquier otro valor, las podes pasar como parámetros a otras funciones y devolverlas en funciones que es clave para el concepto de decoradores también se pueden almacenar en estructuras de datos.

Te dejo un ejemplo medio loco:

```python

def greet(n):
    return f"Hello, {n}!"

say_hi = greet
print(say_hi("Willy"))

def apply(f, v):
    return f(v)

res = apply(say_hi, "Manu")
print(res)


def make_mult(f):
    def mult(x):
        return x * f
    return mult

dbl = make_mult(2)
print(dbl(5))
```

Esta es la salida:
```bash
>Hello, Willy!
>Hello, Manu!
>10
```

Me parece re loco, sobre todo el ejemplo de la multiplicación, intenta razonar que valor tiene `f` y `x` en la llamada a la función make_mult.

### Usos

Los decoradores se pueden usar de distintas maneras, la clásica es en las funciones, que seria algo así:

```python
# decorador

def decorador(funcion):
	def cositas():
		print("antes de la funcion")
		funcion()
		print("despues de la funcion")
	return cositas

@decorador
def funcion_principal():
	print("funcion principal")

funcion_principal()
```

Pero también se pueden usar en métodos de clases:

```python
def method_decorator(func):
    def wrapper(self, *args, **kwargs):
        print("Before method execution")
        res = func(self, *args, **kwargs)
        print("After method execution")
        return res
    return wrapper

class MyClass:
    @method_decorator
    def say_hello(self):
        print("Hello!")

obj = MyClass()
obj.say_hello()
```

Se ejecuta cuando se llama a un método de clase, o incluso para definir métodos de clases.

```python
def fun(cls):
    cls.class_name = cls.__name__
    return cls

@fun
class Person:
    pass

print(Person.class_name)
```


### Combinación de decoradores

Los decoradores se pueden combinar, tiene todo el sentido del mundo:

```python

def decor1(func): 
    def inner(): 
        x = func() 
        return x * x 
    return inner 

def decor(func): 
    def inner(): 
        x = func() 
        return 2 * x 
    return inner 

@decor1
@decor
def num(): 
    return 10

@decor
@decor1
def num2():
    return 10
  
print(num()) 
print(num2())
```

```bash
>400
>200
```

Notar el orden de ejecución de los decoradores.

# GIL, multiprocesamiento y multihilo

El Bloqueo Global del Intérprete de Python (GIL) es un bloqueo que permite que sólo un hilo mantenga el control sobre el intérprete de Python, esto quiere decir que un solo hilo puede estar en ejecución en cualquier momento.Los hilos múltiples están sujetos a la GIL, lo que a menudo hace que utilizar Python para realizar multihilos sea una mala idea: la verdadera ejecución multinúcleo mediante multihilos no está soportada por Python en el intérprete CPython. Sin embargo, los procesos no están sujetos al GIL porque éste se utiliza dentro de cada proceso Python, pero no entre procesos. Esto da la posibilidad de poder crear procesos paralelos para ejecutar condigo async, pero no es multihilo como tal, existen técnicas que permiten liberar temporalmente el GIL como usando PyO3, pero esto es bien raro, en realidad PyO3 es una biblioteca de Rust para crear exenciones de Python en Rust, esto permite que el código de Rust se utilice directamente desde Python y permite interactuar con el interprete de Python desde el código de Rust (esto esta re loco), pero no es lo que intentamos hacer, porque entonces debemos escribir los códigos paralelos en Rust (Rust no tiene GIL) para luego ejecutarlos desde Python, la idea es hacer todo desde Python lo cual creo que no es posible (no con lo que se por ahora) así que este tema queda temporalmente suspendido, podríamos eliminar el GIL pero para eso tenemos que modificar el interprete CPython (ojo que podría estar bueno).