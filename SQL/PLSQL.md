
PLSQL es el manejo de sentencias SQL en paquetes y funciones, la sintaxis es muy similar a el lenguaje de programación Pascal, combinado con sentencias SQL.

## Procedimientos PLSQL

Los procedimientos PLSQL son funciones que no retornan un valor, sino que realizan una acción solamente, esta puede impactar o no en la base de datos por nunca devolverá datos.
Los hermosos procedimientos PL/SQL constan de las tres partes típicas de una función en programación:

```PLSQL
procedure nombreDelProcedimiento(parametrosSirequiere)
is
begin
	--Cuerpo del procedimiento
end nombreDelProcedimiento;
```

Un caso de uso podría ser el siguiente:

```PLSQL
procedure updateEstado(p_id IN VARCHAR2, p_estado IN VARCHAR2 DEFAULT NULL)
IS
BEGIN
	UPDATE Dato SET ESTADO = p_estado WHERE ID = p_id;
	COMMIT; --la sentencia commit se usa para hacer efectivos los cambios en la BD
END updateEstado;
```


## Funciones PLSQL

Una función es un bloque de código reutilizable que realiza una tarea específica y devuelve un valor. Es similar a un procedimiento, pero con la diferencia clave de que una función siempre devuelve un valor a quien la llama.

```plsql
FUNCTION nombre_funcion ( parametro1 [modo] tipo_dato, parametro2 [modo] tipo_dato )
RETURN tipo_retorno
IS
    -- Declaraciones locales
BEGIN
    -- Cuerpo de la función
    RETURN valor;
    
END nombre_funcion;
```

Como se usan:

Desde SQL:
```sql
SELECT nombre_funcion(parametro1) FROM tabla;
```

Desde PLSQL
```plsql
DECLARE
    resultado NUMBER;
BEGIN
    resultado := nombre_funcion(parametro1);
END;
```

Creo que es bastante intuitivo.