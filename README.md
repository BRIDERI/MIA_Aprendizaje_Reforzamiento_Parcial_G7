# Rutas Peatonales Accesibles mediante Aprendizaje por Reforzamiento  
## Paso a Desnivel Bertello — Anillo Vial Periférico

Proyecto académico orientado a la búsqueda de rutas peatonales seguras y accesibles
en el entorno del **Paso a Desnivel Bertello**, ubicado en la intersección de la
**avenida Canta Callao** y la **avenida Bertello**, como parte del proyecto
**Anillo Vial Periférico**.

La propuesta representa a un peatón virtual que aprende a desplazarse desde un
punto de origen hasta un destino, considerando obstáculos urbanos, accesos
peatonales y diferentes condiciones de movilidad.

---

> **Examen Parcial — Maestría en Inteligencia Artificial**  
> **Curso:** Aprendizaje por Reforzamiento  
> **Sección:** A  
> **Grupo:** 7

### Integrantes

- Brigitte Scarlett Del Río Ricce  
- Julio Machado Torres  

### Docente

- Mg. Maria Fernanda Tejada Begazo  

---

[![Abrir en Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/BRIDERI/MIA_Aprendizaje_Reforzamiento_Parcial_G7/blob/main/AR_PARCIAL_G7.ipynb)

---

## Descripción del proyecto

La construcción del Paso a Desnivel Bertello modifica la configuración del cruce
entre las avenidas Canta Callao y Bertello. En el nuevo escenario aparecen muros,
rampas, escaleras, puentes peatonales, vías segregadas y elementos de paisajismo
que cambian las rutas habituales de los pobladores.

El proyecto modela esta situación mediante un entorno discretizado en el que un
agente aprende a seleccionar una ruta válida según su perfil de movilidad.

Se consideran dos perfiles:

1. **Peatón sin restricciones:** puede utilizar rampas, escaleras y puentes.
2. **Persona con movilidad reducida:** debe utilizar recorridos accesibles y evitar
   escaleras.

El objetivo del agente es alcanzar el destino con la mayor recompensa acumulada,
reduciendo movimientos innecesarios y evitando acciones inválidas.

---

## Objetivo

Desarrollar y comparar algoritmos básicos de aprendizaje por reforzamiento para
que un peatón virtual aprenda rutas peatonales seguras y compatibles con su perfil
de movilidad en un entorno inspirado en el Paso a Desnivel Bertello.

---

## Formulación del problema

El entorno se modela como un **Proceso de Decisión de Markov (MDP)**:

\[
\mathcal{M}=\langle \mathcal{S},\mathcal{A},P,R,\gamma\rangle
\]

### Agente

Peatón virtual.

### Estado

```text
estado = (fila, columna, perfil)
```

La grilla contiene:

```text
15 filas × 20 columnas × 2 perfiles = 600 estados
```

### Acciones

```text
0 = arriba
1 = abajo
2 = izquierda
3 = derecha
```

### Política

La política determina qué acción debe elegir el agente en cada estado.

Durante el entrenamiento se utiliza una política **epsilon-greedy**, que alterna
entre:

- **exploración:** probar una acción al azar;
- **explotación:** seleccionar la acción con mayor valor Q.

### Episodio

Cada episodio comienza en el punto de origen y termina cuando el agente:

- alcanza el destino; o
- llega al número máximo de pasos.

---

## Elementos del entorno

| Código | Elemento | Peatón sin restricciones | Movilidad reducida |
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

Cuando el agente intenta ingresar en una celda bloqueada, permanece en su posición
y recibe una penalización.

---

## Sistema de recompensas

| Evento | Recompensa |
|---|---:|
| Movimiento normal | `-1` |
| Llegar al destino | `+100` |
| Intentar atravesar un muro | `-25` |
| Intentar ingresar en una vía vehicular | `-40` |
| Chocar con un árbol | `-15` |
| Chocar con una jardinera | `-15` |
| Utilizar una escalera con movilidad reducida | `-50` |
| Superar el máximo de pasos | `-20` |

Las recompensas orientan al agente hacia rutas válidas, seguras y eficientes.

---

## Algoritmos implementados

### Agente aleatorio

Selecciona acciones al azar y se utiliza como referencia inicial para comparar el
aprendizaje de los agentes entrenados.

### Q-Learning

Algoritmo de control **off-policy** que actualiza la tabla Q utilizando el mayor
valor estimado del siguiente estado:

\[
Q(s,a)\leftarrow Q(s,a)+
\alpha\left[r+\gamma\max_{a'}Q(s',a')-Q(s,a)\right]
\]

### SARSA

Algoritmo de control **on-policy** que actualiza la tabla Q utilizando la siguiente
acción realmente seleccionada:

\[
Q(s,a)\leftarrow Q(s,a)+
\alpha\left[r+\gamma Q(s',a')-Q(s,a)\right]
\]

### Diferencia principal

```text
Q-Learning → utiliza la mejor acción futura estimada.
SARSA      → utiliza la acción futura que realmente tomará el agente.
```

---

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

- `alpha`: tasa de aprendizaje.
- `gamma`: factor de descuento.
- `epsilon`: probabilidad de exploración.
- `epsilon_decay`: reducción progresiva de la exploración.
- `MAX_PASOS`: límite de duración de un episodio.

---

## Flujo de entrenamiento

```text
Construcción del escenario
        ↓
Creación del entorno
        ↓
Inicio del episodio
        ↓
Observación del estado
        ↓
Selección de una acción
        ↓
Transición al siguiente estado
        ↓
Recepción de una recompensa
        ↓
Actualización de la tabla Q
        ↓
Repetición hasta alcanzar el destino
        ↓
Evaluación de la política aprendida
```

---

## Resultados de referencia

La ejecución incluida en el notebook utiliza una semilla fija para facilitar la
reproducción de los resultados.

### Entrenamiento determinístico

| Método | Tasa de éxito | Recompensa media | Acciones inválidas por episodio |
|---|---:|---:|---:|
| Agente aleatorio | `0.48 %` | `-1266.86` | `45.19` |
| Q-Learning | `100 %` | `67.00` | `0.24` |
| SARSA | `100 %` | `66.70` | `0.24` |

Los dos algoritmos entrenados alcanzan el destino de manera consistente y superan
ampliamente el comportamiento del agente aleatorio.

### Evaluación de rutas

| Algoritmo | Perfil | Éxito | Pasos | Recompensa |
|---|---|:---:|---:|---:|
| Q-Learning | Peatón sin restricciones | Sí | `25` | `76` |
| Q-Learning | Movilidad reducida | Sí | `25` | `76` |
| SARSA | Peatón sin restricciones | Sí | `25` | `76` |
| SARSA | Movilidad reducida | Sí | `27` | `74` |

### Comportamiento frente a la vía vehicular

| Algoritmo | Invasiones registradas durante el entrenamiento |
|---|---:|
| Q-Learning | `2262` |
| SARSA | `1320` |

En esta ejecución, SARSA registra menos invasiones de vía durante el aprendizaje.

---

## Visualizaciones incluidas

El notebook presenta:

- escenario anterior;
- escenario proyectado;
- curvas de recompensa;
- promedio móvil de recompensa;
- evolución del número de pasos;
- evolución de epsilon;
- comparación entre Q-Learning y SARSA;
- rutas por perfil de movilidad;
- mapas de política;
- mapas de función de valor;
- comparación de acciones inválidas;
- evaluación del entorno estocástico;
- análisis de hiperparámetros.

---

## Pruebas del entorno

El notebook verifica automáticamente:

- estado inicial;
- límites de la grilla;
- bloqueo de muros;
- bloqueo de vías vehiculares;
- bloqueo de árboles y jardineras;
- uso de rampas;
- restricción de escaleras para movilidad reducida;
- finalización al alcanzar el destino;
- dimensiones de la tabla Q;
- codificación y decodificación de estados.

---

## Proyección

El modelo puede ampliarse incorporando información proveniente del modelo BIM:

```text
Modelo BIM
    ↓
Extracción de veredas, muros, rampas y escaleras
    ↓
Conversión a una matriz de ocupación o grafo peatonal
    ↓
Entrenamiento del agente
    ↓
Generación de rutas según el perfil de movilidad
    ↓
Visualización de la ruta en el modelo
```

También puede ampliarse con:

- coordenadas reales;
- pendientes;
- tiempos de desplazamiento;
- congestión peatonal;
- semáforos;
- accesos temporalmente bloqueados;
- distintos orígenes y destinos;
- perfiles adicionales de usuario.

---

## Principio del proyecto

> El agente no recibe la ruta correcta. La descubre mediante interacción,
> recompensas y corrección progresiva de sus decisiones.
