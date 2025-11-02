# 2.1 Interpolación y Directivas Básicas

## Interpolación de Texto

La forma más básica de enlazar datos es usando la sintaxis de interpolación de texto (llamada "Mustache" por las llaves `{{ }}`):

```vue
<template>
  <p>Mensaje: {{ mensaje }}</p>
  <p>Resultado: {{ numero * 2 }}</p>
  <p>¿Es mayor de edad? {{ edad >= 18 ? 'Sí' : 'No' }}</p>
</template>

<script setup>
const mensaje = 'Hola Vue!';
const numero = 5;
const edad = 20;
</script>
```

## Directiva v-text

Alternativa a la interpolación `{{ }}`:

```vue
<template>
  <p v-text="'Hola ' + nombre"></p>
</template>

<script setup>
const nombre = 'Usuario';
</script>
```

## Directiva v-html

Para insertar HTML sin escapar:

```vue
<template>
  <div v-html="htmlEnBruto"></div>
</template>

<script setup>
import { ref } from 'vue';

const htmlEnBruto = ref('<span style="color: red">Texto en rojo</span>');
</script>
```

## Directiva v-bind

Para enlazar atributos HTML:

```vue
<template>
  <div :class="{ activo: esActivo }">
    Contenido dinámico
  </div>
  <img :src="urlImagen" :alt="textoAlternativo">
</template>

<script setup>
const esActivo = true;
const urlImagen = '/ruta/a/imagen.jpg';
const textoAlternativo = 'Descripción de la imagen';
</script>
```

## Ejercicio Práctico

Crea un componente que muestre una tarjeta de perfil con:
1. Nombre y apellido interpolados
2. Una imagen con v-bind
3. Una descripción que acepte HTML
4. Un indicador de estado (activo/inactivo)

```vue
<template>
  <div class="perfil" :class="{ 'activo': usuario.activo }">
    <img :src="usuario.foto" :alt="'Foto de ' + usuario.nombre">
    <h2>{{ usuario.nombre }} {{ usuario.apellido }}</h2>
    <div v-html="usuario.descripcion"></div>
    <p>Estado: {{ usuario.activo ? 'Activo' : 'Inactivo' }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const usuario = ref({
  nombre: 'Ana',
  apellido: 'García',
  foto: 'https://via.placeholder.com/150',
  descripcion: 'Desarrolladora <strong>frontend</strong> con experiencia en Vue',
  activo: true
});
</script>

<style scoped>
.perfil {
  border: 1px solid #ddd;
  padding: 1rem;
  border-radius: 8px;
  max-width: 300px;
}

.perfil.activo {
  border-color: #42b983;
  background-color: #f8f9fa;
}

img {
  max-width: 100%;
  border-radius: 4px;
  margin-bottom: 1rem;
}
</style>
```

## Preguntas de Repaso
1. ¿Cuál es la diferencia entre `{{ }}` y `v-text`?
2. ¿Por qué es importante usar `v-html` con precaución?
3. ¿Qué ventaja tiene usar la forma abreviada `:` para `v-bind`?

En la siguiente sección veremos `v-model` para formularios.

[Siguiente: V-bind y V-model →](vbind-vmodel.md)
