# 4.3 Axios-en Erabilera Vue 3-n

Axios promesetan oinarritutako HTTP bezero liburutegi bat da, eta HTTP eskaerak kudeatzea errazten du JavaScript aplikazioetan. Bereziki erabilgarria da Vue 3 aplikazioetan, integrazio errazagatik eta ezaugarri aurreratuengatik.

## Instalazioa

Axios zure proiektuan instalatzeko:

```bash
npm install axios
# edo
yarn add axios
```

## Oinarrizko konfigurazioa

Sortu Axios-en konfigurazio-fitxategi bat (adibidez, `src/utils/api.js`):

```javascript
import axios from 'axios'

const api = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  }
})

export default api
```

## Adibide osatua Axios-ekin

```vue
<template>
  <div class="http-demo">
    <h2>Erabiltzaileen Zerrenda (Axios)</h2>
    <button @click="fetchUsers" :disabled="loading">
      {{ loading ? 'Kargatzen...' : 'Kargatu Erabiltzaileak' }}
    </button>
    
    <div v-if="error" class="error">
      {{ error }}
    </div>
    
    <ul v-else-if="users.length > 0" class="user-list">
      <li v-for="user in users" :key="user.id" class="user-item">
        <img :src="user.avatar" :alt="user.first_name" class="avatar">
        <div class="user-info">
          <h3>{{ user.first_name }} {{ user.last_name }}</h3>
          <p>{{ user.email }}</p>
        </div>
      </li>
    </ul>
    
    <p v-else-if="!loading" class="no-data">
      Ez dago erakusteko erabiltzailerik. Egin klik botoian haiek kargatzeko.
    </p>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import api from '@/utils/api'

const users = ref([])
const loading = ref(false)
const error = ref(null)

async function fetchUsers() {
  loading.value = true
  error.value = null
  
  try {
    const response = await api.get('/users')
    users.value = response.data.data
  } catch (err) {
    console.error('Errorea erabiltzaileak kargatzean:', err)
    error.value = 'Ezin izan dira erabiltzaileak kargatu. Saiatu berriro geroago.'
  } finally {
    loading.value = false
  }
}
</script>
```

## HTTP metodoak

### GET
```javascript
// Baliabidea lortu
const response = await api.get('/users')

// Parametroekin
const response = await api.get('/users', {
  params: {
    page: 1,
    limit: 10
  }
})
```

### POST
```javascript
// Baliabide berria sortu
const newUser = {
  name: 'John',
  job: 'Developer'
}

const response = await api.post('/users', newUser)
```

### PUT
```javascript
// Baliabide osoa eguneratu
const updatedUser = {
  name: 'John',
  job: 'Senior Developer'
}

const response = await api.put('/users/1', updatedUser)
```

### PATCH
```javascript
// Baliabidearen zati bat eguneratu
const updates = {
  job: 'Team Lead'
}

const response = await api.patch('/users/1', updates)
```

### DELETE
```javascript
// Baliabidea ezabatu
const response = await api.delete('/users/1')
```

## Ezaugarri aurreratuak

### Eskaeren atzizainak

```javascript
// Erantzuna aldez aurretik prozesatzeko atzizaina
api.interceptors.request.use(
  config => {
    // Token-a gehitu eskaera bakoitzean
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  error => {
    return Promise.reject(error)
  }
)

// Erantzunak prozesatzeko atzizaina
api.interceptors.response.use(
  response => response,
  error => {
    if (error.response?.status === 401) {
      // Erabiltzailea desautentikatu
      router.push('/login')
    }
    return Promise.reject(error)
  }
)
```

### Eskaerak bertan uztea

```javascript
const source = axios.CancelToken.source()

try {
  const response = await api.get('/users', {
    cancelToken: source.token
  })
  // Procesar respuesta
} catch (err) {
  if (axios.isCancel(err)) {
    console.log('Eskaera bertan behera utzi da')
  } else {
    // Manejar otros errores
  }
}

// Eskaera bertan uzteko
source.cancel('Erabiltzaileak eskaera bertan behera utzi du')
```

## Erroreen kudeaketa globala

```javascript
// ErrorHandler.vue
<template>
  <div v-if="error" class="error-overlay">
    <div class="error-content">
      <h3>Errorea gertatu da</h3>
      <p>{{ errorMessage }}</p>
      <button @click="dismissError">Itxi</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const error = ref(null)
const errorMessage = ref('')

const showError = (message) => {
  error.value = true
  errorMessage.value = message
}

const dismissError = () => {
  error.value = false
}

defineExpose({
  showError,
  dismissError
})
</script>

<style scoped>
.error-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.error-content {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  max-width: 500px;
  width: 90%;
  text-align: center;
}
</style>
```

## Konklusioa

Axios-ek funtzionalitate aurreratuak eskaintzen ditu HTTP eskaerak kudeatzeko, hala nola:
- Atzizainak eskaera eta erantzunetarako
- Eskaeren bertan behera uztea
- Erroreen kudeaketa zentralizatua
- Automatikoki JSON bihurtzea
- CSRF babesa

Hurrengo atalean, Alova aztertuko dugu, Vue-rentzako alternatiba arin eta indartsu bat.
