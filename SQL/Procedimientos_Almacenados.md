# Procedimientos Almacenados

Un procedimiento almacenado es un conjunto de instrucciones de tipo SQL que son ejecútales del mismo modo que una función en el ámbito de la programación, para poder realizar un procedimiento almacenado primero se debe saber que sentencias SQL se realizaran en el mismo, la sintaxis básica para estos procedimientos es la siguiente:

```SQL
create proc NOMBREPROCEDIMIENTO

@NOMBREPARAMETRO TIPO_DATO

as

	SENTENCIAS SQL;

GO
```

Como se puede apreciar los parámetros comienzan con `@` y son solo de uso local para los procedimientos. Un ejemplo básico de como se podría desarrollar un procedimiento almacenado es el siguiente:

```SQL
CREATE PROCEDURE saludo
	AS
	PRINT 'Hola Mundo';
	GO
-- Indicamos GO para cerrar el lote que crea el procedimiento
```

Para ejecutar este ejemplo tendríamos que poner el siguiente comando:

```SQL
EXECUTE saludo;
```

Hora aremos un ejemplo mas complejo sobre un procedimiento almacenado que consulte un registro y devuelva una tabla:

```SQL
CREATE PROCEDURE nombre
@var varchar(30)
AS
	BEGIN
		SELECT nombre, apellido FROM personas WHERE nombre= @var;
	END
GO
```