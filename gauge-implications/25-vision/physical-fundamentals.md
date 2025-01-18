---
layout: page
title: "Fundamentos físicos 2.5 Vision"
permalink: /gauge-implications/25-vision/physical-fundamentals/
math: true

---
  
# La Luz en la Atmósfera

La interacción de la radiación electromagnética con la materia es fundamental para comprender y cuantificar la calidad del aire y la visibilidad. En el contexto de la atmósfera, la radiación —especialmente la solar— atraviesa un medio heterogéneo constituido por gases y partículas sólidas o líquidas (aerosoles)[^1]. Dichos componentes pueden absorber, dispersar y transmitir la luz incidente, modificando sus características físico-químicas (intensidad, fase, dirección y composición espectral). La descripción de estos procesos permite establecer relaciones entre la concentración de material particulado y la atenuación de la luz, información esencial para evaluar las condiciones atmosféricas.

---

## Fotometría Atmosférica

La fotometría, en el ámbito atmosférico, se basa en el principio de que la luz (monocromática o policromática) puede sufrir cambios en su intensidad al interactuar con el medio. Estos cambios se describen mediante fenómenos de **absorción** (pérdida de energía fotónica) y **dispersión** (redirección de la luz en diferentes ángulos). Para llevar a cabo mediciones cuantitativas, se aprovechan sistemas de detección como fotodiodos y sensores digitales (CCD o CMOS), los cuales convierten la energía luminosa en señales eléctricas que pueden analizarse y relacionarse con la concentración de diferentes componentes atmosféricos.

Dentro del espectro de la radiación electromagnética, la **luz visible** (aproximadamente de 380 nm a 750 nm) es de especial interés, pues los efectos de calidad del aire y visibilidad se evalúan en gran medida a partir de la percepción humana y la respuesta de dispositivos fotográficos. La **radiación solar**, que muestra un espectro cercano al de un cuerpo negro a aproximadamente 5800 K, es la principal fuente natural de luz que atraviesa la atmósfera. Cualquier cambio que sufre la intensidad de la luz solar (o de fuentes artificiales) debido a la presencia de aerosoles o gases resulta en información útil para describir la calidad del aire.

---

## La clave: Ley de Beer-Lambert-Bouguer

La atenuación de la radiación luminosa al atravesar la atmósfera se describe clásicamente mediante la **Ley de Beer-Lambert-Bouguer**. Esta ley considera que la intensidad de la luz decae de forma exponencial en función de la distancia recorrida y de la densidad de especies atenuadoras presentes en el camino óptico (en otras palabras, debido a aerosoles). Su forma generalizada para un haz de luz de longitud de onda \( \lambda \) es:

$$
I_\lambda = \frac{I_{0,\lambda}}{z_0^2} \, e^{-\tau(\lambda) \, m_{\theta_0}},
$$

donde:

- \( I_\lambda \) es la intensidad de la luz a la salida de la columna de aire.  
- \( I_{0,\lambda} \) representa la intensidad inicial (sin atmósfera, o medida de referencia).  
- \( z_0 \) puede denotar la distancia fuente-objeto o bien un factor geométrico de corrección (por ejemplo, en mediciones astronómicas, representa la distancia astronómica al Sol).  
- \( \tau(\lambda) \) es la **profundidad óptica** o **espesor óptico** a la longitud de onda \( \lambda \).  
- \( m_{\theta_0} \) es la **masa de aire efectiva** atravesada, frecuentemente aproximada con funciones de la secante del ángulo cenital solar \( \theta_0 \).

### Desglose Matemático de la Ley de Atenuación

La ley de Beer-Lambert-Bouguer se fundamenta en un balance diferencial de la intensidad luminosa:

1. **Ecuación diferencial de decaimiento**  
   $$
   \frac{dI_\lambda}{dz} = -\,k(\lambda)\,I_\lambda,
   $$
   donde \( k(\lambda) \) es el coeficiente de atenuación, que incluye contribuciones de absorción y dispersión (scattering).

2. **Integración a lo largo del camino óptico**  
   Si se considera la columna de aire de longitud \( z_0 \), la integración lleva a:

   $$
   \int_{I_{0,\lambda}}^{I_{\lambda}} \frac{dI_\lambda}{I_\lambda} 
   = -\int_{0}^{z_0} k(\lambda)\,dz.
   $$

   Como resultado, se obtiene:

   $$
   \ln\!\biggl(\frac{I_\lambda}{I_{0,\lambda}}\biggr)
   = -\int_{0}^{z_0} k(\lambda)\,dz 
   = -\tau(\lambda).
   $$

3. **Expresión exponencial**  
   Reescribiendo,

   $$
   I_\lambda = I_{0,\lambda}\,\exp[-\,\tau(\lambda)].
   $$

   Así, la profundidad óptica \( \tau(\lambda) \) se define como:

   $$
   \tau(\lambda) = \int_{0}^{z_0} k(\lambda)\,dz.
   $$

De esta manera, la atenuación de la luz depende tanto de las propiedades fisicoquímicas del medio como del recorrido óptico. Cuando en aplicaciones atmosféricas se incluye la geometría solar, se introduce el factor de masa de aire \( m_{\theta_0} \) para corregir la distancia que recorre el rayo de luz en función de la posición del Sol en el cielo (ángulo cenital).

### Contribuciones al Coeficiente de Atenuación

El coeficiente de atenuación total \( k(\lambda) \) se descompone típicamente en:

$$
k(\lambda) = k_a(\lambda) + k_s(\lambda),
$$

donde:

- \( k_a(\lambda) \) corresponde a la **absorción** (debida a moléculas como \( O_3 \), \( NO_2 \), vapor de agua, contaminantes industriales, entre otros).  
- \( k_s(\lambda) \) describe el **scattering** (Rayleigh y Mie, principalmente).

En **scattering Rayleigh**, típico de moléculas más pequeñas que la longitud de onda, la intensidad dispersa es inversamente proporcional a la cuarta potencia de la longitud de onda \(\lambda^{-4}\). En **scattering Mie**, asociado a aerosoles o partículas de tamaños comparables o mayores a la longitud de onda, el comportamiento espectral es más complejo y depende de la distribución de tamaños y del índice de refracción del material particulado.

---

## Medición Fotométrica con Sensores Digitales

La implementación práctica de estos conceptos se basa en la captura de la intensidad luminosa atenuada mediante sensores digitales (CCD o CMOS). Los píxeles de estos sensores convierten la luz en una señal eléctrica proporcional a la cantidad de fotones incidentes, la cual se digitaliza y procesa para obtener valores de intensidad. La relación entre la intensidad registrada y la profundidad óptica se aplica a cada canal espectral, o bien a la respuesta integrada sobre el rango visible.

### Modelo de Color RGB

En gran parte de las aplicaciones de fotometría digital, se trabaja con un sensor equipado con un filtro de color tipo Bayer (RGB). Cada píxel mide preferentemente uno de los tres canales (rojo, verde o azul). La intensidad relativa de cada canal brinda información de las variaciones espectrales de la luz incidente. Por ejemplo:

$$
I_{\text{canal}} 
= \frac{I_{0,\text{canal}}}{z_0^2} \,
  \exp\Bigl[-\,\tau(\lambda)\,m_{\theta_0}\Bigr].
$$

En la práctica, cada canal (R, G o B) abarca un intervalo de longitudes de onda distinto (centrado aproximadamente en 630 nm para rojo, 530 nm para verde y 460 nm para azul, dependiendo del diseño del filtro). Por ello, comparaciones entre la atenuación en cada canal permiten inferir la presencia de ciertos aerosoles o gases que absorben preferentemente en ciertas longitudes de onda.

### Procesamiento Digital de Imágenes

El espacio RGB se representa en un cubo donde cada eje (R, G, B) puede tomar valores típicos de 0 a 255 en una codificación de 8 bits. Un píxel con valores altos en los tres canales tiende al blanco, lo que físicamente puede asociarse a una fuerte dispersión (en el caso atmosférico, indica alta neblina o presencia de partículas que dispersan la luz de forma intensa). Por otra parte, valores bajos en todos los canales se observan en zonas oscuras, asociadas a una absorción significativa o simplemente baja iluminación.

Para la estimación de la calidad del aire mediante imágenes digitales, suelen realizarse pasos como:

1. **Calibración radiométrica**: correlacionar la respuesta del sensor con valores de referencia conocidos de iluminancia o radiancia.  
2. **Corrección atmosférica y geométrica**: tomar en cuenta la posición solar, la topografía y la geometría instrumental.  
3. **Extracción de índices de color**: combinar los canales R, G, B para obtener métricas que se relacionan con la presencia de aerosoles o contaminantes (por ejemplo, índices de turbidez atmosférica).

---

## Relevancia Física y Aplicaciones

La fotometría atmosférica, respaldada en la Ley de Beer-Lambert-Bouguer y en la instrumentación digital, permite la inferencia de variables de interés para:

- **Calidad del aire**: estimación de la concentración de aerosoles y contaminantes gaseosos.  
- **Visibilidad**: determinación de la distancia de alcance visual en carreteras, aeropuertos y zonas urbanas.  
- **Climatología y meteorología**: monitoreo de parámetros como la columna de vapor de agua, el ozono o partículas de quema de biomasa.  
- **Astrofísica y astronomía**: correcciones en magnitudes estelares al considerar la extinción atmosférica, especialmente en la región del visible e infrarrojo cercano.

La combinación de mediciones fotométricas con modelos radiativos y datos complementarios (como mediciones *in situ* de PM\(_{2.5}\), PM\(_{10}\), etc.) fortalece el entendimiento de los procesos de transporte y transformación de contaminantes, así como su impacto en la salud y la percepción visual del entorno.

---

## Conclusión

El marco teórico de la fotometría aplicada al estudio de la atmósfera se basa en la descripción de la interacción de la luz con los componentes gaseosos y particulados. La Ley de Beer-Lambert-Bouguer constituye el pilar matemático para modelar la atenuación de la radiación y, junto con los sensores digitales y los conceptos de colorimetría (RGB), permite un análisis detallado de la calidad del aire y la visibilidad. Agregar justificaciones físicas relacionadas con la dispersión (Rayleigh y Mie) y la absorción (por distintos gases y aerosoles) refuerza la interpretación de los datos, ofreciendo métodos indirectos pero sólidos para la cuantificación y monitoreo continuo de la atmósfera.

---

## Notas
[^1]: De ahora en adelante se usará el término Aerosol para referirse a estas partículas de manera general 