---
layout: page
title: "Front-End Jineteapp"
permalink: /gauge-implications/jineteapp/front/
---
  

Este documento detalla la **arquitectura**, la **estructura** de ficheros, la **configuración** y las **funcionalidades** del frontend de **Jineteapp**.

## Tabla de Contenidos

1. [Introducción](#introducción)
2. [Tecnologías y Dependencias](#tecnologías-y-dependencias)
3. [Estructura del Proyecto](#estructura-del-proyecto)
4. [Módulos y Componentes](#módulos-y-componentes)
5. [Servicios](#servicios)
6. [Rutas (Routing)](#rutas-routing)
7. [Configuración de Entorno](#configuración-de-entorno)
8. [Ejecución y Scripts](#ejecución-y-scripts)
9. [Testing](#testing)
10. [Despliegue](#despliegue)
11. [FAQ y Buenas Prácticas](#faq-y-buenas-prácticas)
12. [Contacto y Contribuciones](#contacto-y-contribuciones)

---

## Introducción

El **frontend** de Jineteapp está construido con **Angular**, y se conecta al backend (Java Spring Boot) a través de peticiones HTTP/REST. Su objetivo principal es ofrecer una interfaz de usuario intuitiva para:

- Registrar, consultar y editar **transacciones financieras**.
- Administrar **cuentas**, **categorías** y **usuarios**.
- Mostrar de forma interactiva y amigable los datos almacenados en el **backend**.

Este proyecto se encuentra en [Github: jineteapp_front](https://github.com/sasanchezramirez/jineteapp-front).

---

## Tecnologías y Dependencias

- **Angular 15+** (versión aproximada, comprobar en el `package.json`)
- **TypeScript**  
- **RxJS**  
- **Bootstrap / Material** (opcional, si se ha utilizado algún framework de UI)
- **Angular CLI** para la generación y el despliegue
- **Libraries** adicionales (ver `package.json`):
  - `@angular/router` para el enrutado
  - `@angular/forms` para formularios
  - Otras según necesidades (chart.js, moment.js, etc.)

---

## Estructura del Proyecto

La estructura base suele lucir así (verificar en el repositorio real si coincide):

~~~plaintext
jineteapp-front
 ┣ src
 ┃ ┣ app
 ┃ ┃ ┣ core
 ┃ ┃ ┃ ┗ services
 ┃ ┃ ┣ features
 ┃ ┃ ┃ ┣ cuentas
 ┃ ┃ ┃ ┣ transacciones
 ┃ ┃ ┃ ┣ categorias
 ┃ ┃ ┃ ┗ usuarios
 ┃ ┃ ┣ shared
 ┃ ┃ ┃ ┣ components
 ┃ ┃ ┃ ┣ directives
 ┃ ┃ ┃ ┗ pipes
 ┃ ┃ ┗ app-routing.module.ts
 ┃ ┣ assets
 ┃ ┣ environments
 ┃ ┃ ┣ environment.ts
 ┃ ┃ ┣ environment.prod.ts
 ┃ ┗ index.html
 ┣ package.json
 ┣ angular.json
 ┗ README.md
~~~

**Carpetas principales**:

- **`app/core`**: Contiene servicios centrales (p.ej. servicio de autenticación, interceptores, guardias).
- **`app/features`**: Cada “feature” o funcionalidad separada de la aplicación (cuentas, transacciones, categorías, etc.).
- **`app/shared`**: Componentes, directivas, pipes y utilidades reutilizables.
- **`environments`**: Configuraciones separadas para distintos entornos (desarrollo, producción, etc.).

---

## Módulos y Componentes

En Angular, la aplicación se compone de **módulos** y **componentes**:

- **Módulos (NgModules)**: Agrupan componentes, servicios y otros recursos. Ejemplo:  
  - `AppModule` (módulo principal de la aplicación)  
  - `TransaccionesModule` (módulo para manejar la lógica de transacciones)  
- **Componentes**: Partes visuales y lógicas de la UI (formularios, listados, detalles, etc.).

Ejemplo de un componente (simplificado):

~~~ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-transaccion-list',
  templateUrl: './transaccion-list.component.html',
  styleUrls: ['./transaccion-list.component.scss']
})
export class TransaccionListComponent implements OnInit {

  transacciones: any[] = [];

  constructor() { }

  ngOnInit(): void {
    // Lógica de carga de transacciones
  }
}
~~~

---

## Servicios

En `app/core/services` o dentro de cada módulo `features`, encontrarás servicios para interactuar con la API y encapsular la lógica de negocio del lado del cliente. Ejemplo:

~~~ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class TransaccionService {

  private baseUrl = 'http://localhost:8080/api/transacciones';

  constructor(private http: HttpClient) { }

  listarTransacciones(): Observable<any[]> {
    return this.http.get<any[]>(this.baseUrl);
  }

  registrarTransaccion(transaccion: any): Observable<any> {
    return this.http.post<any>(this.baseUrl, transaccion);
  }
}
~~~

- Se usa **HttpClient** (Angular) para hacer peticiones al backend.  
- Se define la URL base (`baseUrl`), y métodos específicos (`listarTransacciones`, `registrarTransaccion`, etc.).

---

## Rutas (Routing)

Para gestionar la **navegación** dentro de la aplicación, Angular utiliza el módulo `@angular/router`. Normalmente se configura un `app-routing.module.ts` en la raíz del proyecto:

~~~ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { TransaccionListComponent } from './features/transacciones/transaccion-list/transaccion-list.component';

const routes: Routes = [
  { path: '', redirectTo: 'transacciones', pathMatch: 'full' },
  { path: 'transacciones', component: TransaccionListComponent },
  // Otras rutas para cuentas, categorías, etc.
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
~~~

- Cada ruta se asocia a un **componente**.  
- Se pueden anidar rutas y usar **lazy loading** para mejorar el rendimiento.

---

## Configuración de Entorno

En la carpeta `src/environments`, por convención, se encuentran ficheros como:

- `environment.ts` (desarrollo)  
- `environment.prod.ts` (producción)

~~~ts
// environment.ts
export const environment = {
  production: false,
  apiBaseUrl: 'http://localhost:8080/api'
};
~~~

~~~ts
// environment.prod.ts
export const environment = {
  production: true,
  apiBaseUrl: 'https://mi-dominio.com/api'
};
~~~

Estos ficheros permiten cambiar la **URL base** de la API y otras variables dependiendo del entorno en que se ejecute la app.

---

## Ejecución y Scripts

Los comandos más comunes están definidos en el `package.json`. Por ejemplo:

~~~json
{
  "scripts": {
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  }
}
~~~

1. **Instalar dependencias**:
   ~~~bash
   npm install
   ~~~

2. **Levantar la aplicación en modo desarrollo**:
   ~~~bash
   npm run start
   # o simplemente ng serve (si Angular CLI está instalada globalmente)
   ~~~

3. **Build de producción**:
   ~~~bash
   npm run build -- --prod
   ~~~
   Genera la carpeta `dist/` con los archivos optimizados.

---

## Testing

Angular ofrece varias opciones de pruebas:

- **Unit Tests** (Karma + Jasmine): Cada componente/servicio debería contar con su propio archivo de prueba `*.spec.ts`.
- **End-to-End Tests** (Protractor o Cypress): Se centran en escenarios completos de la aplicación.

Ejemplo de prueba unitaria básica (simplificado):

~~~ts
import { TestBed } from '@angular/core/testing';
import { TransaccionService } from './transaccion.service';
import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';

describe('TransaccionService', () => {
  let service: TransaccionService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [TransaccionService]
    });
    service = TestBed.inject(TransaccionService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  it('should list transactions', () => {
    const dummyTransacciones = [{ id: 1, monto: 100 }];
    service.listarTransacciones().subscribe(data => {
      expect(data).toEqual(dummyTransacciones);
    });
    const req = httpMock.expectOne('http://localhost:8080/api/transacciones');
    expect(req.request.method).toBe('GET');
    req.flush(dummyTransacciones);
  });

  afterEach(() => {
    httpMock.verify();
  });
});
~~~

---

## Despliegue

El **despliegue** de la aplicación Angular se realiza generando la carpeta `dist` con `ng build --prod`. Los archivos resultantes se pueden servir con cualquier servidor web estático (Nginx, Apache, etc.) o integrarse con plataformas de hosting como Firebase, Netlify o GitHub Pages.

Ejemplo de despliegue con Nginx (simplificado):

~~~plaintext
server {
    listen 80;
    server_name mi-dominio.com;
    root /var/www/jineteapp;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
~~~

---

## FAQ y Buenas Prácticas

1. **¿Cómo integrar los entornos de desarrollo y producción?**  
   Ajustar la propiedad `apiBaseUrl` en cada archivo de entorno (`environment.ts`, `environment.prod.ts`).

2. **¿Dónde ubicar servicios compartidos?**  
   Preferiblemente en la carpeta `core/services` o `app/shared/services`. Mantén la consistencia.

3. **¿Cómo manejar la autenticación?**  
   - Usar **Interceptors** para añadir cabeceras (tokens JWT) a cada petición.  
   - Implementar **Guards** para restringir acceso a rutas.

4. **¿Cómo asegurar el rendimiento de la app?**  
   - Lazy loading de módulos.  
   - Uso adecuado de la detección de cambios (OnPush strategy) y trackBy en *ngFor.  

5. **Logs** y **alertas**:
   - Evitar logs excesivos en producción (console.log).
   - Usar servicios especializados para notificaciones y errores.

---

## Contacto y Contribuciones

- **Repositorio oficial Frontend**: [jineteapp-front](https://github.com/sasanchezramirez/jineteapp-front)  
- **Repositorio oficial Backend**: [jineteapp_back](https://github.com/sasanchezramirez/jineteapp_back)  
- Para sugerencias, errores o contribuciones, crea un **Issue** o un **Pull Request** en el repositorio de GitHub.  
- ¡Gracias por tu interés en el proyecto Jineteapp!

---

