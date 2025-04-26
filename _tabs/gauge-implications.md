---
title: "Tech Projects"
icon: fas fa-cogs
order: 2
---





> «Sólo donde hay vida hay también voluntad: pero no voluntad de vida, sino -así te lo enseño yo- ¡voluntad de poder!»
>
> — Nietzsche, F.: Así habló Zaratustra. De la superación de mí mismo, p. 171-172.

# *Gauge Implications*

Crear es una expresión más de mi existencia. Mi voluntad creadora es presentada como *Gauge Implications*, extendiendo o heredando[^1] el concepto de *[Gauge Life]({{site.baseurl}}/about)*, en donde esta "implicación" es lo que me da la libertad de **aceptar** esta todas las ideas y deseos de aprender haciendo.

Cada uno de estos proyectos surgieron desde un profundo aburrimiento, desatando un deseo de aprender algo nuevo. ¿Qué mejor forma de aprender que creando?

<div class="projects-showcase">
  <div class="project-card">
    <div class="project-header">
      <h3>Poop Manager</h3>
      <span class="project-status">Producción</span>
    </div>
    
    <div class="project-tech">
      <span class="tech-tag">Angular</span>
      <span class="tech-tag">FastAPI</span>
      <span class="tech-tag">Postgress</span>
    </div>

    <p class="project-description">
      Gestión inteligente de datos para el cuidado de mascotas. Una solución moderna que permite el seguimiento detallado del comportamiento y salud de tus compañeros peludos.
    </p>

    <div class="project-metrics">
      <div class="metric">
        <span class="number">2023</span>
        <span class="label">Año</span>
      </div>
      <div class="metric">
        <span class="number">v1.0</span>
        <span class="label">Versión</span>
      </div>
    </div>

    <a href="{{ site.baseurl }}/gauge-implications/poop-manager" class="project-link">
      Explorar Proyecto →
    </a>
  </div>

  <div class="project-card">
    <div class="project-header">
      <h3>JineteApp</h3>
      <span class="project-status">Desarrollo</span>
    </div>
    
    <div class="project-tech">
      <span class="tech-tag">Flutter</span>
      <span class="tech-tag">Firebase</span>
      <span class="tech-tag">BLoC</span>
    </div>

    <p class="project-description">
      Control y seguimiento para entrenamiento ecuestre. Una aplicación móvil que revoluciona la forma en que se gestionan los entrenamientos y competencias ecuestres.
    </p>

    <div class="project-metrics">
      <div class="metric">
        <span class="number">2024</span>
        <span class="label">Año</span>
      </div>
      <div class="metric">
        <span class="number">Beta</span>
        <span class="label">Estado</span>
      </div>
    </div>

    <a href="{{ site.baseurl }}/gauge-implications/jineteapp" class="project-link">
      Explorar Proyecto →
    </a>
  </div>

  <div class="project-card">
    <div class="project-header">
      <h3>Time Calculator</h3>
      <span class="project-status">Producción</span>
    </div>
    
    <div class="project-tech">
      <span class="tech-tag">Angular</span>
      <span class="tech-tag">FastAPI</span>
    </div>

    <p class="project-description">
      Esto es fue simplemente un juguete para calmar mi angustia frente al paso del tiempo.
    </p>

    <div class="project-metrics">
      <div class="metric">
        <span class="number">2025</span>
        <span class="label">Año</span>
      </div>
      <div class="metric">
        <span class="number">v1.0</span>
        <span class="label">Versión</span>
      </div>
    </div>

    <a href="{{ site.baseurl }}/gauge-implications/time-calculator" class="project-link">
      Explorar Proyecto →
    </a>
  </div>

  <div class="project-card">
    <div class="project-header">
      <h3>Cash Flow</h3>
      <span class="project-status">MVP</span>
    </div>
    
    <div class="project-tech">
      <span class="tech-tag">Angular</span>
      <span class="tech-tag">FastAPI</span>
      <span class="tech-tag">Postgress</span>
    </div>

    <p class="project-description">
      Análisis y gestión de flujo de efectivo personal. Una herramienta intuitiva para visualizar y controlar tus finanzas de manera efectiva.
    </p>

    <div class="project-metrics">
      <div class="metric">
        <span class="number">2023</span>
        <span class="label">Año</span>
      </div>
      <div class="metric">
        <span class="number">MVP</span>
        <span class="label">Fase</span>
      </div>
    </div>

    <a href="{{ site.baseurl }}/gauge-implications/cash-flow" class="project-link">
      Explorar Proyecto →
    </a>
  </div>

  <div class="project-card">
    <div class="project-header">
      <h3>2.5 Vision</h3>
      <span class="project-status">Experimental</span>
    </div>
    
    <div class="project-tech">
      <span class="tech-tag">Angular</span>
      <span class="tech-tag">FastAPI</span>
      <span class="tech-tag">Postgress</span>
    </div>

    <p class="project-description">
      Exploración de visualización en 2.5 dimensiones. Un experimento que busca nuevas formas de representar datos en un espacio entre 2D y 3D.
    </p>

    <div class="project-metrics">
      <div class="metric">
        <span class="number">2024</span>
        <span class="label">Año</span>
      </div>
      <div class="metric">
        <span class="number">Training</span>
        <span class="label">Fase</span>
      </div>
    </div>

    <a href="{{ site.baseurl }}/gauge-implications/25-vision" class="project-link">
      Explorar Proyecto →
    </a>
  </div>
</div>

<style>
.projects-showcase {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  padding: 1rem 0;
}

.project-card {
  background: var(--card-bg);
  border-radius: 15px;
  padding: 1.5rem;
  border: 1px solid var(--card-border-color);
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.project-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 15px rgba(0, 0, 0, 0.1);
}

.project-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.5rem;
}

.project-header h3 {
  margin: 0;
  color: var(--heading-color);
  font-size: 1.5rem;
}

.project-status {
  font-size: 0.8rem;
  padding: 0.25rem 0.75rem;
  border-radius: 15px;
  background: var(--link-color);
  color: var(--card-bg);
}

.project-tech {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
}

.tech-tag {
  font-size: 0.8rem;
  padding: 0.2rem 0.6rem;
  border-radius: 12px;
  background: var(--code-bg);
  color: var(--text-color);
}

.project-description {
  color: var(--text-muted-color);
  font-size: 0.95rem;
  line-height: 1.5;
  margin: 0.5rem 0;
}

.project-metrics {
  display: flex;
  gap: 2rem;
  padding: 1rem 0;
  border-top: 1px solid var(--border-color);
  border-bottom: 1px solid var(--border-color);
}

.metric {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.metric .number {
  font-size: 1.2rem;
  font-weight: bold;
  color: var(--heading-color);
}

.metric .label {
  font-size: 0.8rem;
  color: var(--text-muted-color);
}

.project-link {
  display: inline-block;
  text-decoration: none;
  color: var(--link-color);
  font-weight: 500;
  margin-top: auto;
  transition: transform 0.2s ease;
}

.project-link:hover {
  transform: translateX(5px);
}

@media (max-width: 768px) {
  .projects-showcase {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }

  .project-metrics {
    gap: 1rem;
  }
}
</style>

[^1]: Hablando desde la relación de dependiancias de la programación orientada a objetos.
