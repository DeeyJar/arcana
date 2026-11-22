---
title: 'Elección del mejor barrio'
tags:
  - bestia
---

## Nombre y enunciado

Una persona se muda a otra ciudad y quiere comprar una vivienda en un barrio tranquilo. Sabe que hospitales, iglesias, supermercados y otras actividades multitudinarias mueven mucha gente en sus cercanías, así que quiere ubicarse lo más lejos posible de todas ellas.

La ciudad tiene forma de cuadrícula de **M × N esquinas**. Se registró la esquina más cercana a cada actividad multitudinaria: son las **esquinas tumultuosas**.

[Enunciado base original (PDF oficial OIA)](https://www.oia.unsam.edu.ar/_media/prob/c3a08n3p1.pdf)

**Modificaciones respecto del enunciado original:**

- Cada esquina tumultuosa tiene una **intensidad** `v` (0 ≤ v ≤ 1000).
- Hay **B cuadras cortadas** que no se pueden transitar. Cada una se describe con las dos esquinas vecinas que une.
- La **molestia** que una esquina tumultuosa provoca sobre una esquina cualquiera es la distancia en cuadras entre ambas (caminando solo por cuadras habilitadas) **menos** su intensidad `v`.

**Problema:** encontrar las esquinas que **maximizan la menor molestia**. Es decir, para cada esquina importa la tumultuosa que más la molesta, y se buscan las esquinas donde esa peor molestia es lo más grande posible.

Enunciado original: OIA, Certamen Nacional 2008, Nivel 3, Problema 1 (`barrio`). Contribución de Hugo Ryckeboer.

---

## Intuición

En el enunciado original (sin cortes ni intensidades) la distancia entre dos esquinas es simplemente la distancia Manhattan, $|x_1 - x_2| + |y_1 - y_2|$. Las dos modificaciones rompen esa comodidad:

- **Los cortes** hacen que la distancia deje de ser Manhattan: una esquina que parece cercana puede quedar lejos si hay que rodear una cuadra cortada. Ya no alcanza con una fórmula, hay que **recorrer** la ciudad como un [[graph]].
- **Las intensidades** hacen que no todas las tumultuosas "pesen" igual: una con `v` alto molesta como si estuviera `v` cuadras más cerca.

Una imagen que ayuda: cada esquina tumultuosa es un **incendio** que se propaga una cuadra por unidad de tiempo, pero no todos arrancan a la vez. El de intensidad `v` empezó `v` unidades de tiempo *antes*. La molestia de una esquina es el momento en que le llega el **primer** fuego, y el mejor barrio es la esquina que **se quema última**.

El problema es un **maximin**: hay un mínimo adentro (la tumultuosa que más molesta a cada esquina) y un máximo afuera (la mejor esquina). Confundir el orden de esas dos operaciones es el error más común.

---

## Definición formal

**Entrada:**

- Una línea con `M N K`: el tamaño de la cuadrícula y la cantidad de esquinas tumultuosas.
- `K` líneas con `x y v`: coordenadas de una esquina tumultuosa (1 ≤ x ≤ M, 1 ≤ y ≤ N) y su intensidad.
- Una línea con `B`: la cantidad de cuadras cortadas.
- `B` líneas con `x1 y1 x2 y2`: las dos esquinas vecinas que une cada cuadra cortada.

**Salida:**

- Una línea con el valor máximo de la menor molestia.
- Una línea con la cantidad de esquinas que alcanzan ese valor.
- Una línea por cada una de esas esquinas, con sus coordenadas `x y`.

**Restricciones:**

- 1 ≤ M, N ≤ 1000, o sea hasta $10^6$ esquinas.
- 1 ≤ K ≤ 100.000.
- 0 ≤ v ≤ 1000.
- Toda esquina es alcanzable desde alguna esquina tumultuosa.

**Condición formal.** Sea $d(t, c)$ la longitud del camino más corto entre la tumultuosa $t$ y la esquina $c$ sin usar cuadras cortadas. La molestia de $c$ es

$$
m(c) = \min_{t} \big( d(t, c) - v_t \big)
$$

y se buscan las esquinas $c$ que maximizan $m(c)$.

Observaciones:

- La molestia puede ser **negativa**. Por ejemplo, sobre la propia esquina tumultuosa vale $-v$.
- Si una esquina aparece repetida como tumultuosa, solo importa la de mayor intensidad.
- El enunciado no fija el orden de las esquinas en la salida. En las soluciones se listan ordenadas por `x` y luego por `y`.

**Ejemplo oficial** (el del enunciado modificado):

```
10 8 3
8 5 2
1 7 0
4 2 5
4
7 2 7 3
9 5 10 5
9 6 9 7
8 5 8 6
```

Salida esperada:

```
4
1
9 8
```

---

## Ejemplo concreto

Para resolver a mano usamos una instancia más chica: una cuadrícula de 5 × 4, con dos esquinas tumultuosas y dos cuadras cortadas.

```
5 4 2
1 1 0
4 4 2
2
3 2 4 2
3 4 4 4
```

- **A** = (1, 1) con intensidad 0.
- **B** = (4, 4) con intensidad 2.
- Cuadras cortadas: entre (3, 2) y (4, 2), y entre (3, 4) y (4, 4).

```
y=4   ·───·───·   B───·
      │   │   │   │   │
y=3   ·───·───·───·───·
      │   │   │   │   │
y=2   ·───·───·   ·───·
      │   │   │   │   │
y=1   A───·───·───·───·
     x=1 x=2 x=3 x=4 x=5
```

**Molestia de A** (distancia desde A menos 0). Los cortes no alargan ningún camino desde A:

| y \ x |  1 |  2 |  3 |  4 |  5 |
|:-----:|---:|---:|---:|---:|---:|
| **4** |  3 |  4 |  5 |  6 |  7 |
| **3** |  2 |  3 |  4 |  5 |  6 |
| **2** |  1 |  2 |  3 |  4 |  5 |
| **1** |  0 |  1 |  2 |  3 |  4 |

**Molestia de B** (distancia desde B menos 2). El corte entre (3, 4) y (4, 4) obliga a B a bajar a la fila 3 para ir hacia el oeste, y el corte entre (3, 2) y (4, 2) lo obliga a rodear para llegar a la fila 2 del lado oeste:

| y \ x |  1 |  2 |  3 |  4 |  5 |
|:-----:|---:|---:|---:|---:|---:|
| **4** |  3 |  2 |  1 | −2 | −1 |
| **3** |  2 |  1 |  0 | −1 |  0 |
| **2** |  3 |  2 |  1 |  0 |  1 |
| **1** |  4 |  3 |  2 |  1 |  2 |

**Menor molestia de cada esquina** (mínimo celda a celda de las dos tablas):

| y \ x |     1 |  2 |  3 |  4 |  5 |
|:-----:|------:|---:|---:|---:|---:|
| **4** | **3** |  2 |  1 | −2 | −1 |
| **3** |     2 |  1 |  0 | −1 |  0 |
| **2** |     1 |  2 |  1 |  0 |  1 |
| **1** |     0 |  1 |  2 |  1 |  2 |

El máximo es **3** y lo alcanza una sola esquina, **(1, 4)**. Por ejemplo, desde B hasta (1, 4) el camino más corto es (4, 4) → (4, 3) → (3, 3) → (3, 4) → (2, 4) → (1, 4): 5 cuadras, y 5 − 2 = 3. Desde A son 3 cuadras, y 3 − 0 = 3. El mínimo es 3.

Salida:

```
3
1
1 4
```

Para ver el efecto de cada modificación sobre esta misma instancia:

- **Sin los cortes**, B llegaría a (1, 4) en 3 cuadras y su molestia ahí sería 1. El máximo pasaría a ser 2, compartido por cuatro esquinas: (1, 3), (2, 2), (3, 1) y (5, 1).
- **Sin las intensidades**, el máximo sería 4, en las esquinas (2, 4) y (5, 1).

---

## Por dónde empezar

Lo primero es ver la ciudad como un grafo: cada esquina es un vértice y cada cuadra habilitada es una arista de peso 1. Las cuadras cortadas son aristas que no existen.

Con eso, una primera aproximación es la **fuerza bruta**: calcular la distancia desde cada tumultuosa hasta todas las esquinas con un BFS, restar la intensidad y quedarse con el mínimo en cada esquina. Es correcta y fácil de verificar, pero repite el recorrido completo de la ciudad K veces.

El salto siguiente es notar que no hace falta separar las tumultuosas: todas pueden "propagarse" a la vez, cada una arrancando con un valor inicial de $-v$. Si siempre se avanza primero desde la esquina con menor molestia pendiente, la primera vez que la propagación llega a una esquina ya es su menor molestia posible. Eso es un Dijkstra con varios orígenes, que resuelve todo en un solo recorrido.

---

## Soluciones disponibles

- [[eleccion-del-mejor-barrio-fuerza-bruta]]
- [[eleccion-del-mejor-barrio-greedy]]
