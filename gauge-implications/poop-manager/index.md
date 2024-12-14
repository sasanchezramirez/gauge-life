---
layout: page
title: "Poop Manager"
permalink: /gauge-implications/poop-manager/

---
  
# Documentación del Proyecto

## Contenido
1. [Objetivos Principales](#objetivos-principales)
2. [Funcionalidades del Sistema](#funcionalidades-del-sistema)
3. [Requisitos Técnicos](#requisitos-técnicos)
4. [Métricas](#métricas)
5. [Funcionalidad Adicional](#funcionalidad-adicional)
6. [Conclusión](#conclusión)
7. [Detalles de la documentación](#detalles-de-la-documentación)

## Objetivos Principales
Poop Manager es un proyecto que combina componentes web y electrónicos para monitorear las veces que mi mascota (Chaplin) entra a su caja de arena, con el objetivo de conocer su comportamiento y monitorear la salud de su alimentación y digestión. 

Es además un desafío personal de profundizar en conocimientos técnicos mientras construyo un prototipo útil.

## Funcionalidades del Sistema
### Aspecto Electrónico
- Utiliza un sensor de efecto Doppler de ultrasonido para detectar la presencia de la mascota en el baño.
- El sensor envía una señal vía API utilizando un ESP32.
- Está alimentado por una batería de 5V y realiza mediciones cada 5 segundos.


### Aspecto Web
- Creación de usuarios y acceso a datos mediante autenticación.
- Almacenamiento de información de usuarios y mascotas en una base de datos.
- Recopilación de datos de múltiples mascotas por usuario.
- Presentación de métricas relacionadas a la actividad de la mascota.

## Requisitos Técnicos
### Software
- Frontend construido con Angular 18.
- Backend construido con FastAPI en Python.

>Para mayor detalle técnico sobre el software, diríjase a las secciones de [Backend]({{ site.baseurl }}/gauge-implications/poop-manager/back/) y [Frontend]({{ site.baseurl }}/gauge-implications/poop-manager/front/).
{: .prompt-info }

### Hardware
- Sensor de efecto Doppler de ultrasonido.
- Módulo ESP32.
- Batería de 5V para alimentar el sistema.

>Para mayor detalle sobre el hardware, consulta la sección [Hardware]({{ site.baseurl }}/gauge-implications/poop-manager/hardware/).
{: .prompt-info }

## Métricas
- Cantidad de veces que la mascota entra al baño, la hora y el día.
- Cálculo del promedio de visitas diarias y nocturnas.
- Identificación de patrones y detección de desviaciones importantes.

## Funcionalidad Adicional
- Gestión de limpiezas del baño de la mascota.
- Recomendación de cuándo realizar limpiezas basada en el número de visitas.
- Historial de limpiezas y visualización de la última limpieza realizada.

## Conclusión
Poop Manager es un prototipo diseñado para adquirir conocimientos técnicos y ofrecer una solución práctica para monitorear la salud de las mascotas.

## Detalles de la documentación:

- [Front]({{ site.baseurl }}/gauge-implications/poop-manager/front/)
- [Back]({{ site.baseurl }}/gauge-implications/poop-manager/back/)
- [Hardware]({{ site.baseurl }}/gauge-implications/poop-manager/hardware/)
- [Esquemas de datos]({{ site.baseurl }}/gauge-implications/poop-manager/data-schemas/)