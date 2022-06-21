# MGP

## Introducción

Los modelos gráficos probabilísticos (MGP) son herramientas poderosas y versátiles para estudiar sistemas aleatorios complejos con muchas variables. Por su parte, existe un conjunto de MGP Causales denominados Redes Bayesianas, que se pueden ver como una generalización de modelos de Markov como Cadenas de Markov, Modelos Ocultos de Markov (HMM) o árboles de Markov.

El problema a resolver en este tipo de modelos gráficos es el llamado problema de inferencia de probabilidad, y se traduce a responder consultas acerca de probabilidades marginales acorde a un grafo específico. Este problema de manera genérica es NP-completo, pero sus soluciones se pueden dividir en dos categorías:

- Solución exacta

- Solución aproximada

En este trabajo nos enfocaremos en soluciones exactas. En este tipo de soluciones, queremos responder consultas del estilo `P(X=x|E=e)` de una manera exacta, algunas de las vías más comunes para resolverlos son los algoritmos llamados:

- Eliminación de Variables

- Propagación de Creencias

En eliminación de variables, queremos computar el valor marginal de un nodo de interés en Modelos Gráficos Dirigidos o Modelos Gráficos no Dirigidos basados en ideas de programación dinámica, este proceso intenta explotar las independencias condicionales en el grafo para reducir la complejidad computacional de los cálculos, sin embargo le prestaremos más interés al algoritmo de Propagación de Creencias.

## Propagación de Creencias

El algoritmo de Propagación de Creencias fue propuesto por primera vez por Judea Pearl con un enfoque inicial hacia grafos conexos simples y luego fue generalizado para grafos conexos múltiples. Existen varias variantes del algoritmo para distintos tipos de modelos, sin embargo nos enfocaremos en su utilización en modelos de Redes Bayesianas.

Formalmente, dado un conjunto finito de variables aleatorias discretas [X_1, X_2, ..., X_n] con una función de masa de probabilidad conjunta p, se quiere calcular las distribuciones marginales de los X_i. El marginal de un solo X_i se calcula de la forma:

![problem-equation](problem.svg)

en donde x' = (x_1', x_2', ..., x'\_n).

Sin embargo, calcular las distribuciones marginales usando la fórmula anterior para cada X_i es una operación exponencial en base de la cantidad de valores que la variable puede tomar, y esto es muy costoso para grandes cantidades de variables o valores posibles. Con el algoritmo de Propagación de Creencias, podemos calcular estas distribuciones marginales de una manera mucho más eficiente.

La versión de Propagación de Creencias de este trabajo ocurre sobre un grafo no dirigido definido sobre un conjunto de factores llamado Clique Tree.

Un Clique Tree es una estructura de datos con forma arbórea donde cada nodo representa un cluster de variables aleatorias y las aristas conectan estos clústeres, en cuyo contexto la intersección es vacía. Además, cada arista definida sobre un par de clústeres es asociada con un sepset S_{i,j} \subconjunto C_i \intersection C_j. Ahora, inicialmente los problemas en Redes Bayesianas no poseen esta estructura, por lo que se debe transformar la Red Bayesiana a un Clique Tree, esto es posible mediante un algoritmo que no veremos en profundidad pero se encuentra presente en todos los modelos gráficos en la librería `pgmpy` por medio de la función `to_junction_tree()` que posee el modelo. Una vez que tenemos el Clique Tree construido, lo que resta es un proceso llamado Paso de Mensajes.

El proceso de Paso de Mensajes varía en dependencia del tipo del modelo gráfico, en el caso de modelos arbóreos, como es el caso luego de obtener el Clique Tree, el algoritmo de Propagación de Creencias computa los marginales de manera exacta, siguiendo un esquema específico de actualización de mensajes, este esquema termina luego de 2 iteraciones y funciona de la siguiente manera:

- Antes de comenzar, es seleccionado un nodo como nodo raíz y cualquier nodo no raíz que se encuentra conectado a un solo nodo es llamado hoja.

- En la primera iteración los mensajes son inicialmente computados en las hojas y “suben” en la estructura hasta alcanzar la raíz. La estructura de árbol garantiza que es posible obtener todos los mensajes de los vecinos de cada nodo, excepto de uno de ellos, que será el que recibirá el mensaje.

- La segunda iteración se compone de enviar los mensajes de forma contraria, comenzando en la raíz y haciéndolos “descender” hasta las hojas. El algoritmo termina cuando todas las hojas hayan recibido su mensaje.


## Calibración del Clique Tree

Una de las ventajas principales que posee el algoritmo de Propagación de Creencias sobre el de Eliminación de Variables radica en consultas múltiples, o sea, para casos en los que necesitamos computar la probabilidad de más de una variable aleatoria. En el caso de Eliminación de Variables debemos correr el algoritmo múltiples veces, lo cual puede ser considerado un costo significativo, sin embargo, utilizando Propagación de Creencias se puede realizar una computación simple que permita responder la consulta múltiple sin hacer ningún cálculo innecesario a posteriori, a este proceso se le llama Calibración del Clique Tree.

## Aplicación
