# Vue 3 Cheat Sheet - Erreferentzia Azkarra

## Oinarrizko Zuzentarauak

### Interpolazioa
```vue
<template>
  <p>{{ mezua }}</p>
  <p v-text="'Kaixo ' + izena"></p>
  <p v-html="htmlEdukia"></p>
</template>
```

### Baldintzak
```vue
<template>
  <div v-if="ikusgaiDago">Ikusgai</div>
  <div v-else-if="besteBaldintzaBat">Beste baldintza bat</div>
  <div v-else>Lehenetsia</div>
  
  <div v-show="ikusgaiDago">v-if-ren ordezkoa</div>
</template>
```

### Begiztak
```vue
<template>
  <!-- Array baten gainean iteratu -->
  <ul>
    <li v-for="(elementua, index) in elementuak" :key="elementua.id">
      {{ index }} - {{ elementua.izena }}
    </li>
  </ul>
  
  <!-- Objektu baten gainean iteratu -->
  <div v-for="(balioa, gakoa) in objektua" :key="gakoa">
    {{ gakoa }}: {{ balioa }}
  </div>
</template>
```

### Datu-loturak
```vue
<template>
  <!-- v-bind edo : -->
  <a :href="esteka">Esteka</a>
  <img :src="irudiarenUrl" :alt="testuAlternatiboa">
  
  <!-- v-model -->
  <input v-model="testua" placeholder="Idatzi zerbait">
  <input type="checkbox" v-model="hautatutaDago">
  <select v-model="hautatua">
    <option v-for="aukera in aukerak" :key="aukera">
      {{ aukera }}
    </option>
  </select>
</template>
```

## Composition API

### Erantzunkortasun Oinarrizkoa
```vue
<script setup>
import { ref, reactive, computed, watch, watchEffect } from 'vue'

// Aldagai eraginkorrak
const kontadorea = ref(0)
const egoera = reactive({
  izena: 'Mikel',
  adina: 30
})

// Kalkulatutako propietateak
const izenOsoa = computed(() => {
  return `${egoera.izena} Lopez`
})

### Bizitza Zikloa
```vue
<script setup>
import { onMounted, onUpdated, onUnmounted } from 'vue'

onMounted(() => {
  console.log('Osagaia muntatuta')
  // Hasieratu liburutegiak, egin eskaerak, etab.
})

onUpdated(() => {
  console.log('Osagaia eguneratuta')
})

onUnmounted(() => {
  console.log('Osagaia desmuntatuta')
  // Garbitu entzuleak, tarteak, etab.
})
</script>
```

## Gertaeren Kudeaketa
```vue
<template>
  <button @click="handitu">Handitu</button>
  <input @keyup.enter="bidaliInprimakia">
  <form @submit.prevent="bidaliInprimakia">
    <button type="submit">Bidali</button>
  </form>
  
  <!-- Argumentuak dituzten gertaera-kudeatzaileak -->
  <button @click="(event) => agurtu('Kaixo', $event)">Agurtu</button>
</template>

<script setup>
function handitu() {
  kontadorea.value++
}

function bidaliInprimakia() {
  // Inprimakiaren logika
}

function agurtu(mezua, event) {
  console.log(mezua, event.target)
}
</script>
```

## Txantiloi-erreferentziak (refs)
```vue
<template>
  <input ref="sarreraRef" type="text">
  <button @click="fokuratuSarrera">Fokuratu sarrera</button>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const sarreraRef = ref(null)

onMounted(() => {
  // sarreraRef.value-k DOM-eko elementuaren erreferentzia du
  console.log(sarreraRef.value) // <input type="text">
})

function fokuratuSarrera() {
  sarreraRef.value.focus()
}
</script>
```

## Zuzentarau Pertsonalizatuak
```vue
<template>
  <p v-nabarmendu="'horia'">Testu nabarmendua</p>
  <button v-finkatu="200">Botoi finkoa</button>
</template>

<script setup>
// Zuzentarau globala (main.js edo antzekoa)
const app = createApp(App)

// Nabarmendzeko zuzentaraua
app.directive('nabarmendu', {
  mounted(el, binding) {
    el.style.backgroundColor = binding.value || 'yellow'
    el.style.padding = '5px'
  },
  updated(el, binding) {
    el.style.backgroundColor = binding.value
  }
})

// Finkatzeko zuzentaraua
app.directive('finkatu', {
  mounted(el, binding) {
    el.style.position = 'fixed'
    el.style.top = binding.value + 'px'
  }
})
</script>
```

## Props eta Emits
```vue
<!-- Seme-osagaia -->
<template>
  <div>
    <h2>{{ izenburua }}</h2>
    <button @click="bidalitakoGertakizuna">Egin klik</button>
  </div>
</template>

<script setup>
const props = defineProps({
  izenburua: {
    type: String,
    required: true
  },
  zenbakia: {
    type: Number,
    default: 0
  }
})

const emit = defineEmits(['gertakizunPertsonalizatua'])

function bidalitakoGertakizuna() {
  emit('gertakizunPertsonalizatua', 'Datuak')
}
</script>

<!-- Guraso-osagaia -->
<template>
  <SemeOsagaia 
    :izenburua="izenburua" 
    @gertakizunPertsonalizatua="kudeatuGertakizuna"
  />
</template>
```

## Beste Aholku Garrantzitsu Batzuk

1. **Errendimendua**: Erabili `v-once` behin bakarrik errendatu behar diren elementuentzat.
2. **Gakoak**: Erabili `:key` beti `v-for`-rekin errendatze egokiagoa lortzeko.
3. **Konputazio Astunak**: Erabili `computed` propietateak emaitzak cacheatzeko.
4. **Gertaera Modifikatzaileak**: Erabili `.prevent`, `.stop`, `.self`, etab. gertaeren portaera kontrolatzeko.
5. **Dinamikoki Osagaiak**: Erabili `<component :is="...">` osagaiak dinamikoki aldatzeko.
6. **Slot-ak**: Erabili `<slot>` eduki moldagarrirako.
7. **Dinamikoki Zuzentarauak**: Erabili `v-bind:[directiva]="balioa"` zuzentarau dinamikoak sortzeko.
8. **Teleport**: Erabili `<teleport>` modalen edo tooltip-ak goragoko mailako elementuetara bidaltzeko.
9. **Berrizabiltzea**: Atera logika errepikatua `composables`-etan.

   ```javascript
   // erabiliKontadorea.js
   import { ref, computed } from 'vue'
   
   export function erabiliKontadorea() {
     const kontadorea = ref(0)
     
     function handitu() {
       kontadorea.value++
     }
     
     const bikoitza = computed(() => kontadorea.value * 2)
     
     return { kontadorea, handitu, bikoitza }
   }
   ```

10. **Erroreak Kudeatzea**: Erabili `onErrorCaptured` barneko erroreak harrapatzeko.

## Ondorioa

Hau Vue 3-ren erabilerarik ohikoenen laburpen azkar bat da. Dokumentazio ofizialean sakondu dezakezu: [Vue 3 Dokumentazioa](https://v3.vuejs.org/)


## Hurrengo urratsa

Ikasitako guztia praktikan jarriko dugu ariketa praktiko batekin

[Hurrengoa: Ariketa praktikoa →](ariketa-praktikoa.md)