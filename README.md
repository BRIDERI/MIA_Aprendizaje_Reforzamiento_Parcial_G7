# Rutas Peatonales Accesibles con Aprendizaje por Reforzamiento  
## Paso a Desnivel Bertello — Anillo Vial Periférico

Prototipo académico que modela el cambio de las rutas peatonales producido por
la construcción del **Paso a Desnivel Bertello**, ubicado en el cruce de la
**avenida Canta Callao** con la **avenida Bertello**, como parte del proyecto
**Anillo Vial Periférico**.

El sistema representa a un peatón virtual que aprende, mediante interacción con
un entorno discretizado, cómo desplazarse desde un punto de origen hasta un
destino evitando:

- muros de contención;
- vías vehiculares;
- árboles y jardineras;
- accesos incompatibles con su perfil de movilidad;
- recorridos y acciones innecesarias.

Se comparan dos perfiles:

1. Peatón sin restricciones.
2. Persona con movilidad reducida, que no puede utilizar escaleras y debe buscar
   rutas mediante rampas y pasos accesibles.

---

> Examen Parcial · Maestría en Inteligencia Artificial · Curso de Aprendizaje por Reforzamiento · Grupo 7

### Integrantes

- Brigitte Scarlett Del Río Ricce.
- Julio Machado Torres.

### Docente

- MEng. Maria Fernanda Tejada Begazo.

---

[![Abrir en Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/BRIDERI/MIA_Aprendizaje_Reforzamiento_Parcial_G7/blob/main/AR_PARCIAL_G7.ipynb)

## Alcance del prototipo

Este proyecto utiliza una representación **discretizada e hipotética** inspirada
en el Paso a Desnivel Bertello.

No constituye:

- una reproducción geométrica exacta del proyecto;
- una herramienta oficial de orientación peatonal;
- una evaluación definitiva de accesibilidad;
- una integración directa con el modelo BIM.

La primera etapa se concentra en la implementación manual y comprensión de
técnicas básicas de aprendizaje por reforzamiento.

## Problema estudiado

En la situación actual, el peatón puede realizar determinados cruces a nivel
entre las avenidas Canta Callao y Bertello.

Con la configuración proyectada aparecen nuevas barreras y accesos:

- muros de contención;
- rampas;
- escaleras;
- puentes o pasos peatonales;
- vías segregadas;
- elementos de paisajismo.

El agente debe aprender una política que le permita llegar al destino de forma
segura, eficiente y compatible con su perfil de movilidad.

## Formulación como MDP

El problema se modela como un **Proceso de Decisión de Markov**:

\[
\mathcal{M}=\langle \mathcal{S},\mathcal{A},P,R,\gamma\rangle
\]

### Agente

Peatón virtual.

### Estado

```text
estado = (fila, columna, perfil)
