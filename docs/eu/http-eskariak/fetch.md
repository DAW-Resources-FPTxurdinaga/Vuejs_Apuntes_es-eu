# 4.2 Fetch API-ren Erabilera

Fetch API-a HTTP eskaerak egiteko modu natiboa da JavaScript modernoan. Nabigatzaile moderno guztietan dago eskuragarri eta ez du beste menpekotasunik behar.

## Oinarrizko GET eskaera

```vue
<template>
  <div class="http-demo">
    <h2>Erabiltzaileen Zerrenda (Fetch)</h2>
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

const users = ref([])
const loading = ref(false)
const error = ref(null)

async function fetchUsers() {
  loading.value = true
  error.value = null
  
  try {
    const response = await fetch('https://reqres.in/api/users?delay=1')
    
    if (!response.ok) {
      throw new Error(`HTTP errorea: ${response.status}`)
    }
    
    const data = await response.json()
    users.value = data.data
  } catch (err) {
    console.error('Errorea erabiltzaileak kargatzean:', err)
    error.value = 'Ezin izan dira erabiltzaileak kargatu. Saiatu berriro geroago.'
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
/* Estiloak hemen */
</style>
```

## Fetch erabiltzearen abantailak

- **Natiboa**: Ez du gehigarririk behar
- **Promise-ak**: Asinkronoa den kodea modu garbiagoan kudeatzeko
- **Malgua**: Eskaeretan kontrol zehatza ahalbidetzen du
- **Streaming**: Datu-fluxuen irakurketa onartzen du

## Desabantailak

- **Nabigatzaile zaharren euskarri eza**: IE11 eta bertsio zaharragoetarako polyfill-a behar du
- **Bertan behera uzte eza**: Ez du eskaerak bertan uzteko euskarri integratua
- **Erroreen kudeaketa eskuz**: HTTP egoera-kodeen egiaztapena eskuz egin behar da

## Async/await-ekin erabiltzea

```javascript
async function getData(url) {
  try {
    const response = await fetch(url)
    if (!response.ok) {
      throw new Error(`Errorea: ${response.status}`)
    }
    return await response.json()
  } catch (error) {
    console.error('Errorea eskaeran:', error)
    throw error
  }
}
```

## Datuak bidaltzea (POST, PUT, DELETE)

```javascript
// POST eskaera
async function postData(url, data) {
  try {
    const response = await fetch(url, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data)
    })
    
    if (!response.ok) {
      throw new Error(`Errorea: ${response.status}`)
    }
    
    return await response.json()
  } catch (error) {
    console.error('Errorea datuak bidaltzean:', error)
    throw error
  }
}

// PUT eskaera
async function updateData(url, data) {
  const response = await fetch(url, {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(data)
  })
  return response.json()
}

// DELETE eskaera
async function deleteData(url) {
  const response = await fetch(url, {
    method: 'DELETE'
  })
  return response.ok
}
```

## Konfigurazio aurreratua

```javascript
// Eskaera konfigurazioa
const controller = new AbortController()
const signal = controller.signal

// Eskaera bertan uzteko
setTimeout(() => controller.abort(), 5000) // 5 segundu ondoren bertan behera

try {
  const response = await fetch('https://api.example.com/data', {
    signal,
    method: 'GET',
    headers: {
      'Authorization': 'Bearer tu-token-aqui',
      'Custom-Header': 'Valore bat'
    },
    mode: 'cors',
    cache: 'no-cache',
    credentials: 'same-origin'
  })
  
  const data = await response.json()
  console.log(data)
} catch (err) {
  if (err.name === 'AbortError') {
    console.log('Eskaera bertan behera utzi da')
  } else {
    console.error('Beste errore bat gertatu da:', err)
  }
}
```

## Erroreen kudeaketa hobetua

```javascript
class ApiError extends Error {
  constructor(message, status, data) {
    super(message)
    this.name = 'ApiError'
    this.status = status
    this.data = data
  }
}

async function fetchWithErrorHandling(url, options = {}) {
  try {
    const response = await fetch(url, options)
    const data = await response.json()
    
    if (!response.ok) {
      throw new ApiError(
        data.message || 'Errorea eskaeran',
        response.status,
        data
      )
    }
    
    return data
  } catch (error) {
    if (error instanceof SyntaxError) {
      console.error('Errorea JSON-a analizatzean:', error)
      throw new Error('Errorea datuak prozesatzean')
    }
    throw error
  }
}
```

## Ondorioak

Fetch API-a erabiltzea erraza da eta funtzionalitate handia eskaintzen du, baina kontuan izan behar dituzu bere mugak, batez ere aplikazio konplexuetan. Etorkizuneko ataletan, Axios eta Alova bezalako liburutegiak aztertuko ditugu, zeinak ezaugarri gehigarriak eskaintzen dituzten, hala nola eskaeren bertan beharreko euskarria edo cache automatikoa.
