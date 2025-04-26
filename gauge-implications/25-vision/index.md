---
layout: page
title: "Vision2.5"
permalink: /gauge-implications/25-vision/
math: true

---
  
Esta es la documentación técnica de, hasta ahora, mi proyecto más largo. **Vision2.5**  tiene una etapa de construcción (que en sí misma es un proyecto indivudal) y una etapa de uso. Esta página centraliza la documentación, pero existen *subpáginas* encargadas de detallar cada área.

## Tabla de Contenidos

1. [Descripción del Proyecto](#descripción-del-proyecto)
2. [Arquitectura General](#arquitectura-general)
3. [Tecnologías Principales](#tecnologías-principales)
4. [Repositorios](#repositorios)
5. [Documentación Física](#documentación-física)
6. [Documentación Backend](#documentación-backend)
7. [Documentación Frontend](#documentación-frontend)
8. [Entrenamiento](#Entrenamiento)
9. [Comentarios y conclusiones](#Comentarios-y-conclusiones)
10. [Notas](#Notas)



---

## Descripción del Proyecto
**Vision2.5** surge desde la siguiente hipótesis: existe una forma de cuantificar la cantidad de luz dispersada en la atmósfera baja debido a su interacción con los aerosoloes[^1] del medio y relacionar esta cuantificación con parámetros de calidad de aire, bien sea humedad o concentración de material particulado (PM2.5[^2], PM10).

El primer paso en este recorrido es entender y definir un modelo físico que sirva de base para el proyecto. Aunque este modelo se detalla en una página independiente, su fundamento parte de la Ley de Beer-Lambert-Bouguer, que permite relacionar la absorción de luz con las propiedades del materia particulado que atraviesa.

La intensidad de luz resultante de esta interacción, si bien no se mide directamente mediante sensores dedicados, puede ser inferida a partir de dispositivos como cámaras fotográficas. Los sensores de las cámaras, al captar la luz proveniente del ambiente, están recibiendo y registrando alteraciones que reflejan la dispersión atmosférica. Así, existe una correlación entre las predicciones que ofrece el modelo físico y los patrones que captan las cámaras. Cada fotografía se convierte en una matriz de píxeles donde la intensidad lumínica de cada punto es una manifestación de las condiciones atmosféricas del momento.

Sobre esta base, **Vision2.5** plantea como objetivo final automatizar el proceso de estimación de calidad del aire, permitiendo que cualquier usuario pueda acceder a una plataforma web, cargar una fotografía del cielo en su ubicación y recibir una estimación basada en su imagen.

Para alcanzar esta meta, el proyecto se estructura en varias etapas. Primero, se entrena un modelo de redes neuronales convolucionales encargado de detectar las regiones de interés atmosférico dentro de una imagen. Posteriormente, otro modelo se encarga de correlacionar la información lumínica de estas regiones con mediciones convencionales de calidad de aire, tales como la concentración de material particulado o los niveles de humedad.

Esta doble etapa de entrenamiento, basada tanto en información lumínica como en datos de calibración provenientes de métodos tradicionales de medición, es la que finalmente permitirá construir estimaciones confiables y funcionales para su uso en la plataforma web.

## Notas:
[^1]: Se define aerosol como todas aquellas partículas o moléculas grandes suspendidas en la atmósfera.
[^2]: PM2.5 o PM10 hace referencia hace referencia al rango superior de tamaño que tiene el aeroson en *micras*, sinedo PM2.5 aquel material particular (*particular material*) cuyo tamaño es menor a $$2.5um$$, de esto el nombre **Vision2.5**