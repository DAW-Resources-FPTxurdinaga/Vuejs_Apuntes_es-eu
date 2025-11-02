# 2.3 Condicionales y Bucles

## Directiva v-if, v-else-if, v-else

Las directivas condicionales permiten renderizar elementos basados en una condición.

### Uso básico:

```html
<div v-if="puntuacion >= 90">
  ¡Excelente!
</div>
<div v-else-if="puntuacion >= 70">
  Bien hecho
</div>
<div v-else>
  Sigue practicando
</div>
```

### v-if vs v-show

- `v-if`: Renderiza condicionalmente el elemento (lo añade/elimina del DOM)
- `v-show`: Siempre renderiza el elemento, pero usa CSS para mostrarlo/ocultarlo

```html
<div v-if="mostrar">Usando v-if</div>
<div v-show="mostrar">Usando v-show</div>
```

## Directiva v-for

Permite iterar sobre arrays u objetos.

### Iterando sobre arrays:

```html
<ul>
  <li v-for="(tarea, index) in tareas" :key="tarea.id">
    {{ index + 1 }}. {{ tarea.texto }}
  </li>
</ul>
```

### Iterando sobre objetos:

```html
<ul>
  <li v-for="(valor, clave, index) in usuario" :key="clave">
    {{ index + 1 }}. {{ clave }}: {{ valor }}
  </li>
</ul>
```

## Ejercicio Práctico: Lista de Tareas Avanzada

```vue
<template>
  <div class="tareas-container">
    <h1>Lista de Tareas</h1>
    
    <!-- Formulario para agregar tareas -->
    <div class="form-group">
      <input
        type="text"
        v-model="nuevaTarea"
        @keyup.enter="agregarTarea"
        placeholder="Escribe una nueva tarea..."
        class="tarea-input"
      >
      <button @click="agregarTarea" class="btn btn-agregar">
        Agregar
      </button>
    </div>
    
    <!-- Filtros -->
    <div class="filtros">
      <button 
        v-for="filtro in filtros" 
        :key="filtro"
        @click="filtroActual = filtro"
        :class="['btn-filtro', { 'activo': filtroActual === filtro }]"
      >
        {{ filtro }}
      </button>
    </div>
    
    <!-- Lista de tareas -->
    <ul class="lista-tareas">
      <li 
        v-for="tarea in tareasFiltradas" 
        :key="tarea.id"
        class="tarea-item"
        :class="{ 'completada': tarea.completada }"
      >
        <input 
          type="checkbox" 
          v-model="tarea.completada"
          @change="actualizarTarea(tarea)"
          class="tarea-checkbox"
        >
        
        <span 
          v-if="!tarea.editando" 
          @dblclick="editarTarea(tarea)"
          class="tarea-texto"
        >
          {{ tarea.texto }}
        </span>
        
        <input
          v-else
          v-model="tarea.texto"
          @blur="guardarEdicion(tarea)"
          @keyup.enter="guardarEdicion(tarea)"
          @keyup.esc="cancelarEdicion(tarea)"
          v-focus
          class="input-edicion"
        >
        
        <div class="acciones-tarea">
          <button 
            @click="editarTarea(tarea)" 
            class="btn btn-editar"
            v-if="!tarea.editando"
          >
            Editar
          </button>
          <button 
            @click="eliminarTarea(tarea.id)" 
            class="btn btn-eliminar"
          >
            Eliminar
          </button>
        </div>
      </li>
      
      <li v-if="tareasFiltradas.length === 0" class="sin-tareas">
        No hay tareas para mostrar
      </li>
    </ul>
    
    <!-- Contadores -->
    <div class="contadores">
      <span>Total: {{ tareas.length }}</span>
      <span>Completadas: {{ tareasCompletadas }}</span>
      <span>Pendientes: {{ tareasPendientes }}</span>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

// Datos reactivos
const nuevaTarea = ref('');
const tareas = ref([]);
const tareasAntesDeEditar = {};
const filtros = ['Todas', 'Pendientes', 'Completadas'];
const filtroActual = ref('Todas');

// Computadas
const tareasCompletadas = computed(() => 
  tareas.value.filter(t => t.completada).length
);

const tareasPendientes = computed(() => 
  tareas.value.length - tareasCompletadas.value
);

const tareasFiltradas = computed(() => {
  switch (filtroActual.value) {
    case 'Pendientes':
      return tareas.value.filter(t => !t.completada);
    case 'Completadas':
      return tareas.value.filter(t => t.completada);
    default:
      return tareas.value;
  }
});

// Métodos
function agregarTarea() {
  if (nuevaTarea.value.trim() === '') return;
  
  tareas.value.push({
    id: Date.now(),
    texto: nuevaTarea.value.trim(),
    completada: false,
    editando: false
  });
  
  nuevaTarea.value = '';
  guardarTareas();
}

function eliminarTarea(id) {
  if (confirm('¿Estás seguro de que quieres eliminar esta tarea?')) {
    tareas.value = tareas.value.filter(t => t.id !== id);
    guardarTareas();
  }
}

function editarTarea(tarea) {
  tareasAntesDeEditar[tarea.id] = tarea.texto;
  tarea.editando = true;
}

function guardarEdicion(tarea) {
  if (tarea.texto.trim() === '') {
    tarea.texto = tareasAntesDeEditar[tarea.id] || '';
  }
  tarea.editando = false;
  guardarTareas();
}

function cancelarEdicion(tarea) {
  tarea.texto = tareasAntesDeEditar[tarea.id] || tarea.texto;
  tarea.editando = false;
}

function actualizarTarea(tarea) {
  guardarTareas();
}

function guardarTareas() {
  localStorage.setItem('tareas', JSON.stringify(tareas.value));
}

// Directiva personalizada para enfocar el input de edición
const vFocus = {
  mounted: (el) => el.focus()
};

// Cargar tareas guardadas al iniciar
onMounted(() => {
  const tareasGuardadas = localStorage.getItem('tareas');
  if (tareasGuardadas) {
    tareas.value = JSON.parse(tareasGuardadas);
  }
});
</script>

<style scoped>
/* Estilos para el contenedor principal */
.tareas-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

/* Estilos para el formulario */
.form-group {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.tarea-input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

/* Estilos para los botones */
.btn {
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.btn-agregar {
  background-color: #4CAF50;
  color: white;
}

.btn-agregar:hover {
  background-color: #45a049;
}

/* Estilos para los filtros */
.filtros {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.btn-filtro {
  background-color: #f1f1f1;
  padding: 5px 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  cursor: pointer;
}

.btn-filtro.activo {
  background-color: #4CAF50;
  color: white;
  border-color: #45a049;
}

/* Estilos para la lista de tareas */
.lista-tareas {
  list-style: none;
  padding: 0;
}

.tarea-item {
  display: flex;
  align-items: center;
  padding: 10px;
  margin-bottom: 8px;
  background-color: #f9f9f9;
  border-radius: 4px;
  transition: background-color 0.3s;
}

.tarea-item:hover {
  background-color: #f1f1f1;
}

.tarea-item.completada {
  opacity: 0.7;
}

.tarea-checkbox {
  margin-right: 10px;
}

.tarea-texto {
  flex: 1;
  margin-right: 10px;
  cursor: pointer;
}

.tarea-item.completada .tarea-texto {
  text-decoration: line-through;
  color: #888;
}

.acciones-tarea {
  display: flex;
  gap: 5px;
}

.btn-editar {
  background-color: #2196F3;
  color: white;
}

.btn-eliminar {
  background-color: #f44336;
  color: white;
}

.btn-editar:hover, .btn-eliminar:hover {
  opacity: 0.9;
}

/* Estilos para el input de edición */
.input-edicion {
  flex: 1;
  padding: 5px;
  margin-left: 10px;
  border: 1px solid #2196F3;
  border-radius: 4px;
}

/* Estilos para los contadores */
.contadores {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
  padding: 10px;
  background-color: #f5f5f5;
  border-radius: 4px;
}

/* Estilos para el mensaje de sin tareas */
.sin-tareas {
  text-align: center;
  color: #888;
  font-style: italic;
  margin-top: 20px;
}
</style>
```

## Explicación del Código

1. **Estructura del Componente**:
   - Usamos la sintaxis de `<script setup>` para una mejor organización del código.
   - El componente está dividido en tres secciones principales: template, script y estilos.

2. **Funcionalidades Clave**:
   - **Agregar tareas**: Con validación de entrada vacía.
   - **Marcar como completada**: Usando `v-model` en los checkboxes.
   - **Editar tareas**: Con doble clic o botón de editar.
   - **Eliminar tareas**: Con confirmación antes de eliminar.
   - **Filtrar tareas**: Entre todas, completadas o pendientes.
   - **Persistencia**: Guarda las tareas en `localStorage`.

3. **Características Avanzadas**:
   - Directiva personalizada `v-focus` para enfocar automáticamente los inputs de edición.
   - Uso de `computed` para tareas filtradas y contadores.
   - Manejo de eventos del teclado (Enter, Esc) para mejor usabilidad.
   - Estilos responsivos y con feedback visual para las interacciones del usuario.

4. **Buenas Prácticas**:
   - Uso de `:key` en los bucles `v-for` para un renderizado eficiente.
   - Separación clara entre lógica, presentación y estilos.
   - Código limpio y bien documentado.

Este ejercicio demuestra el uso efectivo de las directivas `v-if`, `v-for` y `v-model` en un contexto práctico, mostrando cómo se pueden combinar para crear interfaces de usuario interactivas y responsivas con Vue.js.
