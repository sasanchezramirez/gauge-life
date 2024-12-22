---
layout: post
title: "Poop Manager"
date: 2024-12-21 10:00:00 +0000
categories: [GaugeImplications, PoopManager]
tags: [Hardware, Software, Frontend, Backend]
# image:
#   path: /assets/img/posts/chaplin.png
#   alt: Chaplin, mi compañero.
---

> «Una persona que se aburre es una persona peligrosa, porque una persona aburrida florece (...), porque una persona aburrida puede llegar a conocerse a sí misma»
>
> — (Don) Juan Ignacio Delgado Alemany, como Ignatius Farray

Aburrido viendo a mi gato es que me intereso por cuidar de él en una forma diferente. Fue crucial haber estado aburrido, ese estado fue el que me hizo querer tomar el camino largo, el que hizo querer **tomarme mi tiempo**.



## ¿Qué pretende el proyecto?

Hace un tiempo en una revisión veterinaria me preguntaron cada cuánto Chaplin, mi mascota, iba al baño. Es graciosa la pregunta porque ni yo sé cuántas veces voy yo, ahora para saber las veces que el parcero acudía a su arenero. *"Un estimado"* me preguntaron, pero incluso eso era difícil apuntarlo. ¿Cómo yo podía tener un estimado de eso?

El hecho fue ese, nada más, hasta que semanas después, aburrido, quise establcer una estrategia para concer esto, y poder tener una respuesta clara y precisa ante una próxima pregunta como esta. Un padre responsable que llaman.


## La estrategia

Para contar sucesos necesito detectarlos, identificarlos y ordenarlos. En esecencia eso es lo que haría yo si quisiera tener esta cifra, pero no puedo estar frente al arenero de mi gato de manera continua para obtener esta información. Así que debo *reducirme*[^1] a la mínima expresión funcional que permita hacer esto. ¿Cúal es esta reducción?

### Detectar

El proceso de detección más efectivo que hiciese un humano en esta situación sería observar. La reducción de la observación humana resulta directa en una opción de captura multimedia, en particular imágenes. Sin embargo, considero que que esta forma de detectar trasciende el alcance del problema; no es necesario observar para detectar cuándo algo (un gato) está en determiando espacio (una caja). Es decir, no necesito la información adicional como el color, tamaño, contexto espacial, etc. *Simplemente* cambiar ese ojo humano por un ojo más acotado y simple, cuyo *trigger*[^2] sea usando una información (señal) rápida, eficaz y sencilla de detectar.

Un proceso de detección típico es a partir de interrupciones en estados continuos. El sistema *caja* la mayor parte de su tiempo está vacío, por lo que podemos paramterizar alguna propiedad que cambie cuando el sistema *gato* ingresa en él. Los principales candidatos son el peso y el *volumen de su interior*. Este último parámetro es el elegido.

La distancia de una cara a otra en la caja es constante en este sistema. Al ser fijo este valor, significa que una señal que viaja a una velocidad constante de una caja a a otra siempre será 





[^1]: Este es el proceso lógico que llevé. En este caso yo sería un observador que tiene la capacidad de detectar, identificar y ordenar los sucesos objetivos.
[^2]: Lo que hará activar el proceso de contar en el sistema completo.