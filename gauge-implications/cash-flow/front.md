---
layout: page
title: "Front-End Cash Flow"
permalink: /gauge-implications/cash-flow/front/
---
  

Esta documentación describe la configuración, estructura y principales componentes del **Frontend** de la aplicación **Cashflow**. El objetivo es guiar a otros desarrolladores en la instalación, uso y mantenimiento del proyecto, así como brindar una visión general de la interfaz y sus pantallas.

## Tabla de Contenidos

1. [Introducción](#introducción)
2. [Requisitos Previos](#requisitos-previos)
3. [Estructura del Proyecto](#estructura-del-proyecto)
4. [Instalación y Configuración](#instalación-y-configuración)
5. [Scripts Disponibles](#scripts-disponibles)
6. [Flujo Principal de la Aplicación](#flujo-principal-de-la-aplicación)
7. [Servicios y Consumo de API](#servicios-y-consumo-de-api)
8. [Gestión de Estados](#gestión-de-estados)
9. [Estilos y Componentes UI](#estilos-y-componentes-ui)
10. [Capturas de Pantalla](#capturas-de-pantalla)
11. [Contribuciones](#contribuciones)

---

## Introducción

El **Frontend de Cashflow** provee la capa de interfaz de usuario para que los clientes gestionen sus finanzas personales. Algunas características clave son:

- **Registro e Inicio de Sesión**: Permite la autenticación de usuarios.
- **Creación y Consulta de Movimientos**: El usuario puede visualizar, filtrar y crear movimientos financieros.
- **Subida de Facturas/Recibos**: Integración con el módulo de OCR para reconocimiento automático de datos.
- **Reportes y Gráficas**: (En desarrollo) Se planea agregar representaciones visuales de los movimientos.

Este proyecto está desarrollado principalmente con **React**, **JavaScript/TypeScript** (según la configuración) y algunas librerías de UI para la maqueta.

---

## Requisitos Previos

Antes de clonar e iniciar el proyecto, se recomienda tener instalados:

1. **Node.js** (v14 o superior).
2. **npm** o **yarn** para la gestión de dependencias.
3. **Git** para clonar el repositorio.
4. **API Backend en funcionamiento** (o al menos conocer las rutas base de la API para configurarlas en el frontend).

---

## Estructura del Proyecto

La estructura principal del frontend (puede variar según la configuración):

~~~
cashflow-front
├── public
│   ├── index.html
│   └── ...
├── src
│   ├── assets
│   │   └── images
│   ├── components
│   │   └── ...
│   ├── context
│   │   └── ...
│   ├── hooks
│   │   └── ...
│   ├── pages
│   │   ├── Home
│   │   ├── Movements
│   │   ├── OCRUpload
│   │   └── ...
│   ├── services
│   │   └── api.js
│   ├── App.js (o App.tsx)
│   └── index.js (o index.tsx)
├── package.json
├── .env
└── ...
~~~

**Descripción breve**:

- **public/**: Contiene el HTML base y otros archivos estáticos.
- **src/**: Directorio principal de la aplicación.
  - **assets/**: Recursos estáticos como imágenes, íconos, etc.
  - **components/**: Componentes reutilizables de la interfaz.
  - **context/**: Contextos de React para la gestión de estados globales.
  - **hooks/**: Custom Hooks para lógica reutilizable.
  - **pages/**: Vistas principales (pantallas) de la app.
  - **services/**: Lógica de consumo de API y configuraciones de peticiones.
  - **App.js**: Punto de entrada principal de la aplicación, define rutas y estructura básica.
  - **index.js**: Renderizado de la aplicación en el DOM.

---

## Instalación y Configuración

1. **Clonar el repositorio**:
   ~~~bash
   git clone https://github.com/sasanchezramirez/cashflow-front.git
   cd cashflow-front
   ~~~

2. **Instalar dependencias**:
   ~~~bash
   npm install
   # o
   yarn
   ~~~

3. **Crear archivo `.env`** (opcional, según tu configuración) para almacenar las variables de entorno, por ejemplo:
   ~~~bash
   # Archivo .env
   
   REACT_APP_API_BASE_URL=http://localhost:3000
   ~~~

   - `REACT_APP_API_BASE_URL`: Indica la URL base del backend donde se consumen los endpoints.

---

## Scripts Disponibles

En el archivo `package.json` encontrarás varios scripts. Los más importantes son:

1. **npm run start**  
   Inicia la aplicación en modo de desarrollo. Abre `http://localhost:3000` (o el puerto configurado) en el navegador.

2. **npm run build**  
   Compila la aplicación para producción en la carpeta `build/`. Optimiza el bundle para un mejor rendimiento.

3. **npm run test**  
   Ejecuta las pruebas unitarias o de integración (si están configuradas).

4. **npm run lint**  
   Ejecuta un linter (ESLint) para verificar la calidad del código.

---

## Flujo Principal de la Aplicación

El flujo principal puede describirse así:

1. **Pantalla de Inicio de Sesión (Login)**: El usuario ingresa sus credenciales.
2. **Home**: Se presenta un resumen de los movimientos financieros y accesos directos a otras secciones.
3. **Movements**: Aquí se pueden listar, filtrar y crear nuevos movimientos. Se contacta al backend para obtener o guardar datos.
4. **OCR Upload**: Permite subir imágenes de facturas o recibos. Estas se envían al backend, que las procesa con el modelo OCR.
5. **Perfil/Configuración** (opcional): Se muestra y permite actualizar datos del usuario.

---

## Servicios y Consumo de API

En el directorio `src/services/` se gestiona la lógica de peticiones al backend:

- **api.js**:
  - Configura la **URL base** a partir de `process.env.REACT_APP_API_BASE_URL`.
  - Define métodos como `getMovements()`, `createMovement()`, `uploadInvoice()`, etc.
- Manejo de **tokens** de autenticación: Generalmente en un `interceptor` o en el contexto global, se adjunta el token en las cabeceras HTTP.

Ejemplo de una petición con fetch (o axios):
~~~js
export const getMovements = async () => {
  const response = await fetch(`${process.env.REACT_APP_API_BASE_URL}/movements`, {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${localStorage.getItem('token')}`
    }
  });
  return await response.json();
};
~~~

---

## Gestión de Estados

- **Context API** (o Redux, dependiendo de la configuración) para manejar el estado global:
  - Datos de autenticación del usuario.
  - Lista de movimientos financieros.
  - Mensajes de error o retroalimentación al usuario (notificaciones).

Se busca centralizar la información para que sea accesible en las distintas secciones de la aplicación sin necesidad de prop drilling excesivo.

---

## Estilos y Componentes UI

- Uso de librerías como **Material UI**, **Styled Components**, **Tailwind CSS** o **Bootstrap** (según configuración actual).
- Componente `<Header>` y `<Footer>` para la estructura base de la interfaz.
- `<Button>`, `<Input>`, `<Modal>`, etc. como componentes reutilizables de UI.

---

## Capturas de Pantalla

En esta sección se muestran pantallas clave de la aplicación con un enlace a la imagen. *(Coloca los assets en la carpeta indicada o un repositorio de imágenes)*

1. **Home (dark)**  
    ![Desktop View](/assets/img/docs/cash-flow/cash-flow-dark.png){: width="589" height="250" }
    _Home dark_
2. **Home (light)**  
    ![Desktop View](/assets/img/docs/cash-flow/cash-flow-light.png){: width="589" height="250" }
    _Home light_
3. **Añadir gastos**  
    ![Desktop View](/assets/img/docs/cash-flow/cash-flow-add-expense.png){: width="589" height="250" }
    _Añadir gastos_
---

## Contribuciones

Si deseas contribuir al **Frontend de Cashflow**:

1. Haz un **fork** del repositorio.
2. Crea una rama para tu funcionalidad:  
   ~~~bash
   git checkout -b feature/nueva-funcionalidad
   ~~~
3. Realiza los cambios, verifica que todo funcione, añade pruebas o actualiza la documentación.
4. Haz un commit de tus cambios:  
   ~~~bash
   git commit -m "Implementa nueva funcionalidad X"
   ~~~
5. Envía un **pull request** al repositorio principal, describiendo tus cambios y la razón de estos.

---

**Gracias por leer!** Para más información sobre la aplicación o reporte de errores, visita [GitHub - cashflow-front](https://github.com/sasanchezramirez/cashflow-front) o crea un issue en el repositorio.

