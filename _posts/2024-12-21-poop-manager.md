---
layout: post
title: "Poop Manager"
date: 2024-12-21 10:00:00 +0000
categories: [GaugeImplications, PoopManager]
tags: [Hardware, Software, Frontend, Backend]
math: true
# image:
#   path: /assets/img/posts/chaplin.png
#   alt: Chaplin, mi compañero.
---

> «Una persona que se aburre es una persona peligrosa, porque una persona aburrida florece (...), porque una persona aburrida puede llegar a conocerse a sí misma»
>
> — (Don) Juan Ignacio Delgado Alemany, como Ignatius Farray

Aburrido mientras observaba a mi gato, me interesé en cuidarlo de una forma diferente. Fue crucial haber estado aburrido: ese estado fue el que me impulsó a querer tomar el camino largo y a **tomarme mi tiempo**.


## ¿Qué pretende el proyecto?

Hace un tiempo, en una revisión veterinaria, me preguntaron cada cuánto Chaplin, mi mascota, iba al baño. Es graciosa la pregunta porque ni siquiera sé cuántas veces voy yo, así que mucho menos podía saber cuántas veces iba él a su arenero. *"Un estimado"* me pidieron, pero incluso eso era difícil de registrar. ¿Cómo podía tener un cálculo aproximado?

El asunto quedó ahí, nada más, hasta que semanas después, aburrido, quise establecer una estrategia para conocer este dato y poder tener una respuesta clara y precisa ante una próxima pregunta similar. **Un padre responsable que llaman.**

A continuacón verás una descripción poco técnica del proyecto. Si deseas ver los detalles técnicos pues visitar [Poop Manager]({{ site.baseurl }}/gauge-implications/poop-manager/)


## La estrategia

Para contar sucesos, necesito detectarlos, identificarlos y ordenarlos. En esencia, eso es lo que haría yo si quisiera saber cuántas veces va Chaplin al baño, pero no puedo estar frente al arenero de mi gato de manera continua para obtener esta información. Así que debo *reducirme*[^1] a la mínima expresión funcional que permita hacer esto. ¿Cuál es esa reducción?

### Detectar

El proceso de detección más efectivo que haría un humano en esta situación sería observar. La reducción de la observación humana se puede traducir en una opción de captura multimedia, en particular imágenes. Sin embargo, considero que esta forma de detección trasciende el alcance del problema; no es necesario observar para detectar cuándo algo (un gato) está en determinado espacio (una caja). Es decir, no necesito información adicional como color, tamaño o contexto espacial. *Simplemente* debo cambiar ese ojo humano por uno más acotado y simple, cuyo *trigger*[^2] use una señal rápida, eficaz y sencilla de captar.

Un proceso de detección típico se basa en interrumpir estados continuos. El sistema *caja* pasa la mayor parte del tiempo vacío, por lo que podemos parametrizar alguna propiedad que cambie cuando el sistema *gato* ingresa en él.

Podríamos decir que, si el observador fuese una persona, esta detectaría la presencia de *algo* en la caja cuando se produjera una interrupción visual entre sus ojos (sensor) y la caja vacía (objetivo). Ese mismo *flujo* de detección lo considero ideal para aplicar aquí, simplemente cambiando el espectro de luz visible por microondas, de manera que no sea necesario usar un costoso sensor óptico, sino un emisor-receptor de ondas sencillo y barato.

En esencia, se trata de enviar un haz de luz (microondas) de una pared de la caja a la otra. Cuando el arenero está vacío, la luz tarda un tiempo *t* fijo en ir de una pared a la otra y regresar al sensor[^3]. Si existe una obstrucción en ese recorrido, este "ojo" la detectará al recibir el haz de luz en un tiempo menor, ya que no realizó el trayecto completo[^4]. Esta es entonces la estrategia de detección en el proyecto.

### Identificar

Seguir la pista de cuántas veces Chaplin ingresa en su caja no se reduce solo a saber si el espacio está ocupado; necesitamos **identificar** el instante exacto en que esto sucede para poder contarlo y responder la gran pregunta: “¿Cuántas veces va al baño?”

El razonamiento es más o menos así: el sistema, cada cierto intervalo de tiempo, revisa si **Chaplin** está presente o no. Para ello, se basa en la distancia que mide el sensor. Si esta distancia cae por debajo de cierto umbral, consideramos que Chaplin ha entrado en el arenero. Pero identificar no solo implica saber que entró: también distingue si antes ya estaba allí (está ocupándolo aún) o si, por el contrario, acaba de llegar (y se enciende la emoción de un nuevo suceso). Esto ayuda a separar los momentos de entrada y de salida, permitiendo así saber cuándo **Chaplin** hace su visita al baño y cuándo lo abandona. Al final, estamos detectando transiciones: del estado “no presente” al estado “presente”, y viceversa. Cada transición es un acontecimiento que **identificamos** para llevar la cuenta.

Este mecanismo, por simple que parezca, nos da el poder de ver en el código la historia completa de cada visita de **Chaplin**. Desde la alegría (o terror) del primer maullido (cuando el sensor lo detecta por primera vez) hasta el momento en que abandona su arenero. Dicho en pocas palabras, la clave de esta etapa radica en la comparación de estados para distinguir la entrada, la permanencia y la salida. Con esa sencilla lógica, el conteo adquiere todo el sentido: cada vez que Chaplin ingresa, el sistema aumenta su contador y guarda la información, poniendo un granito de datos más en el historial de su compañero felino. 

### Contar

La etapa de **contar** marca el punto en el que todo cobra sentido: ya no basta con saber si Chaplin está o no en la caja, sino **cuántas veces** ha ido durante el día. Aquí es donde cobra relevancia el repositorio de backend, pues se convierte en la **columna vertebral** para recibir los datos del dispositivo, procesarlos y guardarlos.

La idea es sencilla, pero poderosa:  
1. **Cada vez** que el sensor detecta la presencia de Chaplin, se envía un aviso al servidor.  
2. El backend, como un **archivero diligente**, registra esa visita incrementando un contador.  
3. En el momento en que finaliza el día, el conteo se reinicia y la cuenta vuelve a cero, lista para un nuevo registro.

¿Por qué necesitábamos un mecanismo tan formal para algo tan cotidiano?  
- **Consistencia**: Si usamos solo el sensor para sumar, corremos el riesgo de perder datos cuando se reinicia o si se interrumpe la energía. En cambio, el servidor mantiene un historial que garantiza que cada visita quede anotada.  
- **Almacenamiento centralizado**: Toda la información —incluidos los momentos de entrada, salidas y la suma total del día— queda guardada en un solo lugar. Es decir, no dependemos de un dispositivo que pueda fallar o desconectarse, sino que tenemos un respaldo remoto de las idas al arenero.  
- **Integración con otras funciones**: No solo queremos ver cuántas veces Chaplin fue al baño, también podemos ver estadísticas de la semana, alertas por inactividad o, por qué no, proyectar gráficos interactivos. Al tener un servidor que centraliza la data, esta versatilidad es posible.

En cuanto a las **decisiones técnicas** para esta parte, se optó por:  
- **Comunicación HTTP**: Se definió un endpoint donde el dispositivo “golpea la puerta” cada vez que Chaplin entra o sale. Esta elección facilita la integración con aplicaciones web o móviles.  
- **Reset diario automático**: Para llevar la cuenta diaria, el backend controla cuándo es “el final del día” (por ejemplo, a las 23:59). Así, se limpia la cuenta sin que tengamos que intervenir.  
- **Contador adicional para la limpieza**: El mismo dato que cuenta las entradas del día también sirve para activar un umbral de limpieza. Si Chaplin “se pasa de la raya” (por ejemplo, 7 visitas), se enciende la necesidad de limpiar. Esta funcionalidad está diseñada para **mejorar el bienestar** de ambos: la mascota, que necesita un espacio higiénico, y tú, que no quieres sorpresas desagradables.

Al final, **contar** no es solo un ejercicio de recolección de números. Es **darle sentido** a los sucesos cotidianos de Chaplin y tener el respaldo de un sistema confiable que registre cada visita. Así, podemos llevar una vida más tranquila  sabiendo que los datos importantes están a salvo en el backend y que, cuando un veterinario pregunte cuántas veces va Chaplin al baño, la respuesta estará a un solo clic de distancia. 

¿A quién no le tranquiliza eso?

## Siguientes pasos

Esto no es más que un proyecto personal y de entretenimiento en mis ratos libres, así que no es mi prioridad trabajar arduamente en él. Es por eso que consolidar el funcionamiento es muy importante aún: que no se mueva mucho el sensor para no descalibrar sus umbrales, que no se acabe la batería y no me de cuenta, que Chaplin no vea el sensor como su juguete. Aunqeu hay algo más importante que debo definir: **¿Qué le doy a Chaplin cuando llegue mil usos?**


---


[^1]: Este es el proceso lógico que seguí. En este caso, yo sería un observador con la capacidad de detectar, identificar y ordenar los sucesos objetivos.  
[^2]: El elemento que activará el proceso de conteo en el sistema completo.  
[^3]: La velocidad de la luz es constante. La distancia *x* de una pared a otra del arenero también lo es. Por tanto, el tiempo *t* de recorrido es fijo.  
[^4]: En la práctica, la detección no es por el tiempo en sí, sino por la variación de fase provocada por el movimiento del gato (**Efecto Doppler**).


