
# Python Avanzado

Voy a estudiar tres cosas "avanzadas" de Python, los decoradores custom, las meta clases y el procesamiento multi hilo.

## Decorators in Python

Los famosos decoradores, se usan todo el tiempo, pero...

### Que son??

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
```
def decorator_name(func):  
	def wrapper(*args, **kwargs):  
		# Add functionality before the original function call  
		result = func(*args, **kwargs)  
		# Add functionality after the original function call  
		return result  
	return wrapper

@decorator_name  
def function_to_decorate():  
	# Original function code  
	pass
```