---
layout: page
title: "Detalles de Backend - CashFlow"
permalink: /gauge-implications/cash-flow/back/
mermaid: true
---

Volver a [Documentación general]({{ site.baseurl }}/gauge-implications/cash-flow/index/)


Este documento describe en profundidad la arquitectura, los endpoints, la configuración y los componentes principales del **Backend** de **Cashflow**, centrándose en el **enfoque de arquitectura hexagonal**. Además, se incluyen diagramas de clases y diagramas mermaid que ilustran la estructura interna.


## Tabla de Contenidos
1. [Introducción](#introducción)
2. [Arquitectura General](#arquitectura-general)
3. [Configuración de Entorno](#configuración-de-entorno)
4. [Ejecución en Entorno Local](#ejecución-en-entorno-local)
5. [Modelos de Datos](#modelos-de-datos)
   - [Diagrama ER](#diagrama-er-)
   - [Descripción de Entidades](#descripción-de-entidades)
6. [Endpoints Principales](#endpoints-principales)
   - [Autenticación](#autenticación)
   - [Categorías](#categorías)
   - [Movimientos](#movimientos)
   - [OCR / Facturas](#ocr--facturas)
7. [Manejo de Errores](#manejo-de-errores)
8. [Despliegue](#despliegue)
9. [Contribuciones](#contribuciones)

---

## Introducción

**Cashflow** es una aplicación que permite a los usuarios llevar un registro detallado de sus finanzas personales. A diferencia de otras aplicaciones comerciales de finanzas, **Cashflow** está diseñada para ajustarse a necesidades específicas:

- **OCR Integrado**: Automatiza la extracción de información financiera a partir de fotos de facturas/recibos.
- **Reportes Personalizados**: El sistema categoriza y clasifica los movimientos financieros según la preferencia del usuario.
- **Análisis de Gastos/Ingresos**: Visión detallada de la evolución de gastos e ingresos, permitiendo un mejor control de las finanzas.

Este repositorio corresponde al **backend** de la aplicación, el cual está desarrollado con el objetivo de proveer un **API REST** y manejar la lógica de negocio y persistencia de datos en **PostgreSQL**.

---

## Arquitectura

La siguiente representación muestra una vista general de la interacción entre los principales componentes del proyecto: el **usuario**, el **FrontEnd**, el **Backend**, la **base de datos PostgreSQL** y el **componente OCR**.

### Representación General 

~~~mermaid
flowchart LR
    A[Usuario] --> B[FrontEnd]
    B --> C[(Backend)]
    C --> D[(PostgreSQL)]
    C --> E[(OCR Service)]

    D --> C
    E --> C
~~~

- **Usuario**: Interactúa con el sistema a través de la interfaz web ofrecida por el FrontEnd.
- **FrontEnd**: Envía y recibe información del Backend usando llamadas a la API REST.
- **Backend**: Expone endpoints para la lógica de negocio, validaciones y persistencia de datos. Además, procesa las imágenes enviadas al servicio de OCR.
- **PostgreSQL**: Base de datos para almacenar usuarios, categorías, movimientos financieros y facturas procesadas.
- **OCR Service**: Se encarga de reconocer texto en las imágenes, extrayendo información de precios, fechas y categorías potenciales.

En esta sección se presentan dos tipos de diagramas:
1. **Arquitectura Hexagonal**: Ilustra cómo se divide el core de negocio (dominio) del proyecto y cómo interactúa con los diferentes adaptadores de entrada/salida.
2. **Casos de Uso**: Muestra las interacciones principales del usuario con el sistema.

---

### Diagrama de Arquitectura Hexagonal

El siguiente diagrama ejemplifica la aproximación **Hexagonal (Ports & Adapters)** para el backend de **Cashflow**. El **Dominio** (que contiene la lógica de negocio) se comunica con el mundo exterior a través de **puertos** y **adaptadores**:

~~~mermaid
flowchart LR
    subgraph Dominio
      DomainLogic[Logica de Negocio] --> PuertoIn[Puerto de Entrada]
      DomainLogic --> PuertoOut[Puerto de Salida]
    end

    subgraph AdaptadoresDeEntrada
      Controller(Controllers API)
      CLI(CLI / Comandos)
    end

    subgraph AdaptadoresDeSalida
      DBAdapter(Adaptador BD)
      OCRAdapter(Adaptador OCR)
      ExternalServices(Servicios Externos)
    end

    Controller --> PuertoIn
    CLI --> PuertoIn
    PuertoOut --> DBAdapter
    PuertoOut --> OCRAdapter
    PuertoOut --> ExternalServices
~~~

**Explicación Breve**:
- **Dominio**: Contiene las reglas de negocio (cálculos de finanzas, validaciones, etc.).
- **Puertos de Entrada**: Contratos que exponen métodos para que los adaptadores de entrada (p.ej., API Controllers) se conecten al dominio.
- **Puertos de Salida**: Interfaces que permiten al dominio comunicarse con los adaptadores de salida (BD, OCR, servicios externos).
- **Adaptadores de Entrada**: Implementan la lógica para recibir peticiones HTTP, CLI u otras interfaces. Luego delegan al **Dominio** mediante los puertos de entrada.
- **Adaptadores de Salida**: Concretan la interacción del dominio con herramientas externas, como la persistencia en base de datos, consumo de servicios OCR o APIs de terceros.

---

### Diagramas de Casos de Uso

El siguiente diagrama ilustra el flujo del caso de uso **Crear Movimiento**, desde la interacción del usuario con el frontend hasta la persistencia de los datos en la base de datos.

~~~mermaid
sequenceDiagram
    participant U as Usuario
    participant F as Frontend
    participant C as Controlador
    participant S as Servicio de Aplicación
    participant D as Dominio/Repositorio
    
    U->>F: Registra nuevo movimiento
    F->>C: POST /api/movimientos (monto, categoría, fecha)
    C->>S: crearMovimiento(datos del movimiento)
    S->>D: valida reglas de negocio
    S->>D: guarda movimiento en la base de datos
    D-->>S: regresa movimiento guardado
    S-->>C: movimiento registrado con éxito
    C-->>F: 201 Created (movimiento creado)
    F-->>U: Confirmación del registro exitoso
~~~

#### Descripción del Flujo
1. El usuario registra un nuevo movimiento desde el frontend.
2. El frontend realiza una solicitud HTTP `POST` al controlador con los datos del movimiento (monto, categoría, fecha, etc.).
3. El controlador delega la lógica de creación al servicio de aplicación.
4. El servicio valida las reglas de negocio (e.g., que el monto sea válido, que la categoría exista).
5. Si todo es válido, el servicio guarda el movimiento en la base de datos mediante el repositorio del dominio.
6. El repositorio regresa el movimiento guardado al servicio.
7. El servicio informa al controlador que el movimiento fue registrado con éxito.
8. Finalmente, el controlador responde al frontend con un código `201 Created`, que el frontend muestra al usuario como confirmación.

Este flujo sigue la arquitectura hexagonal del proyecto y mantiene separada la lógica de negocio de las interacciones externas.


---

## Configuración de Entorno

Para poner en marcha el proyecto, se requieren las siguientes variables de entorno (a manera de ejemplo, pueden variar según tu configuración local o de despliegue):

~~~bash
# Archivo .env

# Configuración de base de datos
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=postgres
DB_NAME=cashflow

# Configuración del servidor
PORT=3000

# Configuración OCR (rutas, claves, etc. si aplica)
OCR_API_KEY=tu_api_key_ocr
~~~

- **DB_HOST**: Host donde se encuentra la base de datos.
- **DB_PORT**: Puerto de acceso a la base de datos.
- **DB_USER**: Usuario de la base de datos.
- **DB_PASSWORD**: Contraseña del usuario de la base de datos.
- **DB_NAME**: Nombre de la base de datos a la que se conecta.
- **PORT**: Puerto donde corre el servidor backend.
- **OCR_API_KEY**: Variable de ejemplo para la clave de OCR, si se requiere para servicios externos.

**Nota**: Ajusta estos valores según tu entorno de desarrollo o despliegue.

---

## Ejecución en Entorno Local

A continuación, se describen los pasos básicos para levantar la aplicación localmente:

1. Clonar el repositorio:
   ~~~bash
   git clone https://github.com/sasanchezramirez/cashflow-back.git
   cd cashflow-back
   ~~~

2. Crear y configurar el archivo `.env` en la raíz del proyecto, según [Configuración de Entorno](#configuración-de-entorno).

3. Instalar dependencias (el comando puede variar dependiendo de la tecnología, aquí se ejemplifica con Node.js):
   ~~~bash
   npm install
   ~~~

4. Ejecutar migraciones de la base de datos (dependerá de la herramienta de migración empleada, por ejemplo Sequelize, TypeORM, Flyway, etc.):
   ~~~bash
   npm run migrate
   ~~~

5. Iniciar la aplicación:
   ~~~bash
   npm run start
   ~~~

6. Revisar el log para verificar si el servidor está corriendo correctamente en `http://localhost:3000` (o el puerto configurado).

---

## Modelos de Datos

En la base de datos PostgreSQL, se manejan varias tablas para dar soporte a la lógica de la aplicación. A continuación se muestra un **Diagrama ER (Entidades y Relaciones)** y la descripción de los modelos principales.

### Diagrama ER 

~~~mermaid
erDiagram

    USER {
        integer id
        string name
        string email
        string password
    }

    MOVEMENT {
        integer id
        float amount
        string description
        date movement_date
        integer userId
        integer categoryId
    }

    CATEGORY {
        integer id
        string name
        string type
    }

    OCRINVOICE {
        integer id
        string file_path
        float recognized_amount
        date recognized_date
        integer userId
    }

    USER ||--|{ MOVEMENT : "crea muchos"
    USER ||--|{ OCRINVOICE : "sube muchos"
    CATEGORY ||--|{ MOVEMENT : "agrupar movimientos"
~~~

> **Nota**: Esta representación es una guía general; las entidades reales pueden incluir más campos y relaciones adicionales dependiendo de la implementación final del repositorio.

### Descripción de Entidades

1. **User**  
   - **Campos**:
     - `id`: Identificador único del usuario.
     - `name`: Nombre del usuario.
     - `email`: Correo electrónico (único).
     - `password`: Contraseña cifrada.
   - **Relaciones**:
     - Un usuario puede crear múltiples **Movement**.
     - Un usuario puede subir múltiples **OCRInvoice**.

2. **Movement**  
   - **Campos**:
     - `id`: Identificador único del movimiento.
     - `amount`: Monto de la transacción.
     - `description`: Descripción o referencia.
     - `movement_date`: Fecha del movimiento.
   - **Relaciones**:
     - Pertenece a un **User** (userId).
     - Pertenece a una **Category** (categoryId).

3. **Category**  
   - **Campos**:
     - `id`: Identificador único de la categoría.
     - `name`: Nombre de la categoría (por ejemplo: "Alimentación", "Transporte", etc.).
     - `type`: Tipo de categoría (p.e. "Gasto" o "Ingreso").
   - **Relaciones**:
     - Una categoría puede agrupar múltiples movimientos.

4. **OCRInvoice**  
   - **Campos**:
     - `id`: Identificador único.
     - `file_path`: Ruta o nombre de archivo de la factura en el servidor.
     - `recognized_amount`: Monto reconocido por el OCR.
     - `recognized_date`: Fecha reconocida o fecha de creación del ticket.
   - **Relaciones**:
     - Pertenece a un **User** (userId).

---

## Endpoints Principales

A continuación se describen los endpoints más importantes. Los paths pueden variar según la convención que se utilice en la implementación.

### Autenticación

1. **POST** `/auth/register`  
   - **Descripción**: Permite registrar un nuevo usuario.  
   - **Body** (JSON ejemplo):
     ~~~json
     {
       "name": "Sergio Sanchez",
       "email": "sergio@example.com",
       "password": "123456"
     }
     ~~~
   - **Respuesta exitosa** (201):
     ~~~json
     {
       "message": "User registered successfully",
       "user": {
         "id": 1,
         "name": "Sergio Sanchez",
         "email": "sergio@example.com"
       }
     }
     ~~~

2. **POST** `/auth/login`  
   - **Descripción**: Inicia sesión y genera un token de autenticación (JWT u otro).  
   - **Body** (JSON ejemplo):
     ~~~json
     {
       "email": "sergio@example.com",
       "password": "123456"
     }
     ~~~
   - **Respuesta exitosa** (200):
     ~~~json
     {
       "message": "Login successful",
       "token": "header.payload.signature"
     }
     ~~~

### Categorías

1. **GET** `/categories`  
   - **Descripción**: Obtiene la lista de categorías existentes.  
   - **Respuesta** (200):
     ~~~json
     [
       {
         "id": 1,
         "name": "Comida",
         "type": "Gasto"
       },
       {
         "id": 2,
         "name": "Salario",
         "type": "Ingreso"
       }
     ]
     ~~~

2. **POST** `/categories`  
   - **Descripción**: Crea una nueva categoría (sólo usuarios autenticados o con permisos necesarios).
   - **Body** (JSON ejemplo):
     ~~~json
     {
       "name": "Transporte",
       "type": "Gasto"
     }
     ~~~
   - **Respuesta** (201):
     ~~~json
     {
       "id": 3,
       "name": "Transporte",
       "type": "Gasto"
     }
     ~~~

### Movimientos

1. **GET** `/movements`  
   - **Descripción**: Lista todos los movimientos del usuario autenticado (posiblemente con filtros).
   - **Parámetros opcionales**: `dateRange`, `categoryId`, etc.
   - **Respuesta** (200):
     ~~~json
     [
       {
         "id": 1,
         "amount": 50.0,
         "description": "Compra de supermercado",
         "movement_date": "2025-01-19",
         "categoryId": 1,
         "userId": 1
       },
       {
         "id": 2,
         "amount": 1500.0,
         "description": "Salario",
         "movement_date": "2025-01-15",
         "categoryId": 2,
         "userId": 1
       }
     ]
     ~~~

2. **POST** `/movements`  
   - **Descripción**: Crea un nuevo movimiento para el usuario autenticado.
   - **Body** (JSON ejemplo):
     ~~~json
     {
       "amount": 60.0,
       "description": "Taxi",
       "movement_date": "2025-01-19",
       "categoryId": 3
     }
     ~~~
   - **Respuesta** (201):
     ~~~json
     {
       "id": 3,
       "amount": 60.0,
       "description": "Taxi",
       "movement_date": "2025-01-19",
       "categoryId": 3,
       "userId": 1
     }
     ~~~

### OCR / Facturas

1. **POST** `/ocr/upload`  
   - **Descripción**: Permite subir una imagen de factura/recibo para ser procesada por el modelo OCR.
   - **Body**: Archivo de imagen. Usualmente se utiliza `multipart/form-data`.
   - **Respuesta** (201):
     ~~~json
     {
       "id": 10,
       "file_path": "invoices/2025-01-19_abc123.jpg",
       "recognized_amount": 75.5,
       "recognized_date": "2025-01-19",
       "userId": 1
     }
     ~~~

2. **GET** `/ocr/invoices`  
   - **Descripción**: Lista todos los registros de facturas escaneadas para el usuario autenticado.
   - **Respuesta** (200):
     ~~~json
     [
       {
         "id": 10,
         "file_path": "invoices/2025-01-19_abc123.jpg",
         "recognized_amount": 75.5,
         "recognized_date": "2025-01-19",
         "userId": 1
       }
     ]
     ~~~

---

## Manejo de Errores

El backend maneja errores de forma consistente devolviendo la estructura de respuesta correspondiente al código HTTP. Por ejemplo:

~~~json
{
  "error": {
    "code": 400,
    "message": "Invalid request data"
  }
}
~~~

Algunos tipos comunes de errores:
- **400 Bad Request**: Datos inválidos en la petición.
- **401 Unauthorized**: Falta token de autenticación o es inválido.
- **403 Forbidden**: El usuario no tiene permisos para realizar la operación.
- **404 Not Found**: El recurso solicitado no existe.
- **500 Internal Server Error**: Error inesperado en el servidor.

---

## Despliegue

Para desplegar la aplicación, se pueden utilizar distintas estrategias, entre las cuales se incluyen:

1. **Plataformas en la nube** (Heroku, AWS, Azure, GCP): 
   - Configurar variables de entorno.  
   - Asegurarse de tener la base de datos PostgreSQL accesible desde la plataforma elegida.
   - Subir la imagen Docker o directamente el código fuente según corresponda.

2. **Servidores on-premise**:
   - Instalar y configurar PostgreSQL.
   - Configurar servidor (Node.js, Nginx, etc.).
   - Asegurarse de que los puertos estén abiertos.
   - Ejecutar migraciones y levantar la aplicación en segundo plano (PM2, Systemd, Docker, etc.).

---

## Contribuciones

Las contribuciones al proyecto son bienvenidas. Para contribuir:

1. Haz un **fork** del repositorio.
2. Crea una nueva rama para tu funcionalidad/corrección:  
   ~~~bash
   git checkout -b feature/nueva-funcionalidad
   ~~~
3. Realiza los cambios y escribe pruebas unitarias si corresponde.
4. Haz un **commit** con un mensaje descriptivo:  
   ~~~bash
   git commit -m "Agrega nueva funcionalidad X"
   ~~~
5. Envía un **Pull Request** (PR) al repositorio principal detallando tus cambios.

---

**Esperamos que esta documentación sirva de guía para comprender y colaborar con el backend de Cashflow.**  

Si tienes dudas o preguntas, no dudes en crear un nuevo _issue_ en el [repositorio de GitHub](https://github.com/sasanchezramirez/cashflow-back).  

¡Gracias por tu interés en el proyecto!




---

Volver a [Documentación general]({{ site.baseurl }}/gauge-implications/cash-flow/index/)
