---
layout: page
title: "Front-End - Proyecto A"
permalink: /gauge-implications/poop-manager/front/
---
  
# ChaplinPoopManager - Frontend

## Contenido
1. [Descripción](#descripción)
2. [Características Principales](#características-principales)
3. [Tecnologías Utilizadas](#tecnologías-utilizadas)
4. [Componentes Principales](#componentes-principales)
5. [Instalación](#instalación)
6. [Estructura del Proyecto](#estructura-del-proyecto)
7. [Flujo de Trabajo](#flujo-de-trabajo)
8. [Visualizaciones](#visualizaciones)
9. [Notas Adicionales](#notas-adicionales)

## Descripción
La capa de usuario del Poop Manager está desarrollada en Angular, en donde se permite gestionar y monitorear las visitas y necesidades de tu mascota. La aplicación proporciona estadísticas detalladas y visualizaciones en tiempo real para ayudarte a mantener un registro preciso de las actividades de tu mascota.

##  Características Principales
- Dashboard interactivo con estadísticas diarias
- Visualización de datos mediante mapa de calor (heatmap)
- Registro de limpiezas
- Monitoreo de visitas diarias
- Estadísticas en tiempo real

##  Tecnologías Utilizadas
- Angular (versión más reciente)
- TypeScript
- SCSS para estilos
- ngx-charts para visualizaciones

## Componentes Principales

### HomeComponent
El componente principal que muestra el dashboard con las siguientes características:

#### Variables Principales:
- `todayVisits`: Número de visitas del día
- `todayDate`: Fecha actual
- `dailyVisits`: Promedio de visitas diarias
- `poopsSinceLastClean`: Contador desde última limpieza
- `dateLastClean`: Fecha y hora de la última limpieza

#### Funcionalidades:
- `newClean()`: Registra una nueva limpieza
- `onChartRefresh()`: Actualiza los datos del gráfico
- `heatmapData`: Matriz de datos para el mapa de calor semanal

## 🔧 Instalación

1. Clona el repositorio:
```bash
git clone [https://github.com/sasanchezramirez/ChaplinPoopManager-Front.git]
```

2. Instala las dependencias:
```bash
npm install
```

3. Inicia el servidor de desarrollo:
```bash
ng serve
```

4. Abre tu navegador en `http://localhost:4200`

## Estructura del Proyecto
```
front/
├── src/
│   ├── app/
│   │   ├── features/
│   │   │   └── home/
│   │   │       ├── home.component.ts
│   │   │       ├── home.component.html
│   │   │       └── home.component.scss
│   │   └── ...
│   ├── assets/
│   └── ...
└── ...
```

##  Flujo de Trabajo
1. El dashboard muestra estadísticas en tiempo real
2. Los datos del mapa de calor se actualizan automáticamente
3. Se puede registrar una nueva limpieza mediante el botón correspondiente
4. Las estadísticas se actualizan automáticamente después de cada acción

##  Visualizaciones
La aplicación incluye un mapa de calor que muestra la actividad por:
- Días de la semana
- Horas del día
- Intensidad de uso

![Desktop View](/assets/img/docs/poop-manager/desktop.png){: width="972" height="589" }
_Mock del gestor de mascotas_


##  Notas Adicionales
- La aplicación está diseñada para ser responsive
- Se recomienda mantener actualizadas las dependencias
