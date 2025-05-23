
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
# Assigning a function to a variable
def greet(n):
    return f"Hello, {n}!"

say_hi = greet  # Assign the greet function to say_hi
print(say_hi("Alice"))  # Output: Hello, Alice!

# Passing a function as an argument
def apply(f, v):
    return f(v)

res = apply(say_hi, "Bob")
print(res)  # Output: Hello, Bob!

# Returning a function from another function
def make_mult(f):
    def mult(x):
        return x * f
    return mult

dbl = make_mult(2)
print(dbl(5))  # Output: 10
```

Esta es la salida:
```bash
>Hello, Alice!
>Hello, Bob!
>10
```

