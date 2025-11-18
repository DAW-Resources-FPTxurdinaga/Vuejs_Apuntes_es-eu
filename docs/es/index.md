# 🚀 Curso de Vue.js 3

<div align="center">
  <img src="https://vuejs.org/images/logo.png" alt="Vue.js Logo" width="200">
</div>

**Bienvenido/a al curso de Vue 3** - El framework progresivo de JavaScript para construir interfaces de usuario modernas y reactivas.

## 📚 Estructura del Curso

1. **Conceptos Básicos** - Fundamentos esenciales de Vue 3
2. **Fundamentos** - Características principales del framework
3. **Componentes** - Construcción y comunicación
4. **Peticiones HTTP** - Consumo de APIs
5. **Vue Router** - Navegación en SPA
6. **Pinia** - Gestión de estado
7. **Proyecto Práctico** - Aplicación completa

## 🎯 Objetivos de Aprendizaje

- Dominar los **conceptos fundamentales** de Vue 3
- Desarrollar **componentes reutilizables**
- Implementar **gestión de estado** con Pinia
- Crear **aplicaciones de una sola página** con Vue Router
- Consumir **APIs REST** de forma eficiente
- Aplicar **patrones de diseño** profesionales

## 🛠️ Requisitos Previos

- Conocimientos sólidos de **HTML5 y CSS3**
- Experiencia con **JavaScript (ES6+)**
- Conceptos básicos de **programación orientada a objetos**
- Familiaridad con **herramientas de desarrollo** (Node.js, npm/yarn)

## 📖 Contenido Destacado

- **Vue 3 Composition API** - La forma moderna de escribir componentes
- **Reactividad** - Sistema reactivo potente y eficiente
- **Single File Components** - Todo en un solo archivo (.vue)
- **Herramientas de desarrollo** - Vue DevTools, Vite, etc.

## 🚀 Comenzar

Para comenzar con Vue 3, sigue estos pasos:

1. Navega a la sección de [Conceptos Básicos](./1-conceptos-basicos/1.1-introduccion.md) para una introducción completa a Vue 3.
2. Asegúrate de tener instalado [Node.js](https://nodejs.org/) (versión 16 o superior) y [pnpm](https://pnpm.io/installation) o [yarn](https://yarnpkg.com/getting-started/install).
3. Crea un nuevo proyecto Vue con uno de estos comandos:
    - **npm:**
   ```
   npm create vue@latest mi-proyecto-vue
   ```
    - **yarn:**
   ```
   yarn create vue mi-proyecto-vue
   ```
    - **pnpm:**
   ```
   pnpm create vue mi-proyecto-vue
   ```
4. Navega al directorio del proyecto:
   ```
   cd mi-proyecto-vue
   ```
5. Instala las dependencias:
   ```
   npm install  # o yarn install / pnpm install
   ```
6. Inicia el servidor de desarrollo:
   ```bash
   npm run dev  # o yarn dev / pnpm dev
   ```
7. Abre tu navegador en `http://localhost:5173` para ver tu aplicación en acción.

---

### Recursos Adicionales

### 📚 Documentación y Recursos

- **📘 [Documentación Oficial](https://vuejs.org/guide/introduction.html)**  
  La guía completa de Vue 3

- **💻 [Ejemplos de Código](https://github.com/vuejs/examples)**  
  Proyectos de ejemplo para aprender

- **💬 [Comunidad Vue](https://chat.vuejs.org/)**  
  Obtén ayuda de la comunidad

- **▶️ [Vue Mastery](https://www.vuemastery.com/)**  
  Cursos en video gratuitos

## Nivel del Curso

Este material está diseñado para estudiantes de **Desarrollo de Aplicaciones Web** con conocimientos previos en:

- HTML5 y CSS3
- JavaScript (ES6+)
- Conceptos básicos de programación orientada a objetos
- Experiencia previa con algún framework JS es útil pero no requerida

## Estructura del Curso

### 1. Fundamentos de Vue 3
- Reactividad y el sistema de instancias
- Sintaxis de plantillas y directivas básicas
- Propiedades computadas y watchers
- Clases y estilos dinámicos

### 2. Componentes y Composición
- Creación y comunicación entre componentes
- Props y eventos personalizados
- Slots y componentes dinámicos
- Composition API avanzada

### 3. Estado y Gestión de Datos
- Gestión de estado con Pinia
- Patrones de gestión de estado
- Consumo de APIs REST/GraphQL
- Manejo de errores y carga de datos

### 4. Proyecto Final
- Estructura de una aplicación Vue 3
- Enrutamiento avanzado
- Autenticación y autorización
- Despliegue y optimización

## Requisitos Técnicos

Para seguir este curso necesitarás:

- Node.js 16+ y npm/yarn
- Un editor de código (VS Code recomendado)
- Vue DevTools (extensión del navegador)
- Conocimientos básicos de línea de comandos

## Cómo Empezar

1. Instala [Node.js](https://nodejs.org/) (incluye npm)
2. Verifica la instalación:
   ```bash
   node --version
   npm --version
   ```
3. Crea un proyecto Vue 3 con Vite:
   ```bash
   npm create vue@latest mi-proyecto-vue
   cd mi-proyecto-vue
   npm install
   npm run dev
   ```

## Recursos Adicionales

- [Documentación Oficial de Vue 3](https://vuejs.org/)
- [Vue Mastery (cursos en video)](https://www.vuemastery.com/)
- [Vue.js en GitHub](https://github.com/vuejs/core)
- [Comunidad Vue en Discord](https://vue.land/)
- [Awesome Vue (recursos)](https://github.com/vuejs/awesome-vue)

## Sobre el Curso

Este material ha sido desarrollado para el módulo de **Desarrollo de Aplicaciones Web** con el objetivo de proporcionar una guía completa y práctica para el desarrollo de aplicaciones modernas con Vue 3.

### Metodología

- Aprendizaje basado en proyectos
- Ejemplos prácticos y código real
- Ejercicios progresivos
- Buenas prácticas de la industria

### Evaluación

- Ejercicios prácticos semanales
- Proyecto final integrador
- Participación en foros y actividades

---

<div class="cta-buttons">
  <a href="/2-fundamentos/2.0-introduccion" class="button primary">Comenzar Curso</a>
  <a href="/proyecto/ejercicios" class="button secondary">Ver Ejercicios</a>
</div>

<style>
.cta-buttons {
  margin: 2rem 0;
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}

.cta-buttons .button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0.75rem 1.5rem;
  border-radius: 0.5rem;
  font-weight: 600;
  text-decoration: none;
  transition: all 0.2s ease;
}

.cta-buttons .primary {
  background-color: #42b983;
  color: white;
  border: 2px solid #42b983;
}

.cta-buttons .primary:hover {
  background-color: #3aa876;
  border-color: #3aa876;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(66, 185, 131, 0.3);
}

.cta-buttons .secondary {
  background-color: transparent;
  color: #2c3e50;
  border: 2px solid #2c3e50;
}

.cta-buttons .secondary:hover {
  background-color: #f8f9fa;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(44, 62, 80, 0.1);
}

@media (max-width: 768px) {
  .cta-buttons {
    flex-direction: column;
  }
  
  .cta-buttons .button {
    width: 100%;
  }
}
</style>