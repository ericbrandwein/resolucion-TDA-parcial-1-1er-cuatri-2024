# Resolución primer parcial TN

## Pregunta 1
**Respuestas correctas**

- $O(j!\cdot j)$

**Explicación**

Hay dos formas de generar todas las posibles soluciones:

1. Elegir un cajón para cada juguete. Esto nos da $c^j$ posibles asignaciones.
2. Ordenar los juguetes de alguna manera, y poner los primeros en el primer cajón hasta que no entren más, los siguientes en el segundo hasta que no entren más, etc. Acá hay $j!$ combinaciones posibles.

En principio uno podría decir listo, estas son las complejidades del algoritmo. Pero **estaría equivocado**: estas cantidades describen cuántas **hojas** va a haber en el árbol de backtracking, no la cantidad total de nodos, ni cuánto tiempo se tarda por nodo. Vamos entonces a analizar los dos casos por separado.

1. Un algoritmo de backtracking que asigne un cajón a cada juguete construiría la solución parcial como una asignación de los primeros $i$ juguetes a cajones. Pasar por todas las elecciones de cajones para un juguete no tarda más que $O(1)$ por cajón, así que cada nodo interno del backtracking toma $O(1)$. ¿Cuántos nodos tiene nuestro árbol? El primer nivel tendrá uno solo, el segundo tendrá $c$, el tercero $c²$... es decir, la cantidad total de nodos es $$
O\left(\sum_{i=0}^j c^i\right) = O\left(\frac{c(c^j - 1)}{c-1}\right) = O(c^j).$$ 
Sólo faltaría ver cuánto trabajo hacemos en las $c^j$ hojas del árbol. En principio, tendríamos que fijarnos para la asignación de $j$ juguetes si entran en todos los cajones. Hacerlo bien naïf involucraría un costo de $O(j)$ en cada hoja, lo que nos lleva la complejidad a $O(c^j\cdot j)$. Por suerte podemos repartir ese trabajo entre todos los nodos. Vamos guardando un arreglo con la suma de las alturas de los juguetes en cada cajón. Cuando agregamos un juguete, directamente le sumamos su altura al cajón correspondiente. Así, agregar un juguete sigue siendo $O(1)$. Además, vamos pasando un número que es la cantidad de cajones que están sobrepasados, el cual cambiamos cuando agregamos un juguete. En las hojas entonces solo necesitamos fijarnos si ese número es 0, lo cual es $O(1)$. Al final conseguimos la complejidad que queríamos de $O(c^j)$...?  

    **NO.** la igualdad de arriba solo vale si $c>1$. Si $c = 1$, esto termina siendo $O(j)$. La $O$ grande funciona raro con múltiples variables, lo que nos obliga a decir que esto en realidad es $O(c^j + j)$. Recontra molesto, y parte de la razón por la que fuimos más laxos con la corrección.

2. Para construir un orden de los juguetes podemos, por cada posición del orden, elegir un juguete de los que queden poner. Una solución parcial entonces va a ser un prefijo de un orden de juguetes. Agregar un juguete nuevo a este orden, si se hace inteligentemente, se puede hacer en $O(1)$. Eso es lo que vamos a tardar en cada nodo interno entonces en principio. ¿Cuántos nodos internos hay? Va a haber uno por cada posible prefijo de un orden, o sea, $(j+1)!$. Perfecto, ahora veamos cuánto se tarda en cada hoja. Si lo hacemos bien fuerza bruta, tendríamos que recorrer todos los juguetes en cada hoja, sumando en un contador la altura de cada uno hasta pasarnos de $A$. Pero esto está todo bien, porque, como hay $O(j!)$ hojas, la complejidad en las hojas nos queda $O(j! \cdot j)$. En total entonces tenemos $$O((j+1)! + j! \cdot j) = O(j! \cdot (j+1) + j! \cdot j) = O(j! \cdot (j+1 + j))= O(j! \cdot j).$$

**Bonus track:** hay *otra* posible solución, si observamos que en la 1. asignar el primer juguete al cajón 1 o al cajón 26 es lo mismo. Lo que vamos a hacer entonces es asignar juguetes a cajones que ya tengan algún juguete, o a un cajón nuevo cualquiera. Cada nodo del árbol va a tener una cantidad de hijos variable, que va a depender de cuántos cajones había asignado su padre. La cantidad de nodos del árbol acá nos da $$O(\sum_{i=0}^j \sum_{k=1}^c S(i, k)),$$ donde $S(i, k)$ es el [número de Stirling de segundo tipo](https://en.wikipedia.org/wiki/Stirling_numbers_of_the_second_kind). Andá a saber eso.


## Pregunta 2
**Respuestas correctas**
- Ninguna de estas respuestas es correcta.

**Explicación**
- ~~*Utilizar memorización garantiza que solo se calculen los subproblemas necesarios, lo que
siempre reduce el tiempo de ejecución del algoritmo.*~~ No siempre se reduce el tiempo de ejecución. Además, a veces calculás subproblemas que después no usás, como ser muchas veces que hacemos bottom-up.
- ~~*Cuando hay múltiples parámetros en la función implementada se necesita una matriz (de 2
o más dimensiones) para memorizar los resultados.*~~ A veces algunos de los parámetros son constantes que no hace falta memroizar.
- ~~*Utilizar memorización te obliga a utilizar una solución recursiva para cada problema.*~~ Fijate cómo te sale más fácil hacer bottom-up y después hablamos.
- *Ninguna de estas respuestas es correcta.* Esta sí es correcta, así que medio mentirosa.
- ~~*Hay más de un posible valor de retorno para cada subproblema.*~~ No podríamos memorizar si este fuese el caso.

## Pregunta 3
**Respuestas correctas**

- $O(\log(n)*k)$

**Explicación**

Acá hay dos posibles algoritmos, los dos con la misma complejidad. Los dos se bancan en el hecho que nos podemos fijar rápidamente si un intervalo de valores tiene un hueco o no. Por ejemplo, si tenemos que el elemento de la posición 3 es 15, y el de la posición 5 es el 13, como el arreglo tiene todos elementos es decreciente, forzosamente la posición 4 tiene que tener el 14, y entonces no hay ningún hueco entre la posición 3 y la 5. En general, si tenemos que la diferencia entre el elemento en la posición $i$ y el de la posición $j$ es igual a $j - i$, no hay huecos entre las posiciones $j$ e $i$.

El primer algoritmo consiste en partir el arreglo por la mitad, encontrar el mayor hueco a la izquierda y a la derecha recursivamente, y tomar el mayor de los dos. En principio, esto sería una recurrencia en la que en cada paso partimos en 2, y sumamos un costo constante por cada merge: $$T(n) = 2T(n/2) + 1.$$ Esto es $O(n)$. Podemos sin embargo aplicar lo que notamos en el párrafo anterior para no hacer recurrencia cuando ya sabemos que en una de las mitades no puede haber ningún hueco. Como sabemos que hay $k$ huecos, como máximo vamos a tener $k$ caminos en el árbol de recurrencia que alcancen una hoja del último nivel. Podemos acotar la cantidad de nodos en este árbol como $O(\log(n)\cdot k)$, lo cual nos deja la complejidad que queríamos. Sin embargo, la cantidad de hijos de cada nodo en el árbol no es constante, y entonces no podemos usar el teorema maestro para calcular la complejidad.

El segundo algoritmo, en cambio, usa búsqueda binaria para encontrar la primer posición en la que se detecta que hay un hueco, usando la propiedad que vimos antes. Para la búsqueda binaria, se fija si entre la posición 0 y la $i$ puede haber un hueco comparando las dos posiciones. Si hay un hueco, achica el $i$, sino, lo agranda. Una vez que encontró la primer posición en la que hay un hueco, reinicia la búsqueda desde esa posición. En resumen, hace una búsqueda binaria por cada hueco en el arreglo, es decir, $O(\log(n)*k)$. El chiste acá es que el algoritmo de búsqueda binaria es divide and conquer, o sea que el $\log(n)$ puede salir del teorema maestro. Técnicamente no se puede calcular la complejidad del algoritmo total con el teorema maestro, y es discutible si esto es un algoritmo de divide and conquer, pero ninguna de las respuestas que dan razones por las que no se puede calcular su complejidad con el teorema maestro dan la razón correcta. Decidimos entonces poner como correctas las dos opciones.

## Pregunta 4
**Respuestas correctas**

- La complejidad es ajustada, la demostración es incorrecta.

**Explicación**
La demo es incorrecta porque $\epsilon$ no puede ser negativo en el teorema maestro. Sin  embargo, $5 \cdot n^{1/4}$ sí pertenece a $O(n^{\log_93-\epsilon})$ para un $\epsilon$ muy chico, ya que $O(n^{\log_93-\epsilon}) \approx O(n^{1/2})$, así que podemos arreglar la demo para que nos quede la misma complejidad.

## Pregunta 5
**Respuestas correctas**

- Toda orientación acíclica de un grafo tiene al menos un sumidero.
- Todo grafo etiquetado con al menos una arista tiene una cantidad par de orientaciones acíclicas distintas.

**Explicación**

- *Toda orientación acíclica de un grafo tiene al menos un sumidero.* Si no hay sumidero, forzosamente existe un recorrido infinito, que como el grafo es finito debe repetir vértices, y por lo tanto tener un ciclo.
- *Todo grafo etiquetado con al menos una arista tiene una cantidad par de orientaciones acíclicas distintas.* Tomemos una orientación acíclica cualquiera y demos vuelta todas las aristas. Claramente sigue siendo acíclica, porque cada ciclo de la orientación nueva sería un ciclo de la orientación original. Emparejar las orientaciones acíclicas de esta manera demuestra que son todos grupitos de 2, y por lo tanto hay una cantidad par.
- ~~*Todo grafo etiquetado con al menos una arista tiene una cantidad impar de orientaciones
acíclicas distintas.*~~ Es el contrario de la primera.
- ~~*Todo grafo etiquetado tiene al menos un sumidero.∗~~ Un grafo etiquetado ni siquiera es dirigido.

## Pregunta 6
**Respuestas correctas**

- Toda orientación acíclica de $K_n$ tiene un único sumidero.
- Si a una orientación acíclica $H$ de un grafo le agregamos un nuevo vértice $v$ junto a una arista $u → v$ para todo vértice $u$ de $H$, entonces el grafo resultante es la orientación acíclica de un grafo.

**Explicación**

- *Toda orientación acíclica de $K_n$ tiene un único sumidero.* Si tuviese dos $u$ y $v$, serían adyacentes en el grafo original, y la arista $(u, v)$ tendría que o salir de $u$ o salir de $v$, contradiciendo que son los dos sumideros. Además, sabemos que tiene por lo menos uno por lo visto en la Pregunta 5.
- *Si a una orientación acíclica $H$ de un grafo le agregamos un nuevo vértice $v$ junto a una arista $u → v$ para todo vértice $u$ de $H$, entonces el grafo resultante es la orientación acíclica de un grafo.* Ningún ciclo puede contener a $v$, porque $v$ es sumidero. Y tampoco ningún ciclo puede no contener a $v$, porque entonces sería un ciclo en $H$, que era una orientación acíclica.
- ~~*Toda orientación acíclica de $K_n$ tiene exactamente dos sumideros (para $n ≥ 4$).*~~ Ver el primer ítem.
- ~~*El grafo $K_n$ etiquetado tiene exactamente $n^2$ orientaciones acíclicas distintas.*~~ Son $n!$, todos los posibles órdenes de los vértices.

## Pregunta 7
**Respuestas correctas**

- Si $G$ es un grafo conexo entonces no es un grafo junta.
- La existencia de un ciclo en un grafo asegura que el grafo es conexo.

**Explicación**

Esta era la que había que elegir las **falsas**.

- *Si $G$ es un grafo conexo entonces no es un grafo junta.* Contraejemplo: $K_2$.
- *La existencia de un ciclo en un grafo asegura que el grafo es conexo.* Contraejemplo: $K_3 \cup K_1$.
- ~~*El digrafo resultado de darle una orientación a un grafo completo tiene un camino de longitud $n − 1$.*~~ Quizá estemos usando un cañón para esto, pero agarremos el digrafo $D$ en donde cada nodo es una componente fuertemente conexa maximal de la orientación del $K_n$, y son hay un arco entre nodos si y solo si hay un arco entre las componentes fuertemente conexas correspondientes. Este digrafo también es la orientación de un completo, pero no tiene ciclos, porque sino los nodos que formasen parte del ciclo en realidad formarían una misma componente fuertemente conexa. Notemos que cualquier camino en $D$ se puede trasladar a $K_n$: pasar por un nodo en $D$ es como pasar por un camino en la componente fuertemente conexa en $K_n$. Si encontramos un camino que pase por todos los nodos de $D$, vamos a encontrar un camino que pase por todos los vértices de $K_n$. Hacemos lo siguiente: agarramos un nodo de $D$ que tenga grado de entrada 0, lo tomamos como el primero del camino, y lo sacamos de $D$. Repetimos esto hasta quedarnos sin nodos en $D$. Como el original era completo, este camino existe en $D$, y por lo tanto encontramos uno en $K_n$ que pasa por todos los vértices.
- ~~*Si $G$ tiene exactamente 2 vértices con grado impar, tiene que haber un camino entre ellos.*~~ Tomemos 2 vértices con grado impar $u$ y $v$. Supongamos que no hay camino entre ellos, o sea, que pertenecen a distintas componentes conexas. Sabemos que la suma de los grados es par en cada componente conexa. Además, no puede haber otro vértice con grado impar en ninguna de las dos componentes conexas, porque $u$ y $v$ eran los únicos vértices de grado impar. Pero entonces hay un sólo vértice de grado impar en cada componente conexa, y la suma tiene que dar impar. ABSURDO!

## Pregunta 8
**Respuestas correctas**

- Todos los vértices a distancia $k$ de la raíz son visitados antes que los de distancia $k + 1$.

**Explicación**

- *Todos los vértices a distancia $k$ de la raíz son visitados antes que los de distancia $k + 1$.* Así funca BFS.
- ~~*Se necesita que el grafo de entrada sea representado con una matriz de adyacencias para alcanzar la complejidad óptima.*~~ Nop, se necesita que sea lista de adyacencias.
- ~~*Para cada grafo $G$ hay un único árbol BFS posible.*~~ Simplemente cambiar el vértice de donde empezás te da un árbol potencialmente diferente.
- ~~*Para toda arista $(u,v)$ del grafo original, o $u$ es ancestro de $v$ en el árbol BFS, o $v$ es ancestro de $u$.*~~ Esto era para DFS nomás. Contraejemplo: $K_3$. 

## Pregunta 9
**Respuestas correctas**

- $O(|V | + |E|)$

**Explicación**
Cada arco $u \rightarrow v$ es visitado una sola vez: ni bien se visita $u$. Cada vértice también es visitado una vez, que es cuando se marca como visitado. Ergo, se hace una cantidad de operaciones proporcional a la suma de los dos.

## Pregunta 10
**Respuestas correctas**

- Ninguna de estas respuestas es correcta.

**Explicación**
- ~~*La cantidad de vértices de $D$ con grado de entrada 0.*~~ Contraejemplo: el grafo $u \rightarrow v$, si se empieza el primer DFS desde $v$.
- ~~*La cantidad de componentes conexas de $D$.*~~ Contraejemplo: el grafo $u \rightarrow v$, si se empieza el primer DFS desde $v$.
- ~~*La cantidad de componentes fuertemente conexas de $D$.*~~ Contraejemplo: el grafo $u \rightarrow v$, si se empieza el primer DFS desde $u$. Fua este grafo sirve para todo.
- ~~*La cantidad total de vértices de $D$.*~~ Contraejemplo: el grafo $u \rightarrow v$, si se empieza el primer DFS desde $u$. Para qué pensar en más vértices que 2.
- *Ninguna de estas respuestas es correcta.* Excepto esta.