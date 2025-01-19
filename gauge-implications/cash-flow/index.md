---
layout: page
title: "Cash Flow"
permalink: /gauge-implications/cash-flow/
mermaid: true
---
Aquí encontrarás la **documentación general** del proyecto, para los casos específicos puedes visitar la documentación específica para:

- [Front]({{ site.baseurl }}/gauge-implications/cash-flow/front/)
- [Back]({{ site.baseurl }}/gauge-implications/cash-flow/back/)
- [Modelo]({{ site.baseurl }}/gauge-implications/cash-flow/model/)


Este documento reúne la información esencial acerca de **Cashflow**, tanto a nivel de su arquitectura de software (frontend y backend), como de la configuración requerida para su despliegue en **AWS**. Se incluyen diagramas de la arquitectura interna del proyecto, diagramas de casos de uso y un diagrama de infraestructura para guiar la puesta en producción en la nube.

## Tabla de Contenidos
1. [Introducción](#introducción)
2. [Objetivos del Proyecto](#objetivos-del-proyecto)
3. [Arquitectura General](#arquitectura-general)
   - [Arquitectura Interna (Hexagonal)](#arquitectura-interna-hexagonal)
   - [Casos de Uso Principales](#casos-de-uso-principales)
4. [Frontend](#frontend)
5. [Backend](#backend)
6. [Despliegue en AWS](#despliegue-en-aws)
   - [Diagrama de Infraestructura Cloud](#diagrama-de-infraestructura-cloud)
   - [Configuración de Servicios AWS](#configuración-de-servicios-aws)
     - [Amazon ECR](#amazon-ecr)
     - [Amazon ECS (Fargate o EC2)](#amazon-ecs-fargate-o-ec2)
     - [Amazon RDS](#amazon-rds)
     - [Amazon S3](#amazon-s3)
     - [Balanceador de Carga y Certificados SSL](#balanceador-de-carga-y-certificados-ssl)
   - [Flujo de Despliegue Paso a Paso](#flujo-de-despliegue-paso-a-paso)
7. [Conclusión](#conclusión)

---

## Introducción

**Cashflow** es una aplicación completa que permite a los usuarios:

- Registrar movimientos financieros (ingresos y gastos).
- Clasificar dichos movimientos por categorías.
- Subir imágenes de facturas/recibos para procesar la información de forma automática con OCR.
- Analizar reportes de gastos e ingresos de manera más eficiente.

Este proyecto se compone de:
- **Frontend**: Interfaz en React (o tecnología similar), responsable de la experiencia de usuario.
- **Backend**: API REST en Node.js (o la tecnología especificada) con base de datos PostgreSQL.

---

## Objetivos del Proyecto

1. Proveer una **herramienta personalizable** de gestión financiera que no esté limitada por las opciones típicas del mercado.
2. **Automatizar** la captura de datos financieros mediante **OCR**, para simplificar la creación de movimientos.
3. Facilitar un **despliegue en la nube (AWS)** con escalabilidad y alta disponibilidad.
4. Mantener una **arquitectura limpia (Hexagonal)** que permita incorporar nuevas funciones sin comprometer la estabilidad del sistema.

---

## Arquitectura General

### Arquitectura Interna (Hexagonal)

La arquitectura hexagonal facilita la separación de la lógica de negocio (Dominio) de los adaptadores de entrada (Controllers, CLI, etc.) y salida (Base de Datos, OCR, Servicios Externos). Aquí se muestra un diagrama simplificado:

~~~mermaid
flowchart LR
    subgraph Dominio
      DomainLogic[Logica de Negocio] --> PuertoIn[Puerto de Entrada]
      DomainLogic --> PuertoOut[Puerto de Salida]
    end

    subgraph AdaptadoresDeEntrada
      UI(Frontend / Controllers)
      CLI(CLI / Comandos)
    end

    subgraph AdaptadoresDeSalida
      DBAdapter(Adaptador BD)
      OCRAdapter(Adaptador OCR)
      ExternalServices(Servicios Externos)
    end

    UI --> PuertoIn
    CLI --> PuertoIn
    PuertoOut --> DBAdapter
    PuertoOut --> OCRAdapter
    PuertoOut --> ExternalServices
~~~

- **Dominio**: Contiene la lógica principal (cálculo de finanzas, validaciones, reglas de negocio).
- **Puertos**: Definen **interfaces** para que el Dominio interactúe con el exterior, sin conocer detalles de implementación.
- **Adaptadores**: Implementan físicamente estas interfaces (HTTP Controllers para entrada, DB u OCR para salida).

### Casos de Uso Principales

El flujo general de cómo interactúa el usuario con la aplicación se muestra en este diagrama de secuencia:

~~~mermaid
sequenceDiagram
    actor U as Usuario
    participant Front as Frontend
    participant Back as Backend
    participant DB as Base de Datos
    
    U->>Front: Solicita registrar nuevo movimiento
    Front->>Back: POST /api/movements
    Back->>DB: Valida y guarda el movimiento
    DB-->>Back: Retorna movimiento registrado
    Back-->>Front: 201 Created (movimiento creado)
    Front-->>U: Muestra confirmación
~~~

La lógica es similar para otros casos de uso, como inicio de sesión, subida de facturas para OCR, etc.

---

## Frontend

El **Frontend** de Cashflow:
- Desarrollado en **React** (o framework similar).
- Se organiza en carpetas como `components/`, `pages/`, `services/`, etc.
- Se conecta al **Backend** mediante llamadas HTTP (`fetch` o `axios`).

Para más detalles, consulta la [Documentación del Frontend](https://github.com/sasanchezramirez/cashflow-front) donde se explica:

1. **Estructura de archivos** y organización del proyecto.
2. **Componentes principales** y estilo (Material UI, Tailwind, etc., según configuración).
3. **Flujos de usuario** (Crear movimiento, subir factura, ver reportes).
4. **Configuración de entorno** mediante archivos `.env`.

---

## Backend

El **Backend** de Cashflow:
- Está desarrollado con **Node.js** (u otra tecnología compatible).
- Se conecta a **PostgreSQL** como base de datos principal.
- Expone una serie de endpoints REST para:
  - Registro/Iniciar Sesión (Auth).
  - Gestión de categorías y movimientos.
  - Procesamiento de imágenes OCR.

Para mayor detalle, revisa la [Documentación del Backend](https://github.com/sasanchezramirez/cashflow-back), donde se cubre:

1. **Modelado de datos** (User, Movement, Category, OCRInvoice).
2. **Estructura de carpetas** (controllers, services, routes, etc.).
3. **Configuración de variables de entorno** (DB_HOST, DB_USER, PORT, etc.).
4. **Endpoints disponibles** y ejemplos de petición/respuesta.
5. **Diagrama ER** mostrando las relaciones de tablas en PostgreSQL.

---

## Despliegue en AWS

Para habilitar un entorno escalable y robusto, se propone **AWS** como proveedor cloud. A continuación, se presentan los pasos recomendados para desplegar la solución.

### Diagrama de Infraestructura Cloud

~~~mermaid
flowchart LR
    subgraph client["Cliente"]
        user["👤 Usuario"]
    end

    subgraph vpc["VPC"]
        subgraph public["Subredes Públicas"]
            alb["🔄 Application\nLoad Balancer"]
        end
        
        subgraph private["Subredes Privadas"]
            subgraph ecs_cluster["Cluster ECS"]
                ecs_frontend["🖥️ ECS Frontend\nTask Definition"]
                ecs_backend["⚙️ ECS Backend\nTask Definition"]
            end
            
            rds["💾 RDS PostgreSQL"]
            
        end
    end
    
    subgraph storage["Almacenamiento"]
        s3["📁 S3 Bucket\nFacturas"]
        ecr["📦 ECR\nImágenes Docker"]
    end
    
    subgraph processing["Procesamiento"]
        ocr["🔍 Servicio OCR"]
    end

    %% Conexiones
    user --> alb
    alb --> ecs_frontend
    alb --> ecs_backend
    ecs_frontend --> ecr
    ecs_backend --> ecr
    ecs_backend --> rds
    ecs_backend --> s3
    s3 --> ocr
    
    %% Estilos
    classDef vpc fill:#f5f5f5,stroke:#333,stroke-width:2px
    classDef public fill:#e1f5fe,stroke:#333
    classDef private fill:#e8f5e9,stroke:#333
    classDef storage fill:#fff3e0,stroke:#333
    classDef processing fill:#f3e5f5,stroke:#333
    
    class vpc vpc
    class public public
    class private private
    class storage storage
    class processing processing
~~~


1. El **Usuario** interactúa con la aplicación (Frontend).
2. **Application Load Balancer (ALB)** distribuye la carga entrante a instancias de contenedores en ECS.
3. **ECS** aloja contenedores Docker del **Frontend** y **Backend** (opcionalmente se pueden dividir servicios).
4. **ECR** (Elastic Container Registry) almacena las imágenes Docker.
5. **RDS** (PostgreSQL) gestiona la base de datos.
6. **S3** puede utilizarse para almacenar imágenes de facturas, recibos u otros archivos estáticos.
7. El **Servicio OCR** puede ser un contenedor aparte en ECS o un servicio externo (depende de la implementación actual).

### Configuración de Servicios AWS

#### Amazon ECR
1. Crear un repositorio para **cashflow-front** y otro para **cashflow-back**.
2. Generar tokens de autenticación para Docker e iniciar sesión en ECR.
3. Construir las imágenes Docker de frontend y backend; etiquetarlas y subirlas a ECR.

#### Amazon ECS (Fargate o EC2)
1. Crear una **ECS Cluster** en modo Fargate (serverless) o EC2 (requiere gestionar instancias).
2. Configurar **Task Definitions** para **frontend** y **backend**; apuntar a las imágenes ubicadas en ECR.
3. Definir **Service** para cada Task Definition, especificando cuánto CPU/Memoria se asigna.
4. Configurar la **Networking** (VPC, subnets, security groups) para acceso seguro.

#### Amazon RDS
1. Crear una instancia de **PostgreSQL** en Amazon RDS.
2. Definir el tamaño (db.t3.micro para entornos de prueba o mayor en producción).
3. Asegurarse de habilitar la conexión desde ECS a RDS mediante la **Security Group** adecuada.
4. Configurar variables de entorno en ECS para usar `DB_HOST`, `DB_USER`, `DB_PASSWORD`, etc.

#### Amazon S3
1. Crear un bucket para almacenar los archivos de facturas/recibos.
2. Definir políticas de acceso para el backend (o un rol de IAM asociado al servicio ECS).
3. Integrar la carga/descarga de archivos desde la aplicación (backend + frontend).

#### Balanceador de Carga y Certificados SSL
1. Crear un **Application Load Balancer** (ALB) que reciba tráfico HTTP/HTTPS.
2. Configurar un **Listener** en el puerto `443` con **certificado SSL** (vía AWS Certificate Manager).
3. Dirigir el tráfico a la **Target Group** asociada a la tarea de ECS (frontend o backend).
4. Para el **Frontend** estático, se puede usar S3 + CloudFront en lugar de ECS, o incluirlo en un contenedor Docker sirviendo la app.

---

### Flujo de Despliegue Paso a Paso

~~~bash
# 1. Construir imágenes Docker (ejemplo con backend)
docker build -t cashflow-back:latest .

# 2. Autenticarse con AWS ECR
aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <your_account_id>.dkr.ecr.<region>.amazonaws.com

# 3. Etiquetar y subir imagen
docker tag cashflow-back:latest <your_account_id>.dkr.ecr.<region>.amazonaws.com/cashflow-back:latest
docker push <your_account_id>.dkr.ecr.<region>.amazonaws.com/cashflow-back:latest

# 4. Repetir el proceso para la imagen frontend
docker build -t cashflow-front:latest .
docker tag cashflow-front:latest <your_account_id>.dkr.ecr.<region>.amazonaws.com/cashflow-front:latest
docker push <your_account_id>.dkr.ecr.<region>.amazonaws.com/cashflow-front:latest
~~~

1. **ECR**: Definir repositorios para `cashflow-back` y `cashflow-front`.
2. **IAM Roles**: Crear roles para ECS y RDS, permitiendo acceso a ECR y a la base de datos.
3. **ECS Cluster**:
   - Crear cluster en Fargate.
   - Crear Task Definitions para cada contenedor, indicando la imagen, puertos y variables de entorno (`DB_HOST`, `OCR_API_KEY`, etc.).
4. **Servicios**:
   - Definir un servicio ECS para el backend y otro para el frontend.
   - Configurar **Auto Scaling** si se requiere alta disponibilidad.
5. **ALB (Load Balancer)**:
   - Asociar un target group para el backend en el puerto 3000 (por ejemplo).
   - Asignar listener HTTPS en el ALB y conectar la ruta `/api/*` → Backend ECS.  
   - (Opcional) Asignar la ruta `/` → Frontend ECS o servir el frontend en S3/CloudFront.
6. **Comprobar**:
   - Probar la URL generada en el ALB (o dominio personalizado con Route53).
   - Validar conexión con la base de datos y el correcto funcionamiento del OCR.

---

## Conclusión

La presente documentación unifica:
- **Arquitectura del proyecto** (Frontend y Backend).
- **Principales casos de uso** y diagramas de interacción.
- **Guía de despliegue en AWS** con sus componentes clave (ECR, ECS, RDS, S3, ALB).

Con esta base, es posible **escalar el proyecto** de forma modular, manteniendo las ventajas de la **arquitectura hexagonal** y aprovechando los servicios de AWS para asegurar alta disponibilidad, confiabilidad y un proceso de entrega continuo.

---

Para dudas o sugerencias, se recomienda abrir un _issue_ en los repositorios de [Cashflow-Back](https://github.com/sasanchezramirez/cashflow-back) o [Cashflow-Front](https://github.com/sasanchezramirez/cashflow-front).  

