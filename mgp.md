# MGP

## Introducción

Los modelos gráficos probabilísticos (MGP) son herramientas poderosas y versátiles para estudiar sistemas aleatorios complejos con muchas variables. Por su parte, existe un conjunto de MGP Causales denominados Redes Bayesianas, que se pueden ver como una generalización de modelos de Markov como Cadenas de Markov, Modelos Ocultos de Markov (HMM) o árboles de Markov. Para este tipo de modelos, la inferencia exacta usualmente involucra las llamadas forward quantities y backward quantities para obtener distribuciones marginales o condicionales y se pueden obtener mediante algoritmos lineales. En el caso de las Redes Bayesianas existe un algoritmo análogo para realizar las mismas tareas llamado Progragación de Creencias (Belief Propagation).

## Propagación de Creencias

El algoritmo de Propagación de Creencias fue propuesto por primera vez por Judea Pearl con un enfoque inicial hacia grafos conexos simples y luego fue generalizado para grafos conexos múltiples. Existen varias variantes del algoritmo para distintos tipos de modelos, en este trabajo nos enfocaremos en su utilización en modelos de Redes Bayesianas.

Formalmente, dado un conjunto finito de variables aleatorias discretas [X_1, X_2, ..., X_n] con una función de masa de probabilidad conjunta p, se quiere calcular las distribuciones marginales de los X_i. El marginal de un solo X_i se calcula de la forma:

![problem-equation](problem.svg)

en donde x' = (x_1', x_2', ..., x'\_n).

Sin embargo, calcular las distribuciones marginales usando la fórmula anterior para cada X_i es una operación exponencial en base de la cantidad de valores que la variable puede tomar, y esto es muy costoso para grandes cantidades de variables o valores posibles. Con el algoritmo de Propagación de Creencias, podemos calcular estas distribuciones marginales de una manera mucho más eficiente.

La versión de Propagación de Creencias de este trabajo ocurre sobre un grafo no dirigido definido sobre un conjunto de factores llamado Clique Tree, donde cada nodo representa un cluster de variables aleatorias y las aristas conectan estos clústeres. Además, cada arista definida sobre un par de clústeres es asociada con un sepset S_{i,j} \subconjunto C_i \intersection C_j. Ahora, inicialmente los problemas en Redes Bayesianas no poseen esta estructura, por lo que se debe transformar la Red Bayesiana a un Clique Tree, esto es posible mediante un algoritmo que no veremos en profundidad pero se encuentra presente en todos los modelos gráficos en la librería `pgmpy`.

Una vez que tenemos el Clique Tree construido, lo