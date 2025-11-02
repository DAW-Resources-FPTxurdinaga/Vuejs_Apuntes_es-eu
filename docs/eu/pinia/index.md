# Pinia

## Sarrera

Pinia Vue.js aplikazioetako ofizialeko biltegi-modulua da, egoera kudeatzeko. Vuex-en ordezko gisa garatu zen, baina API sinpleagoa eta TypeScript hobeto onartzen du.

## Ezaugarri nagusiak

- **Sinpletasuna**: API intuitiboa eta erraza erabiltzeko
- **TypeScript laguntza**: Erabilera erraza TypeScript-ekin
- **Modularra**: Biltegi anitz onartzen ditu
- **Devtools laguntza**: Vue DevTools-ekin bateragarria
- **Hot Module Replacement (HMR)**: Garapenerako ezaugarri onak
- **Kode-banaketa**: Automatikoki zure kodea banatzen du

## Instalazioa

```bash
npm install pinia
# edo
yarn add pinia
```

### Aplikazioan Konfiguratzea

```js
// src/main.js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const app = createApp(App)
const pinia = createPinia()

app.use(pinia)
app.mount('#app')
```

## Lehen Biltegia Sortu

```js
// stores/counter.js
import { defineStore } from 'pinia'

export const erabileraKontagailua = defineStore('counter', {
  state: () => ({
    count: 0,
    izena: 'Erabiltzailea'
  }),
  getters: {
    bikoitia: (state) => state.count % 2 === 0,
    biderBi: (state) => state.count * 2
  },
  actions: {
    increment() {
      this.count++
    },
    async incrementAsync() {
      // Adibide bat sinkronizazio asinkronorako
      const count = await fetch('/api/increment').then(response => response.json())
      this.count = count
    }
  }
})
```

## Biltegia Erabiltzen

### Oinarrizko Erabilera

```vue
<template>
  <div>
    <h1>Kontagailua: {{ counter.count }}</h1>
    <p>Bikoitia: {{ counter.bikoitia ? 'Bai' : 'Ez' }}</p>
    <p>Bider bi: {{ counter.biderBi }}</p>
    
    <button @click="counter.increment()">Gehitu</button>
    <button @click="counter.incrementAsync()">Gehitu asinkronoki</button>
    
    <input v-model="counter.izena" placeholder="Idatzi zure izena">
    <p>Kaixo, {{ counter.izena }}!</p>
  </div>
</template>

<script>
import { erabileraKontagailua } from '@/stores/counter'

export default {
  setup() {
    const counter = erabileraKontagailua()
    
    return {
      counter
    }
  }
}
</script>
```

### Erabilera Options API-rekin

```vue
<template>
  <div>
    <h1>Kontagailua: {{ count }}</h1>
    <p>Bikoitia: {{ bikoitia ? 'Bai' : 'Ez' }}</p>
    <p>Bider bi: {{ biderBi }}</p>
    
    <button @click="increment">Gehitu</button>
    <input v-model="izena" placeholder="Idatzi zure izena">
  </div>
</template>

<script>
import { mapState, mapActions } from 'pinia'
import { erabileraKontagailua } from '@/stores/counter'

export default {
  computed: {
    // Mapeatu egoera eta getterrak
    ...mapState(erabileraKontagailua, ['count', 'izena']),
    ...mapState(erabileraKontagailua, {
      bikoitia: 'bikoitia',
      biderBi: 'biderBi'
    })
  },
  methods: {
    // Mapeatu ekintzak
    ...mapActions(erabileraKontagailua, ['increment'])
  }
}
</script>
```

## Biltegi Anitzak Erabiltzea

```js
// stores/user.js
import { defineStore } from 'pinia'

export const erabileraErabiltzailea = defineStore('user', {
  state: () => ({
    user: null,
    preferences: {
      theme: 'light',
      notifications: true,
      language: 'eu'
    }
  }),
  getters: {
    isLoggedIn: (state) => state.user !== null,
    userInitials: (state) => {
      if (!state.user) return ''
      return state.user.name
        .split(' ')
        .map(name => name[0])
        .join('')
        .toUpperCase()
    }
  },
  actions: {
    async login(credentials) {
      try {
        const response = await fetch('/api/login', {
          method: 'POST',
          body: JSON.stringify(credentials)
        })
        this.user = await response.json()
        return true
      } catch (error) {
        console.error('Errorea saioa hastean:', error)
        throw error
      }
    },
    logout() {
      this.user = null
    },
    updatePreferences(newPreferences) {
      this.preferences = { ...this.preferences, ...newPreferences }
    }
  },
  persist: true // Egoera gordetzeko plugina erabiliz
})
```

### Biltegi anitzak konbinatuz

```vue
<template>
  <div>
    <div v-if="userStore.isLoggedIn">
      <h1>Ongi etorri, {{ userStore.user.name }}!</h1>
      <p>Zure inicialak: {{ userStore.userInitials }}</p>
      
      <div>
        <h2>Hobespenak</h2>
        <label>
          Gaia:
          <select v-model="userStore.preferences.theme">
            <option value="light">Argia</option>
            <option value="dark">Iluna</option>
          </select>
        </label>
        
        <label>
          <input 
            type="checkbox" 
            v-model="userStore.preferences.notifications"
          >
          Jakinarazpenak aktibatu
        </label>
        
        <button @click="gordeHobespenak">Gorde hobespenak</button>
      </div>
      
      <button @click="kontagailua.increment()">
        Kontagailua: {{ kontagailua.count }}
      </button>
      
      <button @click="irten">Irten</button>
    </div>
    
    <div v-else>
      <h1>Saioa hasi</h1>
      <form @submit.prevent="sartu">
        <input v-model="erabiltzailea" placeholder="Erabiltzailea">
        <input v-model="pasahitza" type="password" placeholder="Pasahitza">
        <button type="submit">Sartu</button>
      </form>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
import { erabileraKontagailua } from '@/stores/counter'
import { erabileraErabiltzailea } from '@/stores/user'

export default {
  setup() {
    const kontagailua = erabileraKontagailua()
    const userStore = erabileraErabiltzailea()
    
    const erabiltzailea = ref('')
    const pasahitza = ref('')
    
    const sartu = async () => {
      try {
        await userStore.login({
          username: erabiltzailea.value,
          password: pasahitza.value
        })
        // Berbideratu erabiltzailea saio hasi ondoren
      } catch (error) {
        alert('Errorea saio hastean: ' + error.message)
      }
    }
    
    const irten = () => {
      userStore.logout()
    }
    
    const gordeHobespenak = async () => {
      try {
        await fetch('/api/preferences', {
          method: 'POST',
          body: JSON.stringify(userStore.preferences)
        })
        alert('Hobespenak gorde dira!')
      } catch (error) {
        console.error('Errorea hobespenak gordetzean:', error)
        alert('Ezin izan dira hobespenak gorde')
      }
    }
    
    return {
      kontagailua,
      userStore,
      erabiltzailea,
      pasahitza,
      sartu,
      irten,
      gordeHobespenak
    }
  }
}
</script>
```

## Egoeraren Persistentzia

Egoera gordetzeko `pinia-plugin-persistedstate` plugina erabil dezakezu:

```bash
npm install pinia-plugin-persistedstate
# edo
yarn add pinia-plugin-persistedstate
```

### Konfigurazioa

```js
// main.js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'
import App from './App.vue'

const pinia = createPinia()
pinia.use(piniaPluginPersistedstate)

const app = createApp(App)
app.use(pinia)
app.mount('#app')
```

### Erabilera Biltegian

```js
export const erabileraErabiltzailea = defineStore('user', {
  state: () => ({
    user: null,
    preferences: {
      theme: 'light',
      notifications: true
    }
  }),
  
  // Egoera gordetzeko konfigurazioa
  persist: {
    enabled: true,
    strategies: [
      {
        key: 'user', // Gakoa gordetzeko erabiliko dena
        storage: localStorage, // edo sessionStorage
        paths: ['user', 'preferences.theme'] // Zein propietate gorde
      }
    ]
  }
})
```

## Ariketa Praktikoa

Egin erosketa-saskiko aplikazioa Pinia erabiliz:

1. Sortu `produktuak` biltegia produktu-katalogoarentzat
2. Sortu `saskia` biltegia erosketa-saskirako
3. Inplementatu produktuak gehitzea eta kentzea saskitik
4. Gehitu kantitatea aldatzea saskian
5. Kalkulatu totala eta deskontuak
6. Gorde saskiko edukia tokiko biltegian
7. Sortu erabiltzailearen saio-hasiera eta profila
8. Inplementatu gogokoetako produktuen zerrenda
9. Gehitu txanpon-trukea (moneta-tasa bidez)
10. Sortu aginduen jarraipena eta historia

### Aholkuak:

- Erabili getters kalkulatuak prezio totalak eta deskontuak kalkulatzeko
- Erabili actions API deietarako eta logika konplexuetarako
- Erabili TypeScript hobeto kudeatzeko motak
- Inplementatu kargatze- eta errore-egoerak
- Erabili persistitu gabeko egoerak behin-behineko datuetarako
