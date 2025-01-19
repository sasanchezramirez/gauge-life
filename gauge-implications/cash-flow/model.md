---
layout: page
title: "Modelo Cash Flow"
permalink: /gauge-implications/cash-flow/model/
mermaid: true

---

Volver a [Documentación general]({{ site.baseurl }}/gauge-implications/cash-flow/index/)


# Documentación de la Integración OCR en el Backend (Python + FastAPI)

Este documento describe cómo se integra un **módulo OCR** (Python) dentro de un **backend desarrollado en Python usando FastAPI** y siguiendo principios de **arquitectura hexagonal**. El objetivo es **detectar de manera automática** la categoría, el precio, la fecha y la prioridad de compras en facturas o recibos.

## Tabla de Contenidos
1. [Introducción](#introducción)
2. [Objetivos del Módulo OCR](#objetivos-del-módulo-ocr)
3. [Requisitos y Tecnologías](#requisitos-y-tecnologías)
4. [Arquitectura e Integración](#arquitectura-e-integración)
   - [Arquitectura Hexagonal en FastAPI](#arquitectura-hexagonal-en-fastapi)
   - [Flujo de Subida de Imágenes](#flujo-de-subida-de-imágenes)
   - [Endpoints y Puertos de Entrada/Salida](#endpoints-y-puertos-de-entradasalida)
5. [Variables de Entorno](#variables-de-entorno)
6. [Detección de Datos (Categoría, Precio, Fecha, Prioridad)](#detección-de-datos-categoría-precio-fecha-prioridad)
7. [Limitaciones y Posibles Mejoras](#limitaciones-y-posibles-mejoras)
8. [Conclusiones](#conclusiones)

---

## Introducción

En **Cashflow**, el **backend en Python** se encarga de procesar, registrar y gestionar los movimientos financieros. Para **agilizar la creación de movimientos** provenientes de facturas o recibos físicos, se ha desarrollado/integrado un **módulo de OCR** que extrae automáticamente la información relevante (precio, categoría, fecha, prioridad).

El backend usa **FastAPI** para exponer endpoints REST y sigue una **arquitectura hexagonal** (puertos y adaptadores), lo que facilita su mantenibilidad y escalabilidad. El OCR se integra como un adaptador de salida que consume la lógica principal del dominio para almacenar sus resultados.

---

## Objetivos del Módulo OCR

1. **Automatizar** la lectura de datos clave en facturas (precio, fecha, categoría, prioridad).
2. **Reducir errores** de transcripción y ahorrar tiempo al usuario.
3. **Mantener una arquitectura limpia**: El OCR se implementa como un servicio externo o un submódulo que cumple un puerto (interfaz) definido en el dominio.
4. **Mejorar la analítica** posterior de movimientos financieros gracias a la detección de prioridad y categoría.

---

## Requisitos y Tecnologías

1. **Python 3.8+**: Lenguaje principal.
2. **FastAPI**: Framework para crear y gestionar las rutas y lógica de negocio.
3. **Arquitectura Hexagonal**: Separación de la lógica de negocio (dominio) de los adaptadores de entrada (endpoints) y de salida (OCR, base de datos, etc.).
4. **OCR**:
   - Puede ser **Tesseract**, **Pytesseract**, **OpenCV** o un **modelo personalizado** (PyTorch, TensorFlow, etc.).
5. **Base de Datos**: PostgreSQL (u otra) para almacenar la información extraída.

---

## Arquitectura e Integración

### Arquitectura Hexagonal en FastAPI

~~~
Domino (Reglas de negocio, casos de uso)
┌───────────┐
│ Puertos   │   <--- Contratos de entrada/salida
└───────────┘
   ↑     ↓  
Adaptadores: 
- Entrada (FastAPI Controllers, CLI, etc.)
- Salida (BD, OCR, servicios externos)
~~~

- **Dominio**: Contiene las entidades y lógica asociada a la creación de movimientos.
- **Puerto de Entrada**: Interfaz definida para registrar un nuevo movimiento (p.ej., `CreateInvoiceUseCase`).
- **Puerto de Salida**: Interfaz que el dominio invoca para ejecutar la detección OCR (`OCRPort`).
- **Adaptadores**: Concretan la conexión con FastAPI y la herramienta OCR real.

### Flujo de Subida de Imágenes

~~~mermaid
sequenceDiagram
    actor U as Usuario
    participant F as Frontend
    participant API as FastAPI (Adaptador de Entrada)
    participant OCR as Módulo OCR (Adaptador de Salida)
    participant D as Dominio
    participant DB as Base de Datos
    
    U->>F: Sube imagen de la factura
    F->>API: POST /invoices/upload (multipart/form-data)
    API->>D: Ejecuta caso de uso CreateInvoice (pasa ruta de imagen)
    D->>OCR: Invoca método de puerto de salida (detectData(image))
    OCR->>OCR: Procesa imagen (fecha, precio, categoría, prioridad)
    OCR-->>D: Retorna resultados de OCR
    D->>DB: Guarda datos en la entidad Invoice/Movement
    DB-->>D: Confirma registro
    D-->>API: Devuelve datos de la factura registrada
    API-->>F: Respuesta 201 Created (con datos extraídos)
~~~

1. **El usuario** sube una imagen desde el frontend.
2. **FastAPI** recibe el archivo y llama a la lógica del dominio.
3. **Dominio** invoca un **puerto de salida** para solicitar la detección OCR.
4. **OCR** procesa la imagen y retorna la información extraída.
5. **El dominio** guarda la información en la base de datos a través de su repositorio.
6. El backend responde con un código `201` y los datos registrados.



### Endpoints y Puertos de Entrada/Salida

#### Ejemplo de endpoint en FastAPI (adaptador de entrada)

```python
# app/adapters/entrypoints/fastapi_routes.py
from fastapi import APIRouter, File, UploadFile, Depends
from app.domain.usecases.create_invoice import CreateInvoiceUseCase

router = APIRouter()

@router.post("/invoices/upload")
async def upload_invoice(
    file: UploadFile = File(...),
    use_case: CreateInvoiceUseCase = Depends()
):
    # Se guarda el archivo en una carpeta temporal, luego se llama al caso de uso
    temp_path = f"/tmp/{file.filename}"
    with open(temp_path, "wb") as f:
        f.write(await file.read())
    
    invoice_data = use_case.execute(temp_path)
    return invoice_data
```

#### Puerto de Salida (OCRPort)

```python
# app/domain/ports/ocr_port.py
from abc import ABC, abstractmethod

class OCRPort(ABC):
    @abstractmethod
    def detect_data(self, image_path: str) -> dict:
        """
        Retorna un dict con campos:
        {
          'category': str,
          'price': float,
          'date': datetime/date,
          'priority': str
        }
        """
        pass
```

#### Adaptador OCR

```python
# app/adapters/out/ocr_adapter.py
import pytesseract
from app.domain.ports.ocr_port import OCRPort
from PIL import Image
import re

class PyTesseractOCRAdapter(OCRPort):
    def detect_data(self, image_path: str) -> dict:
        # Conversión a texto
        text = pytesseract.image_to_string(Image.open(image_path))
        
        # Ejemplo simplificado de extracción
        price = self._extract_price(text)
        date = self._extract_date(text)
        category = self._extract_category(text)
        priority = self._infer_priority(category, price)
        
        return {
            'category': category,
            'price': price,
            'date': date,
            'priority': priority
        }
    
    def _extract_price(self, text: str) -> float:
        # Regex básico de ejemplo
        match = re.search(r"(\d+(\.\d{1,2}))", text)
        return float(match.group(1)) if match else 0.0
    
    def _extract_date(self, text: str):
        # Extrae fecha con regex sencillo
        # ...
        return "2025-01-19"
    
    def _extract_category(self, text: str) -> str:
        # Busca palabras clave para deducir categoría
        # ...
        return "Comida"
    
    def _infer_priority(self, category: str, price: float) -> str:
        # Ejemplo básico
        if price > 100:
            return "Alta"
        return "Media"
```

---

## Variables de Entorno

En el proyecto se pueden definir variables de entorno para personalizar la integración OCR:

```bash
# .env
OCR_ENGINE=pytesseract
OCR_LANG=spa
OCR_CONFIDENCE_THRESHOLD=0.7
DB_URL=postgresql://user:password@host:5432/cashflow_db
```

- **OCR_ENGINE**: Selecciona el motor OCR (pytesseract, un contenedor con Tesseract, etc.).
- **OCR_LANG**: Configura el idioma principal (ej. `spa` para español).
- **OCR_CONFIDENCE_THRESHOLD**: Umbral mínimo de confianza para la detección.
- **DB_URL**: URL de conexión a la base de datos.

---

## Detección de Datos (Categoría, Precio, Fecha, Prioridad)

1. **Categoría**: 
   - Heurísticas basadas en palabras clave en la factura (p.e. “grocery”, “food”, “restaurant” → `Comida`).
   - O un modelo ML entrenado para clasificar categorías (ej. BERT, TF-IDF, etc.).
2. **Precio**:
   - Regex o heurística para capturar valores monetarios con formato `XX.XX`.
   - Manejo de símbolos monetarios o separadores de miles.
3. **Fecha**:
   - Patrones de fecha (`DD/MM/YYYY`, `YYYY-MM-DD`).
   - Ajustes regionales (día/mes invertidos).
4. **Prioridad**:
   - Reglas de negocio: monto elevado => “Alta”; ciertos tipos de gastos => “Esencial”.
   - Opcionalmente, la categoría determina la prioridad (p.e., “Salud” => “Alta”, “Entretenimiento” => “Baja”).

---

## Limitaciones y Posibles Mejoras

1. **Calidad de las Imágenes**: OCR depende de la nitidez y el formato de la factura. Imágenes borrosas reducen la exactitud.
2. **Formato y Legibilidad**: Facturas con diseños complicados pueden requerir segmentación previa o un modelo de IA más robusto.
3. **Clasificación de Categoría**: Un sistema de palabras clave sencillo puede errar con nuevas categorías. Incluir un **método de aprendizaje continuo** mejoraría la precisión.
4. **Internacionalización**: Para distintos idiomas o monedas, se requiere mayor configuración (ej. regex flexibles o un modelo entrenado multilenguaje).
5. **Procesamiento en Paralelo**: Para grandes volúmenes, considerar **workers asíncronos** (celery, RQ, etc.) que procesen en segundo plano.

---

## Conclusiones

La integración de **OCR** en el **backend Python + FastAPI** (con arquitectura hexagonal) permite:

- **Automatizar** la creación de movimientos financieros a partir de imágenes.
- **Mantener** un diseño escalable y limpio, donde el OCR es un adaptador de salida.
- **Centralizar** la lógica de negocio en el dominio, agnóstico de la implementación específica del OCR.

Para un **sistema más completo**, conviene:

- **Añadir** un pipeline de preprocesamiento de la imagen (desenfoque, rotación, etc.).
- **Entrenar o mejorar** los algoritmos de clasificación de categoría y detección de fecha.
- **Implementar** un sistema de colas para procesar las imágenes de manera asíncrona.

**Con esta arquitectura y definición** se sientan las bases para un módulo OCR robusto, facilitando el registro y clasificación de sus facturas de manera rápida y efectiva.



---
Volver a [Documentación general]({{ site.baseurl }}/gauge-implications/cash-flow/index/)


