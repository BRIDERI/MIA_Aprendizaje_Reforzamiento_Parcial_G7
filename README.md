# Aprendizaje por Reforzamiento aplicado a rutas peatonales accesibles  
## Paso a Desnivel Bertello — Anillo Vial Periférico

Prototipo académico que modela el cambio de las rutas peatonales producido por la
configuración proyectada del **Paso a Desnivel Bertello**, ubicado en el cruce de la
**avenida Canta Callao** con la **avenida Bertello**, como parte del proyecto
**Anillo Vial Periférico (AVP)**.

El sistema representa a un **peatón virtual** que aprende, mediante interacción con
un entorno discretizado, cómo desplazarse desde un punto de origen hasta un destino,
evitando barreras físicas, vías vehiculares y accesos incompatibles con su perfil de
movilidad.

---

> **Examen Parcial — Maestría en Inteligencia Artificial**  
> **Curso:** Aprendizaje por Reforzamiento  
> **Sección:** A  
> **Grupo:** 7

### Integrantes

- Brigitte Scarlett Del Río Ricce
- Julio Machado Torres

### Docente

- MEng. Maria Fernanda Tejada Begazo

---

[![Abrir en Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/BRIDERI/MIA_Aprendizaje_Reforzamiento_Parcial_G7/blob/main/AR_PARCIAL_G7.ipynb)

## Estado del proyecto

**Funcionando en entorno determinístico.**

La versión actual implementa:

- un entorno Gridworld creado manualmente;
- dos escenarios: antes y después de la intervención;
- dos perfiles de movilidad;
- agente aleatorio como línea base;
- Q-Learning tabular;
- SARSA tabular;
- política epsilon-greedy;
- evaluación greedy;
- curvas de aprendizaje;
- comparación de rutas;
- pruebas automáticas;
- experimentos de hiperparámetros;
- análisis automático de resultados;
- experimento adicional en entorno estocástico.

El escenario estocástico se conserva como experimento de análisis. Con la
configuración actual todavía no alcanza una tasa de éxito adecuada, por lo que no
se presenta como resultado concluido.

## Problemática

En la situación actual, el cruce de las avenidas Canta Callao y Bertello funciona
a nivel y permite determinados desplazamientos peatonales relativamente directos.

Con la configuración proyectada del Paso a Desnivel Bertello aparecen nuevas
condiciones:

- muros de contención;
- elevación de la vía;
- rampas peatonales;
- escaleras;
- puentes o pasos peatonales;
- vías vehiculares segregadas;
- árboles;
- jardineras;
- elementos de paisajismo;
- zonas no transitables.

Una persona que antes cruzaba directamente para dirigirse a un colegio, iglesia,
centro de salud, paradero u otro destino tendrá que identificar una nueva ruta.

La ruta adecuada depende también del usuario:

1. **Peatón sin restricciones:** puede utilizar rampas y escaleras.
2. **Movilidad reducida:** no puede utilizar escaleras y debe buscar una ruta
   accesible mediante rampas y pasos habilitados.

## Objetivo general

Implementar y comparar algoritmos básicos de aprendizaje por reforzamiento para
que un peatón virtual aprenda una ruta segura, eficiente y compatible con su perfil
de movilidad en un entorno discretizado inspirado en el Paso a Desnivel Bertello.

## Objetivos específicos

- Modelar el problema como un Proceso de Decisión de Markov.
- Representar veredas, muros, vías, rampas, escaleras, puentes y paisajismo.
- Implementar una línea base aleatoria.
- Implementar manualmente Q-Learning y SARSA.
- Comparar aprendizaje, tasa de éxito, pasos y acciones inválidas.
- Comparar las rutas obtenidas para dos perfiles de movilidad.
- Analizar el efecto de `alpha`, `gamma` y `epsilon`.
- Evaluar un entorno determinístico y una variante estocástica.
- Visualizar las políticas y rutas aprendidas.

## Preguntas del proyecto

1. ¿Qué algoritmo, Q-Learning o SARSA, obtiene una mayor tasa de éxito, menor
   cantidad de pasos y menor número de acciones inválidas?
2. ¿Cómo cambia la ruta aprendida entre un peatón sin restricciones y una persona
   con movilidad reducida?
3. ¿Cómo cambia la longitud del recorrido entre el escenario anterior y el
   escenario proyectado?

## Alcance y advertencia metodológica

> Este prototipo utiliza una representación discretizada e hipotética inspirada en
> el Paso a Desnivel Bertello. No constituye una simulación geométrica exacta, una
> herramienta oficial de orientación peatonal ni una evaluación definitiva de
> accesibilidad.

En esta primera entrega no se implementa:

- conexión directa con Revit;
- procesamiento automático del modelo BIM;
- georreferenciación;
- navegación tridimensional;
- visión artificial;
- redes neuronales;
- DQN;
- datos reales de demanda peatonal.

El proyecto se concentra en la implementación manual y comprensión de técnicas
básicas de aprendizaje por reforzamiento.

## ¿Por qué Aprendizaje por Reforzamiento?

En un mapa fijo, completamente conocido y con un único criterio de distancia,
algoritmos como **Dijkstra** o **A\*** encontrarían la ruta más corta de forma más
directa.

En este proyecto se utiliza aprendizaje por reforzamiento con fines académicos para
estudiar:

- aprendizaje mediante interacción;
- exploración y explotación;
- recompensas acumuladas;
- penalización de acciones inseguras;
- políticas diferentes según el perfil del usuario;
- comportamiento on-policy y off-policy;
- sensibilidad frente a la incertidumbre;
- adaptación a condiciones estocásticas.

No se afirma que RL sea siempre superior a los algoritmos clásicos de búsqueda de
rutas.

## Formulación como MDP

El problema se representa mediante:

\[
\mathcal{M}=\langle \mathcal{S},\mathcal{A},P,R,\gamma\rangle
\]

### Agente

Peatón virtual que aprende a desplazarse hasta el destino.

### Entorno

Gridworld bidimensional inspirado en la configuración del Paso a Desnivel Bertello.

### Estado

```text
estado = (fila, columna, perfil)
```

Dimensiones:

```text
15 filas × 20 columnas × 2 perfiles = 600 estados
```

El perfil forma parte del estado. Por ello, una sola tabla Q puede almacenar
políticas para ambos tipos de usuario.

### Acciones

```text
0 = arriba
1 = abajo
2 = izquierda
3 = derecha
```

### Espacio de acciones

```text
4 acciones por estado
600 estados × 4 acciones = 2400 valores Q
```

### Política

Regla que determina qué acción toma el agente en cada estado.

Durante el entrenamiento se usa una política **epsilon-greedy**:

- con probabilidad `epsilon`, explora;
- en caso contrario, explota la acción con mayor valor Q;
- los empates entre máximos se resuelven aleatoriamente.

### Episodio

Un episodio comienza en el origen y termina cuando:

- el agente llega al destino; o
- alcanza el máximo de pasos permitido.

## Representación del entorno

| Código | Elemento | Perfil sin restricciones | Movilidad reducida |
|:---:|---|:---:|:---:|
| `0` | Vereda | Permitido | Permitido |
| `1` | Muro | Bloqueado | Bloqueado |
| `2` | Vía vehicular | Bloqueado | Bloqueado |
| `3` | Rampa | Permitido | Permitido |
| `4` | Escalera | Permitido | Bloqueado |
| `5` | Puente peatonal | Permitido | Permitido |
| `6` | Árbol | Bloqueado | Bloqueado |
| `7` | Jardinera | Bloqueado | Bloqueado |
| `8` | Origen | Permitido | Permitido |
| `9` | Destino | Permitido | Permitido |

Los obstáculos no se atraviesan. Si el agente intenta ingresar en una celda
bloqueada, permanece en su posición y recibe la penalización correspondiente.

## Sistema de recompensas

| Evento | Recompensa |
|---|---:|
| Movimiento normal | `-1` |
| Llegar al destino | `+100` |
| Chocar con un muro o salir de la grilla | `-25` |
| Invadir una vía vehicular | `-40` |
| Chocar con un árbol | `-15` |
| Chocar con una jardinera | `-15` |
| Usar una escalera con movilidad reducida | `-50` |
| Superar el máximo de pasos | `-20` |

La única recompensa positiva es llegar al destino. Las rampas, escaleras
permitidas y puentes tienen el mismo costo de movimiento que una vereda.

Esta decisión evita que el agente acumule recompensas repitiendo movimientos sobre
una rampa o puente, comportamiento conocido como **reward hacking**.

## Algoritmos implementados

### Agente aleatorio

Selecciona acciones al azar y no actualiza ninguna tabla Q.

Su función es actuar como línea base. Permite verificar si la mejora observada se
debe realmente al aprendizaje.

### Q-Learning

Algoritmo de control TD **off-policy**:

\[
Q(s,a)\leftarrow Q(s,a)+
\alpha\left[r+\gamma\max_{a'}Q(s',a')-Q(s,a)\right]
\]

Q-Learning usa el mejor valor futuro estimado, aunque durante el entrenamiento el
agente esté explorando.

### SARSA

Algoritmo de control TD **on-policy**:

\[
Q(s,a)\leftarrow Q(s,a)+
\alpha\left[r+\gamma Q(s',a')-Q(s,a)\right]
\]

SARSA utiliza la siguiente acción realmente seleccionada por la política
epsilon-greedy.

### Diferencia central

```text
Q-Learning → aprende mirando la mejor acción futura posible.
SARSA      → aprende considerando la acción futura que realmente tomará.
```

En entornos con riesgo y exploración, SARSA puede desarrollar una política más
conservadora.

## Hiperparámetros principales

```python
SEMILLA = 42

EPISODIOS = 5000
ALPHA = 0.1
GAMMA = 0.95

EPSILON_INICIAL = 1.0
EPSILON_MINIMO = 0.05
EPSILON_DECAY = 0.995

MAX_PASOS = 200
```

### Interpretación

- `alpha`: cuánto corrige el agente sus valores Q en cada actualización.
- `gamma`: cuánto valora las recompensas futuras.
- `epsilon`: probabilidad de explorar.
- `epsilon_decay`: velocidad con la que disminuye la exploración.
- `MAX_PASOS`: evita episodios infinitos.

## Dinámica determinística y estocástica

### Entorno determinístico

La acción solicitada se ejecuta exactamente.

### Entorno estocástico

La variante experimental utiliza:

```text
80 % → ejecuta la acción solicitada
10 % → permanece en la misma celda
10 % → se desvía lateralmente
```

Esta incertidumbre puede representar congestión, bloqueo temporal o dificultad de
desplazamiento.

## Flujo de funcionamiento

```text
Construcción del mapa
        ↓
Creación del entorno MDP
        ↓
Inicio del episodio
        ↓
El agente observa el estado
        ↓
Selecciona una acción con epsilon-greedy
        ↓
El entorno ejecuta la transición
        ↓
Entrega recompensa y siguiente estado
        ↓
Q-Learning o SARSA actualiza la tabla Q
        ↓
Se repite hasta destino o timeout
        ↓
Evaluación final con política greedy
```

## Resultados de la ejecución actual

Los siguientes resultados corresponden a la ejecución guardada en la versión
actual del notebook. Pueden variar si se modifican el mapa, los hiperparámetros o
la semilla.

### Comparación frente al agente aleatorio

| Método | Recompensa media | Tasa de éxito | Acciones inválidas por episodio |
|---|---:|---:|---:|
| Agente aleatorio | `-1266.86` | `0.48 %` | `45.19` |
| Q-Learning, últimos 500 episodios | `67.00` | `100 %` | `0.24` |
| SARSA, últimos 500 episodios | `66.70` | `100 %` | `0.24` |

Los dos algoritmos entrenados superan ampliamente la línea base.

### Evaluación greedy final

| Algoritmo | Perfil | Éxito | Pasos | Recompensa |
|---|---|:---:|---:|---:|
| Q-Learning | Peatón sin restricciones | Sí | `25` | `76` |
| Q-Learning | Movilidad reducida | Sí | `25` | `76` |
| SARSA | Peatón sin restricciones | Sí | `25` | `76` |
| SARSA | Movilidad reducida | Sí | `27` | `74` |

### Comportamiento frente al riesgo

Invasiones de la vía vehicular durante todo el entrenamiento:

| Algoritmo | Invasiones |
|---|---:|
| Q-Learning | `2262` |
| SARSA | `1320` |

En los últimos 500 episodios:

| Algoritmo | Invasiones |
|---|---:|
| Q-Learning | `74` |
| SARSA | `57` |

En esta ejecución, SARSA invadió la vía menos veces, resultado coherente con su
naturaleza on-policy y con un comportamiento más conservador durante la
exploración.

### Accesibilidad

| Algoritmo | Perfil | Longitud | Escaleras | Rampas | Puente |
|---|---|---:|---:|---:|---:|
| Q-Learning | Sin restricciones | `25` | `0` | `4` | `4` |
| Q-Learning | Movilidad reducida | `25` | `0` | `4` | `4` |
| SARSA | Sin restricciones | `25` | `0` | `4` | `3` |
| SARSA | Movilidad reducida | `27` | `0` | `5` | `3` |

Las rutas de movilidad reducida no utilizan escaleras, por lo que cumplen la
restricción programada.

En SARSA, la movilidad reducida presenta un sobrecosto de:

```text
+2 celdas = +8 %
```

### Comparación antes y después

En la geometría discretizada actual:

| Perfil | Ruta antes | Ruta después | Incremento |
|---|---:|---:|---:|
| Sin restricciones | `25` | `25` | `0 %` |
| Movilidad reducida | `25` | `25` | `0 %` |

Este resultado no significa que la infraestructura real no incremente el recorrido.
Significa que las posiciones actuales del origen, destino y accesos dentro de la
grilla producen rutas de igual longitud.


## Decisiones de diseño

- Se emplea un Gridworld 2D para mantener el foco en RL tabular.
- El perfil de movilidad forma parte del estado.
- Se usa una tabla Q de `600 × 4`.
- Los obstáculos bloquean físicamente el movimiento.
- La escalera es inválida para movilidad reducida.
- La única recompensa positiva es alcanzar el destino.
- Cada movimiento tiene costo para favorecer rutas cortas.
- Se fija la semilla `42` para reproducibilidad.
- Los algoritmos se implementan manualmente.
- Los empates en acciones greedy se resuelven aleatoriamente.
- La evaluación final usa `epsilon = 0`.
- Se compara con un agente aleatorio.
- Los resultados se generan desde la ejecución real del notebook.


## Limitaciones

- El mapa es hipotético y discretizado.
- Las celdas no tienen todavía equivalencia validada en metros.
- No existe georreferenciación.
- No se utilizan datos reales de peatones.
- No se modelan semáforos ni tiempos de espera.
- No se simulan pendientes reales.
- El BIM no se procesa automáticamente.
- El entorno estocástico requiere nuevos ajustes.
- Los resultados no representan una recomendación oficial de ruta.

## Escalabilidad futura con BIM

En una fase posterior, el modelo BIM podría utilizarse para extraer:

- muros;
- veredas;
- rampas;
- escaleras;
- puentes;
- vías;
- árboles;
- jardineras;
- pendientes;
- niveles;
- coordenadas.

El flujo futuro sería:

```text
Modelo BIM
    ↓
Extracción de geometría y propiedades
    ↓
Matriz de ocupación o grafo peatonal
    ↓
Entorno de aprendizaje por reforzamiento
    ↓
Políticas según perfil de movilidad
    ↓
Visualización de rutas en el modelo
```

