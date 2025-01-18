---
layout: page
title: "Jineteapp"
permalink: /gauge-implications/jineteapp/
mermaid: true
---
  
Jineteapp es una aplicación **fullstack** compuesta de un **backend** (Java Spring Boot + PostgreSQL) y un **frontend** (Angular). Se inspira en la idea de “jinetear” (no seré explícito con este concepto), ofreciendo una plataforma muy específica para gestionar y registrar estos moviminetos.

Esta documentación es un **resumen global** del proyecto, para información más detallada, revisa la documentación específica del [backend](#documentación-backend) y del [frontend](#documentación-frontend).

---

## Tabla de Contenidos

1. [Descripción del Proyecto](#descripción-del-proyecto)
2. [Arquitectura General](#arquitectura-general)
3. [Tecnologías Principales](#tecnologías-principales)
4. [Repositorios](#repositorios)
5. [Documentación Backend](#documentación-backend)
6. [Documentación Frontend](#documentación-frontend)
7. [Cómo Empezar](#cómo-empezar)
8. [Despliegue](#despliegue)
9. [FAQ y Buenas Prácticas](#faq-y-buenas-prácticas)
10. [Contacto y Contribuciones](#contacto-y-contribuciones)

---

## Descripción del Proyecto

El objetivo de **Jineteapp** es ofrecer una **herramienta** que te permita llevar un registro claro y organizado de tus **movimientos financieros**. A grandes rasgos, la aplicación permite:

- **Registrar transacciones** (ingresos y egresos).
- **Gestionar cuentas** (cuentas bancarias, tarjetas, etc.).
- **Categorizar transacciones** (alimentación, vivienda, transporte, etc.).
- **Analizar** tu información para un mejor control y toma de decisiones financieras.

---

## Arquitectura General

El proyecto sigue una **arquitectura cliente-servidor** convencional, donde el **frontend** (Angular) consume los servicios REST que expone el **backend** (Spring Boot). La estructura interna del backend se organiza con **arquitectura hexagonal**, mientras que en el frontend se implementan **módulos y componentes** de Angular siguiendo las buenas prácticas.

~~~mermaid
flowchart LR
    A["Frontend (Angular)"] -->|HTTP/REST| B["Backend (Spring Boot)"]
    B -->|Persistencia| C["PostgreSQL DB"]
~~~

---

## Tecnologías Principales

- **Backend**:
  - *Lenguaje*: Java 17+
  - *Framework*: Spring Boot (2.x o 3.x)
  - *Base de Datos*: PostgreSQL
  - *Arquitectura*: Hexagonal (Puertos y Adaptadores)
- **Frontend**:
  - *Framework*: Angular 15+
  - *Lenguaje*: TypeScript
  - *Herramientas*: RxJS, Angular CLI
- **Otros**:
  - *Control de versiones*: Git (GitHub)
  - *Herramientas de build*:
    - Maven (backend)
    - npm (frontend)
  - *Testing*: JUnit, Mockito (backend), Karma/Jasmine (frontend)

---

## Repositorios

- **Backend**:  
  [jineteapp_back](https://github.com/sasanchezramirez/jineteapp_back)  
  Contiene la lógica de negocio, controladores REST y capa de persistencia.
  
- **Frontend**:  
  [jineteapp_front](https://github.com/sasanchezramirez/jineteapp-front)  
  Maneja la interfaz de usuario con Angular, así como la interacción con el backend a través de HTTP.

---

## Documentación Backend

La documentación detallada del **backend** cubre:

- **Arquitectura hexagonal** y organización en capas (Dominio, Aplicación, Infraestructura).  
- **Entidades** y **casos de uso**.  
- **Controladores REST**, **repositorios** de datos y **configuraciones**.  
- **Ejecución**, **instalación**, **pruebas**, etc.

Para más información, revisa:  
[Jineteapp Backend Documentation]({{ site.baseurl }}/gauge-implications/jineteapp/back/) 
(O donde esté alojada la guía completa en Markdown).

---

## Documentación Frontend

La documentación específica del **frontend** describe:

- **Estructura Angular**: Módulos, componentes, servicios.  
- **Rutas** (routing) e integración con la API.  
- **Configuraciones de entorno** (desarrollo, producción).  
- **Scripts de ejecución**, **testing**, y **despliegue**.

Puedes encontrarla aquí:  
[Jineteapp Frontend Documentation]({{ site.baseurl }}/gauge-implications/jineteapp/front/)

---

## Cómo Empezar

Para **ejecutar localmente** el proyecto completo (frontend y backend), se recomiendan estos pasos:

1. **Clonar repositorios**:
   ~~~bash
   # Clonar backend
   git clone https://github.com/sasanchezramirez/jineteapp_back.git
   
   # Clonar frontend
   git clone https://github.com/sasanchezramirez/jineteapp-front.git
   ~~~

2. **Levantar el backend**:
   - Ajusta credenciales de la base de datos en `application.properties` o variables de entorno.  
   - Compila y arranca:
     ~~~bash
     cd jineteapp_back
     mvn clean install
     mvn spring-boot:run
     ~~~
   - Por defecto, se expone en `http://localhost:8080/api`.

3. **Levantar el frontend**:
   - Instala dependencias:
     ~~~bash
     cd jineteapp-front
     npm install
     ~~~
   - Ejecuta en modo desarrollo:
     ~~~bash
     npm run start
     ~~~
   - Accede a la aplicación en `http://localhost:4200/`.

---

## Despliegue

Para **desplegar** la aplicación:

- **Backend**:  
  1. Generar el `.jar` o `.war` con `mvn package`.  
  2. Desplegarlo en un servidor (Tomcat, Docker, o plataforma Cloud).  
  3. Configurar las variables de entorno o `application.properties` de producción.

- **Frontend**:  
  1. Construir el proyecto de producción con `ng build --prod`.  
  2. Copiar la carpeta `dist/jineteapp-front` al servidor web (Nginx, Apache, etc.).  
  3. Configurar redirecciones para que Angular maneje las rutas.

---

## FAQ y Buenas Prácticas

1. **¿Por qué usar arquitectura hexagonal en el backend?**  
   Permite una separación clara entre la lógica de negocio y los detalles de infraestructura (bases de datos, frameworks web).  
2. **¿Cómo manejar la autenticación y seguridad?**  
   - **Backend**: Spring Security con JWT u OAuth2.  
   - **Frontend**: Interceptors para añadir tokens en cada petición, guards para rutas protegidas.  
3. **¿Se pueden integrar tests de forma conjunta?**  
   Cada repositorio (front y back) tiene su propia suite de pruebas. Se pueden orquestar tests end-to-end con herramientas como **Cypress** que engloben la pila completa.  
4. **¿Uso de Docker?**  
   Si se desea, se puede crear un Dockerfile para el backend y el frontend, simplificando el despliegue.

---

## Contacto y Contribuciones

- **Repositorio Backend**: [jineteapp_back](https://github.com/sasanchezramirez/jineteapp_back)  
- **Repositorio Frontend**: [jineteapp_front](https://github.com/sasanchezramirez/jineteapp-front)  
- Crea un **Issue** o un **Pull Request** en GitHub para reportar problemas o sugerencias.  
- ¡Gracias por tu interés en Jineteapp y por ayudar a mejorar esta herramienta de gestión financiera!

---