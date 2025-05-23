
# Python Avanzado

Voy a estudiar tres cosas "avanzadas" de Python, los decoradores custom, las meta clases y el procesamiento multi hilo.

## Decorators in Python

Los famosos decoradores, se usan todo el tiempo, pero...

### Que son??

Básicamente son funciones que reciben como parámetros otras funciones, tienen algunas cosas especiales como el "wrapper" que es la forma que se tiene para agregar funcionalidades a esta función que llega como parámetro.
Esto seria un ejemplo básico de como se utiliza.

```python
# decorador

def esteEsElDecorador(func):

	def wrapper():
		print("antes de la funcion")
		func()
		print("despues de la funcion")
	return wrapper

@esteEsElDecorador
def hello():
	print ("Hello, World!")

hello()
```

Asi se podrían usar de forma básica, el wrapper lo que hace es encapsular la nueva funcionalidad a la funcion que viene por parámetro