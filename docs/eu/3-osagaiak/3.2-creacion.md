# 3.1 Osagaiak Sortzea Vue 3-n

Osagaiak Vue aplikazioen oinarrizko eraikuntza blokeak dira. Erabiltzaile-interfazea pieza independente eta berrerabilgarrietan banatzeko aukera ematen dute.

## Osagaia Composition API-rekin

```vue
<template>
  <div>
    <h1>{{ title }}</h1>
    <p>{{ message }}</p>
    <button @click="increment">Kontagailua: {{ count }}</button>
    <p>Bikoitza: {{ doubleCount }}</p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

// Egoera erreaktiboa
const count = ref(0)
const title = ref('Nire Osagaia Composition API-rekin')
const message = 'Kaixo Vue 3 Composition API-tik!'

// Kalkulatutako propietatea
const doubleCount = computed(() => count.value * 2)

// Metodoa
function increment() {
  count.value++
}

// Bizitza-zikloaren hook-a
onMounted(() => {
  console.log('Osagaia muntatuta')
})
</script>

<style scoped>
/* Estiloak osagaira mugatuta */
button {
  background-color: #42b983;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 10px;
}

button:hover {
  background-color: #3aa876;
}
</style>
```

## Fitxategi Bakarreko Osagaiak (SFC)

`.vue` fitxategiek osagai baten txantiloia, logika eta estiloak fitxategi bakar batean kapsulatzeko aukera ematen dute:

```
nire-osagaia/
├── NireOsagaia.vue    // Osagai nagusia
└── components/
    └── BotoiPertsonalizatua.vue  // Seme-osagaia
```

## Osagaien Erregistroa

### Erregistro Globala

```javascript
import { createApp } from 'vue'
import NireOsagaia from './components/NireOsagaia.vue'

const app = createApp({})
app.component('nire-osagaia', NireOsagaia)
```

### Erregistro Lokala

```vue
<script>
import BotoiPertsonalizatua from './BotoiPertsonalizatua.vue'

export default {
  components: {
    BotoiPertsonalizatua
  }
}
</script>
```

## Osagai Dinamikoak (Osagaien arteko aldatzea)

Batzuetan, pantailako kokapen berean osagai desberdinak aldatzea behar dugu. Vue-k hori erraz egiteko aukera ematen digu `<component>` osagai bereziarekin eta `:is` zuzentarauarekin.

### Oinarrizko Adibidea:

```vue
<template>
  <div>
    <!-- Erakutsiko den osagaia aldatzen duten botoiak -->
    <button @click="erakutsi = 'Hasiera'">Hasiera</button>
    <button @click="erakutsi = 'Profila'">Profila</button>
    
    <!-- Erakutsiko den osagaia 'erakutsi' aldagaiaren araberakoa da -->
    <component :is="erakutsi"></component>
  </div>
</template>

<script setup>
import Hasiera from './Hasiera.vue'
import Profila from './Profila.vue'
import { ref } from 'vue'

const erakutsi = ref('Hasiera')
</script>
```

## Osagaien Karga Atzeratua (Lazy Loading)

Errendimendua hobetzeko, osagaiak behar direnean bakarrik karga ditzakegu. Hori oso erabilgarria da berehala behar ez diren osagai handientzat.

### Oinarrizko Karga Atzeratua:

```vue
<template>
  <div>
    <button @click="erakutsi = true">Erakutsi Galeria</button>
    <ArgazkiGaleria v-if="erakutsi" />
  </div>
</template>

<script setup>
import { ref, defineAsyncComponent } from 'vue'

const erakutsi = ref(false)

// ArgazkiGaleria osagaia behar denean bakarrik kargatuko da
const ArgazkiGaleria = defineAsyncComponent(() => 
  import('./components/ArgazkiGaleria.vue')
)
</script>
```

### Karga Atzeratua Kargatze-egoerarekin:

```vue
<template>
  <div>
    <button @click="kargatuGaleria">Erakutsi Galeria</button>
    
    <!-- Kargatze-egoeraren arabera erakusten da -->
    <div v-if="kargatzen" class="kargatze-mezua">
      Kargatzen...
    </div>
    <div v-else-if="errorea" class="errorea">
      Errorea gertatu da galeria kargatzean
    </div>
    <ArgazkiGaleria v-else-if="erakutsi" />
  </div>
</template>

<script setup>
import { ref, defineAsyncComponent } from 'vue'

const erakutsi = ref(false)
const kargatzen = ref(false)
const errorea = ref(false)

async function kargatuGaleria() {
  if (!erakutsi.value) {
    try {
      kargatzen.value = true
      erroreak.value = false
      
      // Osagaia kargatu
      await new Promise(resolve => setTimeout(resolve, 1000)) // Simulatu karga
      
      ArgazkiGaleria = defineAsyncComponent(() => 
        import('./components/ArgazkiGaleria.vue')
      )
      
      erakutsi.value = true
    } catch (err) {
      console.error('Errorea galeria kargatzean:', err)
      erroreak.value = true
    } finally {
      kargatzen.value = false
    }
  } else {
    erakutsi.value = !erakutsi.value
  }
}

let ArgazkiGaleria = null
</script>

<style>
.kargatze-mezua {
  padding: 1rem;
  background-color: #f8f9fa;
  border: 1px solid #dee2e6;
  border-radius: 4px;
  margin-top: 1rem;
}

.errorea {
  padding: 1rem;
  background-color: #f8d7da;
  border: 1px solid #f5c6cb;
  color: #721c24;
  border-radius: 4px;
  margin-top: 1rem;
}
</style>
```

## Ariketa Praktikoa

Sortu `Agurra` izeneko osagai bat, eta erakutsi mezu pertsonalizatu bat erabiltzaileari. Gero, erabili osagai hori zure aplikazio nagusian. Ondoren, sortu bigarren osagai bat `Kontagailua` izenekoa, eta erabili biak batera, bata bestearen barruan.
