---
layout: page
title: "Modelo Cash Flow"
permalink: /gauge-implications/cash-flow/model/
mermaid: true

---

Volver a [Documentación general]({{ site.baseurl }}/gauge-implications/cash-flow/index/)


Esta documentación describe cómo se integra el **módulo de OCR** (creado en Python) con el backend de **Cashflow** para **detectar automáticamente** la categoría, el precio, la fecha y la prioridad de compra de una factura o recibo. Además, se presentan sugerencias sobre qué información extra podría requerirse para completar la implementación.

## Tabla de Contenidos
1. [Introducción](#introducción)
2. [Objetivos del Módulo OCR](#objetivos-del-módulo-ocr)
3. [Requisitos y Tecnologías](#requisitos-y-tecnologías)
4. [Arquitectura e Integración](#arquitectura-e-integración)
   - [Flujo de Subida de Imágenes](#flujo-de-subida-de-imágenes)
   - [Estructura de Carpetas (Python OCR)](#estructura-de-carpetas-python-ocr)
   - [Endpoints y Servicios en Node/Backend](#endpoints-y-servicios-en-nodebackend)
5. [Variables de Entorno](#variables-de-entorno)
6. [Detección de Datos (Categoría, Precio, Fecha, Prioridad)](#detección-de-datos-categoría-precio-fecha-prioridad)
7. [Limitaciones y Posibles Mejoras](#limitaciones-y-posibles-mejoras)
8. [Conclusiones](#conclusiones)

---

## Introducción

El módulo **OCR** (Optical Character Recognition) tiene como propósito **procesar imágenes** de facturas o recibos y extraer de forma automática:
- **Precio** (importe total)
- **Fecha** de emisión o compra
- **Categoría** (clasificación inferida, p. ej. “Comida”, “Transporte”, etc.)
- **Prioridad** o urgencia de la compra

Este módulo se integra con el backend principal de **Cashflow** para agilizar el alta de movimientos financieros sin requerir que el usuario ingrese manualmente toda la información.

---

## Objetivos del Módulo OCR

1. **Reducir el tiempo** de registro de movimientos, automatizando la lectura de datos relevantes.
2. **Disminuir errores** de digitación, asegurando que el importe y fecha coincidan con la factura.
3. **Sugerir categorías** de forma inteligente basándose en el contenido textual.
4. **Asignar prioridad** al gasto, permitiendo un análisis más profundo del flujo de caja (por ejemplo, si es un gasto esencial vs. uno discrecional).

---

## Requisitos y Tecnologías

1. **Python (versión 3.8+)**: Lenguaje principal del módulo OCR.
2. **Bibliotecas de OCR**: 
   - [Tesseract OCR](https://github.com/tesseract-ocr/tesseract) o 
   - Librerías como `pytesseract`, `OpenCV`, o 
   - Un modelo propio en PyTorch/TF que identifique textos específicos (dependiendo de la implementación).
3. **Conexión al Backend** (Node.js o similar):
   - API para recibir imágenes (multipart/form-data).
   - Endpoint para guardar los resultados devueltos por el OCR.
4. **Base de Datos**: Se asume que la información final de la factura (categoría, precio, fecha, etc.) se almacena en PostgreSQL o la base configurada.

*Nota*: La configuración exacta del OCR (tipo de modelo, entrenamientos adicionales, etc.) podría variar en función de las necesidades y la calidad de las imágenes.

---

## Arquitectura e Integración

La integración se basa en un enfoque **multi-servicio** o **microservicio** donde el backend en Node.js coordina la recepción de la imagen y llama al módulo OCR en Python.

### Flujo de Subida de Imágenes

~~~mermaid
sequenceDiagram
    participant F as Frontend
    participant NB as Backend Node
    participant OCR as OCR Python Service
    participant DB as DB/PostgreSQL
    
    F->>NB: POST /ocr/upload (multipart/form-data)
    NB->>OCR: Envía la imagen a procesar
    OCR->>OCR: Extrae fecha, precio, categoría, prioridad
    OCR-->>NB: Retorna datos OCR en JSON
    NB->>DB: Guarda los datos reconocidos en la tabla correspondiente
    NB-->>F: 201 Created (datos de la factura registrados)
~~~

1. El **Frontend** envía la imagen al **Node Backend**.
2. El backend invoca el **servicio OCR** (Python), pasando la imagen (o ruta a la misma).
3. El servicio OCR **procesa** la imagen y extrae la información deseada.
4. Retorna un objeto JSON con `category`, `price`, `date`, `priority`.
5. El backend **almacena** la información en la base de datos.
6. Retorna una respuesta exitosa al **Frontend**.

### Estructura de Carpetas (Python OCR)

Un ejemplo de estructura de proyecto para el OCR:

```
python-ocr/
├── requirements.txt
├── ocr_app/
│   ├── __init__.py
│   ├── main.py
│   ├── services/
│   │   ├── category_detection.py
│   │   ├── price_detection.py
│   │   ├── date_detection.py
│   │   └── priority_detection.py
│   └── utils/
│       ├── image_preprocessing.py
│       └── text_parsing.py
└── Dockerfile
```

- **services/**: Módulos separados para cada parte de la información que se desea extraer.
- **utils/**: Funciones de utilidad, como preprocesamiento de imágenes o normalización de texto.
- **main.py**: Punto de entrada que define los endpoints (si se corre como microservicio) o las funciones exportables (si se integra de otra forma).

### Endpoints y Servicios en Node/Backend

En el backend Node.js se define un controlador para la ruta de OCR, por ejemplo:
```js
// routes/ocr.js
router.post('/upload', upload.single('invoice'), async (req, res) => {
  try {
    const imagePath = req.file.path; // Ruta temporal o definitiva de la imagen
    const ocrData = await callPythonOCRService(imagePath);

    // Estructura de ocrData esperada:
    // {
    //   category: 'Comida',
    //   price: 123.45,
    //   date: '2025-01-19',
    //   priority: 'Alta'
    // }

    // Guardar datos en DB
    const savedInvoice = await InvoiceModel.create({
      file_path: imagePath,
      recognized_amount: ocrData.price,
      recognized_date: ocrData.date,
      recognized_category: ocrData.category,
      recognized_priority: ocrData.priority,
      userId: req.user.id
    });

    return res.status(201).json(savedInvoice);
  } catch (error) {
    return res.status(500).json({ error: error.message });
  }
});
```

La función `callPythonOCRService(imagePath)` puede, por ejemplo, comunicarse vía HTTP con el servicio Python u operar en la misma máquina/contendor si se instala Python localmente.

---

## Variables de Entorno

Ejemplo de variables relevantes para el servicio OCR y su integración:

```bash
# .env (Backend Node.js)
PYTHON_OCR_URL=http://localhost:5000/ocr/process
OCR_CONFIDENCE_THRESHOLD=0.6

# .env (Python OCR)
OCR_MODEL_PATH=/app/models/ocr_model.pt
OCR_LANG=en
CATEGORIZATION_RULESET=/app/rules/category_rules.json
```

- **PYTHON_OCR_URL**: EndPoint del servicio Python OCR.
- **OCR_CONFIDENCE_THRESHOLD**: Umbral mínimo de confianza (ej. 0.6).
- **OCR_MODEL_PATH**: Ruta donde se encuentra el modelo de deep learning (si aplica).
- **CATEGORIZATION_RULESET**: Archivo con reglas para mapear palabras clave a categorías.

---

## Detección de Datos (Categoría, Precio, Fecha, Prioridad)

1. **Categoría**:  
   - Uso de un _rule-based approach_ o un pequeño clasificador entrenado (ej: _bag of words_ + SVM o un modelo NN simple).  
   - Se buscan palabras clave en el texto extraído (ej: “groceries”, “market”, “restaurant” → categoría “Comida”).

2. **Precio**:  
   - Detección de secuencias numéricas y reconocimiento de patrones monetarios (`\$XX.XX` o `XX,XX`).
   - Normalización de la moneda (ej. pesos, dólares).

3. **Fecha**:  
   - Reconocimiento de patrones de fecha (`DD/MM/YYYY` o `YYYY-MM-DD`).
   - Manejo de distintos formatos y validación final (día, mes y año reales).

4. **Prioridad**:  
   - Se puede basar en la categoría o el monto total (ej: si supera cierto umbral monetario → “Alta”).
   - O la detección de ciertos términos (p.e., “urgente”, “arriendo”, “electricidad” → “Alta”).

### Posible Estructura de Respuesta OCR

```json
{
  "category": "Comida",
  "price": 123.45,
  "date": "2025-01-19",
  "priority": "Media",
  "confidenceScores": {
    "category": 0.85,
    "price": 0.95,
    "date": 0.99
  }
}
```
- Se pueden incluir campos adicionales como `confidenceScores` para depurar cómo de seguro está el modelo en cada dato.

---

## Limitaciones y Posibles Mejoras

1. **Calidad de Imagen**: OCR depende fuertemente de que la factura sea legible. Imágenes borrosas o mal iluminadas pueden disminuir la precisión.
2. **Idiomas y Formatos**: Si la factura está en otro idioma/país con distintos formatos de fecha y moneda, se debe ajustar la configuración.
3. **Precisión de Categoría**: Un rule-based approach simple puede fallar en compras mixtas. Se podría mejorar con un **modelo de clasificación** o una **IA** que identifique contextos.
4. **Escalabilidad**: Para grandes volúmenes de imágenes, se recomienda un sistema que procese en segundo plano (por ejemplo, colas asíncronas con RabbitMQ o AWS SQS).
5. **Mantenimiento del Modelo**: Si se usa un modelo de machine learning, necesitará retraining con datos reales para mejorar la precisión de categorización y prioridad.

---

## Conclusiones

La **integración OCR en Python** para **Cashflow** permite:

- Automatizar la captura de datos de facturas.
- Asignar automáticamente categorías, importe, fecha y prioridad.
- Mantener la arquitectura flexible (Node.js en el backend principal y Python para OCR).

**Información requerida adicional**:
- **Ejemplos de facturas** reales o dataset de entrenamiento (si se usa ML).
- **Especificaciones exactas** de las categorías disponibles y su correspondencia textual.
- **Definición de reglas** para determinar la prioridad (¿se basa únicamente en el monto? ¿Existen palabras clave “urgente”?).

Con esa información, el sistema podría configurarse y entrenarse para lograr mayor precisión, mejor experiencia de usuario y una automatización más robusta.  

---
Volver a [Documentación general]({{ site.baseurl }}/gauge-implications/cash-flow/index/)


