En esta parte veremos como resolver algunas ecuaciones diferenciales muy particulares y pocas, de primer y segundo orden, estas pueden tener infinitas soluciones o no, lo mas seguro luego de encontrar una solución es verificarla en la ecuación inicial luego de encontrara.
Existen dos tipos, Homogéneas y no Homogéneas, y puede variar su orden, primero aprenderemos a solucionar las que son Homogéneas y posteriormente las que no son homogéneas 

## Homogéneas 

#### Definición
Estas ecuaciones diferenciales son de la forma $y'=A(y)B(x)$ donde $A(y)$ es una función dada, que depende solo de $y$ continua para todo $y$ en un intervalo abierto de $\Re$, y $B(x)$ es una función dada que depende solo de $x$, continua para todo $x$ en un intervalo abierto de $\Re$ 
### Variables Separables

Este es el primer método que veremos para la resolución de ecuaciones, es bastante mecánico y intuitivo, consiste en separar las variables de tal manera que nos quede algo de la forma $\frac{y'}{y}=x$ y de esta forma efectuar un cambio de variable.

En general para buscar la solución de la ecuación cuando $A(y)\neq0$, entonces, dividiendo la ecuación diferencial entre $A(y)$ se obtiene:$$\frac{y'(x)}{A(y(x))}=B(x)$$ Primitivando respecto a $x$ (para mantener la igualdad tiene que ser primitivadas respecto a la **la misma variable**).
$$\int\frac{y'(x)}{A(y(x))}=\int{D(x)}dt$$
con esto en mente debemos ejecutar un cambio de variable de forma que nos quede$$\int\frac{dy}{A(y)}=C +\int{D(x)}dt$$
**Ejemplo:**

$$y'=(cos(x))y$$
$\int\frac{y'(x)}{y(x)}dx=C +\int{cos(x)dx}$  -> $\int\frac{dy}{y}=C+sen(x)$  -> $log(|y|)=C+sen(x)$, para despejar el logaritmo de  $y$  elevamos ambas partes en $e$, entonces nos queda como $y=e^{C+sen(x)}$ que se puede escribir como $y=Ke^{sen(x)}$, siendo $K$ una constante positiva que se genera de la propiedad de descomposición de factores $e^Ce^{sen(x)}$, como $C$ es una constante el, el resultado de $e^C$ es otra constante que le llamamos $K$.
Si analizamos la solución resultante podemos ver que forman un sub-espacio vectorial de dimensión 1.

Este método funciona para lo que serian funciones de esta forma  $y'=A(y)B(x)$, a este tipo de ecuaciones se les llama homogéneas de primer orden, por ahora seguiremos trabajando con las de primer orden pero que pasa con las que No son homogéneas ?

## No Homogéneas

#### Definición 
**Las Ecuaciones diferenciales no homogéneas son de la forma  $y'+A(x)y=B(x)$

La solución de este tipo de ED es la suma de la solución general que corresponde a la homogénea respecto a la no homogénea que queremos resolver, esto es la solución de $y'+A(x)y=0$(que aprendimos a resolver con el método de variables separables ), mas una solución particular que aun no sabemos encontrar aun.

### Variación de Constantes 

Para hallar una solución particular $y_{p(x)}$  en la ecuación diferencial (NH) no homogénea dada, probemos con una función de cierto tipo, como se escribe:

1. En la solución general $y_{H(x)}$ de la ecuación diferencial homogénea asociada $H$ (que tendrá que haber sido resuelta antes de aplicar este método), aparece una constante arbitraria $C$ real
2. Tomaremos $y_{p(x)}$  igual a  $y_{H(x)}$ pero donde dice $C$ pondremos una función desconocida $C(x)$, (con esto vamos a estar haciendo "variar" lo que antes era una constante). Habrá que determinar la función desconocida $C(x)$, escribiendo una función genérica $C(x)$ en vez de $C$ llamado ahora $y_{H(x)}$ a lo que se obtiene.
3. Sustituyendo $y_{P(x)}$ en la ecuación diferencial (NH) no homogénea dada, haciendo que verifique, se despeja y se encuentra $C(x)$.De todas las posibles funciones $C(x)$ que se despejen (en general son infinitas), habrá que elegir UNA SOLA.
4. La función $C(x)$ hallada en el paso anterior se sustituye dentro de la expresión de $y_{P(x)}$ para obtener la solución particular buscada de la ecuación diferencial no homogénea. 


##### Ejemplo 

Resolver $y'=(cos(x))y+cos(x)$ 

La función homogénea asociada es $y'-(cos(x))y=0$, la solución de la homogénea es $y_H=Ce^{sen(x)}$, donde $C$ es una constante arbitraria real.
La forma de la solución general de la NH es de la forma $$y(x)=Ce^{sen(x)}+y_{p(x)}$$
utilizando la información que ya tenemos la forma general de la solución particular que es la misma que la homogénea lo único que variando la constante o sea que seria: $y_{P(x)}=C(x)e^{sen(x)}$ entonces sustituimos esta $y_{P(x)}$ en la ecuación dada NH:
$$(C(x)e^{sen(x)})'=(cos(x))(C(x)e^{sen(x)}+cos(x))$$$$(C'(x)+C(x)cos(x))e^{sen(x)}=C(x)(cos(x))e^{sen(x)}+cos(x)$$$$C'(x)=e^{-sen(x)}cos(x) -> C(x)\int {e^{-sen(x)}cos(x)}dx$$esa integral no queda $C(x)=-e^{-sen(x)}+K$ elegimos solamente un $C(x)$ por ejemplo el caso en el que  $K=0$  y con esto en mente sustituimos $C(x)$ en $y_{P(x)}=C(x)e^{sen(x)}$ que nos deja $y_{P(x)}=-e^{-sen(x)}e^{sen(x)}=-1$, en este caso la