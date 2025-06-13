Los números tienen origen histórico en XVI Rafael Bombelli los comienza a manipular y descartes los llama imaginarios por su origen.

#### Definición

  Los números complejos son un par ordenado de números reales (a,b).
  Un par ordenado no es lo mismo que (1,2), Dado un numero complejo z=(a,b), la primera componente "a" se denomina parte real, y se denota a=Re(z). La segunda componente se llama parte imaginaria, se escribe b=Im(z).

 
#### Operaciones

 Sean (a,b) y (c,d) dos números complejos.

 - Decimos que $(a,b)=(c,d)$ si $a=c , b=d$.
 - Definimos la suma como $(a,b) + (c,d) = (a+c,b+d)$.
 - Definimos el producto como $(a,b)(c,d) = (ac - bd, ad + bc)$.

 #### Definición
==Dado un complejo (a,b), su opuesto esta dado por (-a,-b) y en caso de ser $(a,b) \neq (0,0)$, su inverso esta dado por== $\frac{a}{a^2+b^2} ,\frac{-b}{a^2+b^2}$

## Complejos y su Geometría

Entonces un cuerpo $C$, que es una extensión de los reales, pero que se porta mejor con las ecuaciones polinomiales. hay alguna propiedad de los reales que hayamos perdido en los complejos?, Si, ==Lo que no podemos hacer en $c$ es ordenarlos, de manera que se "porten bien" con las operaciones==. Los reales son entonces un cuerpo ordenado pero los complejos no. Pero los complejos son un cuerpo algebraicamente cerrado, y los reales no.

podemos escribir un complejo cualquiera $z=(a,b)$ descomponiendo su parte real y su parte imaginaria:
$$z=(a,b)=(a,0)+(0,b) = a(1,0)+b(0,1)= a+bi$$
A esta forma de escribir números complejos se le llama notación binomica. 

La definición como par ordenado ya da una idea geométrica clara. Interpretamos un numero complejo como un punto en el plano, con coordenadas $(a,b)$, es decir, en el eje horizontal representamos la parte real, y en el eje vertical la parte imaginaria.

![[Pasted image 20230802110852.png]]

Con esta interpretación, la suma de complejos no es mas que la suma de vectores en el plano (o regla del paralelogramo).
Existen otras  maneras de escribir los puntos del plano, por ejemplo mediante su distancia al origen, y el angulo que forman con el eje horizontal. 

#### Definición

==Sea $z=a+bi$ un complejo. Lo llamamos modulo a la distancia de $z$ al origen: $\sqrt{a^2+b^2}$, y se denota $|z|$. Al angulo $\phi$ que forma el vector$(a,b)$ con el eje horizontal lo denominamos argumento.==

![[Pasted image 20230802112156.png]]

Notar que hay varios argumentos posibles, dado que el ángulo esta determinado a menos de múltiplos de $2\pi$.
Para calcular el argumento de un complejo a partir de su expresión en notación binomica, notar que la tangente del ángulo que forma $z$ con el eje real es $\frac{b}{a}$ es decir, $\phi = arctan(\frac{b}{a})$. Pero hay que tener cuidado con eso.
El complejo $z_{1} = 1+i$ tiene modulo $|z_{1}| = \sqrt{1^2 + 1^2} =\sqrt{2}$ y argumento $\phi = arctan(\frac{1}{1}) = arctan(1)=\frac{\pi}{4}$ esto ya lo podemos intuir a partir de la representación grafica.

![[Pasted image 20230802185309.png]]

Probemos ahora con el complejo $z_2 = -1-i$.
Entonces su modulo es $\sqrt{2}$ cuando intentamos calcular su argumento, si no tenemos cuidado resulta $\frac{\pi}{4}$. Es decir, tendría el mismo modulo y argumento que $z_1$, por lo que algo debe estar mal, pues dos complejos con el mismo modulo y mismo argumento, necesariamente son iguales . Este resultado no tiene sentido con la representación grafica que teníamos.

El problema es que la tangente tiene varias ramas, y estamos usando la que esta definida en el intervalo $(-\frac{\pi}{2},\frac{\pi}{2})$, porque en algunos casos hay que corregir, sumando (o restando) $\pi$. en el caso de $z_2$ por ejemplo, deberíamos sumar $\pi$ al resultado. En general se debe hacer es corroborar si el resultado tiene sentido con la representación grafica, mirando en que cuadrante se encuentra el complejo.

**El modulo de un complejo es la distancia al origen, y por lo tanto no puede ser un valor negativo, por ejemplo. Listamos algunas de estas propiedades **

**Sean $z$ y $w$ dos complejos. Entonces:**

1. $|z|\geq0$, y $|z|=0$ sii $z=0$
2. $|Re(z)|\leq|z| ; |Im(z)|\leq|z|$
3. $|zw| = |z||w|; |\frac{z}{w}| = \frac{|z|}{|w|}$
4. $|z+w| \leq |z| + |w|$

Si tenemos un numero complejo dado con su modulo $|z|$ y el ángulo $\phi$, podemos calcular su parte real e imaginaria, simplemente proyectando el vector a cada uno de los ejes:
$a=Re(z)=|z|cos\phi$, $b=Im(z)=|z|sin\phi$

Algunos ejemplos:

1. $z_1=1$ tiene modulo 1 y argumento 0 (o$2\pi$, o cualquier múltiplo de $2\pi$) .
2. El complejo $z_2 = 1$ tiene modulo 1 argumento $\pi$ (o $-\pi$, etc.). En general, los números reales (con parte imaginaria nula), tienen argumento 0 si son positivos, o $\pi$ si son negativos.
3. El complejo $z_3=i$, la unidad imaginaria, tiene modulo $|i|=\sqrt{0^2+1^2}=1$  y argumento $\frac{\pi}{2}$
4. El complejo $z_4=-i$ tiene modulo $1$, y argumento $\frac{-\pi}{2}$(o $\frac{3\pi}{2}$)
5.  El complejo $z_5=1-i$ tiene modulo $|1-i|=\sqrt{1^2+1^2}=\sqrt{2}$ y argumento $\frac{-\pi}{4}$

#### Definición

==Dado un complejo $z=a+bi$, definimos su conjugado como $\bar{z} = a-bi$==

![[Pasted image 20230802215957.png]]

**Algunas propiedades**

1. $\bar{z+w} = \bar{z}+\bar{w}$
2. $\bar{zw} = \bar{z}.\bar{w}$
3. $z+\bar{z} = 2Re(z)$
4. $z-\bar{z} = 2i.Im(z)$
5. $\bar{\bar{z}} = z$
6. $z.\bar{z} = |z|^2$

La ultima propiedad nos permite realizar cálculos de cocientes de complejos de forma sencilla. Si quieres expresar $\frac{z}{w}$ en notación binomica, entonces multiplicamos y dividimos por el conjugado del denominador. De esta manera, en el denominador resulta $w\bar{w}$ que es real, y por lo tanto podemos separar parte real e imaginaria del cociente.
Ejemplo:

$z=\frac{3+2i}{1-i} =\frac{(3+2i)(1+i)}{(1-i)(1+i)}=\frac{3+3i-2+2i}{|1-i|^2}=\frac{1+5i}{2}=\frac{1}{2}+\frac{5}{2}i$

En muchas ocasiones nos encontramos con que intentamos sacar raíces a un polinomio y el coeficiente dentro de la raíz es negativo , lo que obtenemos son dos raíces complejas conjugadas: $\frac{-b+i\sqrt{4ac-b^2}}{2a}$ y  $\frac{-b-i\sqrt{4ac-b^2}}{2a}$. Recíprocamente, si tomamos dos complejos conjugados, digamos $z_0=a+bi$ y $\bar{z_0}=a-bi$ y consideramos el polinomio que los tiene como raíces, entonces resulta:

$P(z) = (z-z_0)(z-\bar{z_0})=z^2-2az+a^2+b^2$

1. Con esto deducimos que $P(\bar{z})= \bar{P(z)}$
2. Concluir que si el polinomio tiene una raíz compleja, entonces su conjugada también es raíz.

Para cualquier complejo $z=a+bi$ de modulo uno (es decir$a^2 + b^2 =1$), su parte real es $a=Cos(\alpha)$ y su parte imaginaria es $b=Sin(\alpha)$, siendo $\alpha$ un argumento de $z$.

Si ahora tomamos un complejo $z \neq 0$ cualquiera, y lo dividimos entre su modulo, el ejemplo resultante $\frac{z}{|z|}$ tiene modulo uno. Por lo tanto, como esta en la circunferencia unidad, es $\frac{z}{|z|} = cos(\alpha)+isin(\alpha)$ donde $\alpha$ es el argumento de $\frac{z}{|z|}$.

#### Definición

$z=|z|cos(\alpha)+isin(\alpha)$ 

==A esta notacion de un complejo utilizando su modulo y argumento, se la denomina notación polar==

## Exponencial Compleja 

La idea es bisecar una función $F:\not\subset -> \not\subset$ que extienda a la función exponencial real que conocemos, y que además cumpla con las propiedades clásicas de la exponencial.

#### Definición

==Dado un complejo $z=a+bi$, definimos a la funcion exponencual compleja como $e^z=e^a(cos(b) + isin(b))$ donde $e^a$ es la exponencial real conocida.==

Lo primero que podemos observar es que si $z=a$ es decir que la parte imaginaria es nula, entonces $e^z=e^a$, por lo tanto en los reales la exponencial recién definida coincide con la exponencial real.

**Proposición**

Si $z=a+bi$ y $w=a+bi$ son dos complejos, entonces se tiene $e^{z+w} = e^ze^w$ 

![[Pasted image 20230803005732.png]]

Observemos:

$e^{i\pi}=e^0(cos(\pi)+isin(\pi))= -1$

Si $z$ es un complejo puramente imaginario, $z=ib$, entonces $e^z=e^{ib}=cos(b) + isin(b)$. Esto es, un complejo de modulo uno (y ángulo $b$).

==En general si $z=a+bi$, entonces el modulo de $e^z$ depende solamente de la parte real de $z$. Por otro lado, el argumento depende solamente de la parte imaginaria.==

Otra forma de vero es observando que $e^{a+ib}=e^ae^{ib}$, dónde $|e^{a+ib}|=|e^a|.|e^{ib}|=e^a$.

Volvamos a la exponencial de un imaginario puro $e^{ib}=cos(b)+isin(b)$ y recordemos la notación polar. Entonces, si $z$ es un complejo de modulo $\wp$ y argumento $\emptyset$, tenemos la notación mas compacta :
$$z=\wp e^{i\emptyset}$$

ya vimos que la suma de complejos es muy natural, sobre todo si miramos su representación geometría en el plano. Ahora, la implementación para el producto de dos complejos no es evidente a partir de su definición. En cambio si lo escribimos en su notación polar $z_1=\wp_1e^{i\emptyset_1}$ y $z_2=\wp_2e^{i\emptyset_2}$, entonces el producto es:
$z_1z_2=\wp_1e^{i\emptyset_1}.\wp_2e^{i\emptyset_2}=\wp_1\wp_2e^{i{(\emptyset_1+\emptyset_2)}}$
Es decir, cuando hacemos el producto de dos complejo, los módulos se multiplican y los ángulos se suman.
Tomar en cuentea que la notación binomica es mas cómoda para trabajar con simas, y la notación polar es mas cómoda para trabajar con productos y potencias.

![[Pasted image 20230804222333.png]]

Un ejemplo, busquemos todos los números complejos que cumplan $z^2=\bar{z}$.
Si escribimos $z$ en su notación polar es $z=\wp e^{i\emptyset}$, entonces tenemos que $$\wp^2 e^{i2\emptyset}=\wp e^{-i\emptyset}$$
Para que estos dos complejos sean iguales, deben serlo sus módulos y sus ángulos (a menos de múltiplos de $2\pi$, recordemos que $2\pi$ es una vuelta completa al circulo trigonométrico). Entonces tenemos:
$$\wp^2=\wp$$ $$2\emptyset=-\emptyset+2k\pi$$
Para que la primera ecuación solo existen dos soluciones 0 y 1, la segunda nos queda $\emptyset=\frac{2k\pi}{3}$, puede parecer que esto tienen infinitas soluciones pero en realidad se van a repetir los ángulos cuando $k=3$ ya que la ecuación quedaría $2\pi$ que seria una vuelta entera y el ángulo de esto seria 0, entonces esto tendría exactamente 3 soluciones, en las que $k$ menor a $3$ 


## Raíces complejas 

Comencemos planteándonos esta pregunta, Sera cierto por ejemplo que $\sqrt{zw}=\sqrt{z}\sqrt{w}$?.
comencemos estudiando las raíces enésimas. Es decir , dado un natural $n$, buscamos números complejos $z$ tales que $z^n=1$. Entonces escribimos a $z$ en su notación polar $z=\wp e^{i\emptyset}$, y tenemos que 
$\wp^n e^{in\emptyset}=1$

De donde$\wp^n=1$ y por lo tanto $\wp=1$ (no existe otro entero positivo real que de 1). Luego, tenemos que $n\emptyset=2k\pi$, con $k$ en los irracionales, de aquí obtenemos $n$ soluciones, que son $\emptyset_k=\frac{2k\pi}{n}$. Observamos que para $k=0$ obtenemos solución $z_0=1.e^{i\pi}=-1$.
Para dos valores consecutivos de $k$, las soluciones correspondientes difieren en un ángulo de $\frac{2\pi}{n}$, y todas tienen el mismo modulo. Es decir, tenemos $n$ raíces enésimas de la unidad, y se encuentran en los vértices de un polígono regular de $n$ lados.
Cuando queremos calcular las raíces enésimas de un complejos cualquiera, procedemos a igualar. Buscamos entonces los complejos tales que $z^n=w_0$. Escribimos a $w_0$ en su notación polar $w_0=\wp_0e^{i\emptyset_0}$, y también la $z$ que buscamos, $z=\wp e^{i\emptyset}$.Entonces tenemos:
$$\wp^ne^{in\emptyset}=\wp_0e^{i\emptyset_0}$$
De donde obtener dos ecuaciones reales, una parte del modulo y otra para el argumento:
$$\wp^n=\wp_0$$$$n\emptyset=\emptyset_0+2k\pi$$
Recordemos que $\wp$ debe ser un real no negativo, y que las soluciones para $\emptyset$ son cíclicas en $k$, obtenemos:
$$\wp=\sqrt[n]{\wp_0}$$$$\emptyset=\frac{\emptyset_0}{n}+\frac{2k\pi}{n},k=0,...,n-1$$
