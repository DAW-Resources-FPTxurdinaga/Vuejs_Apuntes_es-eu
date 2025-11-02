# HTTP Eskariak Vue-n

## Sarrera

Web aplikazio gehienek atzeko-zerbitzari batekin komunikatu behar dute datuak eskuratzeko edo bidaltzeko. Vue-ek ez du berezko HTTP bezerorik, baina `fetch` API estandarra edo `axios` bezalako hirugarrenen liburutegiak erabil ditzakegu.

## Fetch API erabiliz

`fetch` API modernoa da eta promesetan oinarritzen da.

### Oinarrizko Eskaera

```vue
<template>
  <div>
    <h2>Erabiltzaile Zerrenda</h2>
    <ul v-if="!kargatzen && !errorea">
      <li v-for="erabiltzaile in erabiltzaileak" :key="erabiltzaile.id">
        {{ erabiltzaile.izena }} ({{ erabiltzaile.email }})
      </li>
    </ul>
    
    <div v-if="kargatzen">Kargatzen...</div>
    <div v-if="errorea" class="errorea">{{ errorea }}</div>
    
    <button @click="berrituDatuak">Berritu Datuak</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      erabiltzaileak: [],
      kargatzen: false,
      errorea: null
    }
  },
  created() {
    this.kargatuErabiltzaileak()
  },
  methods: {
    async kargatuErabiltzaileak() {
      this.kargatzen = true
      this.errorea = null
      
      try {
        const erantzuna = await fetch('https://jsonplaceholder.typicode.com/users')
        
        if (!erantzuna.ok) {
          throw new Error(`HTTP errorea! Egoera: ${erantzuna.status}`)
        }
        
        this.erabiltzaileak = await erantzuna.json()
      } catch (error) {
        console.error('Errorea erabiltzaileak kargatzean:', error)
        this.errorea = 'Ezin izan dira erabiltzaileak kargatu. Saia zaitez berriro geroago.'
      } finally {
        this.kargatzen = false
      }
    },
    berrituDatuak() {
      this.kargatuErabiltzaileak()
    }
  }
}
</script>

<style scoped>
.errorea {
  color: #f44336;
  margin: 10px 0;
  padding: 10px;
  background-color: #ffebee;
  border-radius: 4px;
}

button {
  margin-top: 15px;
  padding: 8px 16px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #3aa876;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  padding: 8px;
  margin: 5px 0;
  background-color: #f5f5f5;
  border-radius: 4px;
}
</style>
```

## Axios erabiliz

Axios HTTP bezeroa da, eta hobea da errore-kudeaketarako eta HTTP eskaerak egiteko.

### Instalazioa

```bash
npm install axios
# edo
yarn add axios
```

### Adibidea Axios-ekin

```vue
<template>
  <div>
    <h2>Mezua Bidali</h2>
    <form @submit.prevent="bidaliMezua">
      <div>
        <label>Izena:</label>
        <input v-model="mezua.izena" required>
      </div>
      <div>
        <label>Emaila:</label>
        <input v-model="mezua.email" type="email" required>
      </div>
      <div>
        <label>Mezua:</label>
        <textarea v-model="mezua.testua" required></textarea>
      </div>
      <button type="submit" :disabled="bidaltzen">
        {{ bidaltzen ? 'Bidaltzen...' : 'Bidali' }}
      </button>
      <div v-if="bidalketaArrakastatsua" class="arrakasta">
        Mezua ongi bidali da!
      </div>
      <div v-if="errorea" class="errorea">
        {{ errorea }}
      </div>
    </form>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      mezua: {
        izena: '',
        email: '',
        testua: ''
      },
      bidaltzen: false,
      bidalketaArrakastatsua: false,
      errorea: null
    }
  },
  methods: {
    async bidaliMezua() {
      this.bidaltzen = true
      this.errorea = null
      this.bidalketaArrakastatsua = false
      
      try {
        const erantzuna = await axios.post(
          'https://jsonplaceholder.typicode.com/posts',
          this.mezua
        )
        
        console.log('Erantzuna:', erantzuna.data)
        this.bidalketaArrakastatsua = true
        this.garbituFormularioa()
      } catch (error) {
        console.error('Errorea mezuaren bidalketan:', error)
        
        if (error.response) {
          // Zerbitzariak 4xx/5xx erantzuna itzuli du
          this.errorea = `Errorea ${error.response.status}: ${error.response.statusText}`
        } else if (error.request) {
          // Eskaera egin da baina ez da erantzunik jaso
          this.errorea = 'Ez da erantzunik jaso. Ziurtatu internet konexioa duzula.'
        } else {
          // Beste akats batzuk
          this.errorea = 'Errorea mezuaren bidalketan: ' + error.message
        }
      } finally {
        this.bidaltzen = false
      }
    },
    garbituFormularioa() {
      this.mezua = {
        izena: '',
        email: '',
        testua: ''
      }
    }
  }
}
</script>

<style scoped>
form {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #f9f9f9;
}

div {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input[type="text"],
input[type="email"],
textarea {
  width: 100%;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-sizing: border-box;
}

textarea {
  height: 100px;
  resize: vertical;
}

button {
  background-color: #42b983;
  color: white;
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background-color: #3aa876;
}

.arrakasta {
  margin-top: 15px;
  padding: 10px;
  background-color: #dff0d8;
  color: #3c763d;
  border-radius: 4px;
}

.errorea {
  margin-top: 15px;
  padding: 10px;
  background-color: #f2dede;
  color: #a94442;
  border-radius: 4px;
}
</style>
```

## Interceptoreak eta Konfigurazioa

### Axios Instantzia Globala Sortu

```js
// src/utils/axios.js
import axios from 'axios'

const api = axios.create({
  baseURL: 'https://api.zureaplikazioa.eus/api',
  timeout: 10000, // 10 segundu
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  }
})

// Eskaeren aurreko prozesamendua
api.interceptors.request.use(
  config => {
    // Gehitu autentikazio-tokena eskaera guztietara
    const token = localStorage.getItem('auth_token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  error => {
    return Promise.reject(error)
  }
)

// Erantzunen prozesamendua
api.interceptors.response.use(
  response => response,
  error => {
    // Kudeatu 401 erroreak (Ez baimendua)
    if (error.response && error.response.status === 401) {
      // Erabiltzailea deskonektatu edo tokena berritu
      console.error('Erabiltzailea deskonektatua')
      // Nahi adina logika gehiago...
    }
    return Promise.reject(error)
  }
)

export default api
```

### Erabilera osagai batean

```vue
<script>
import api from '@/utils/axios'

export default {
  data() {
    return {
      datuak: [],
      kargatzen: false,
      errorea: null
    }
  },
  async created() {
    await this.kargatuDatuak()
  },
  methods: {
    async kargatuDatuak() {
      this.kargatzen = true
      this.errorea = null
      
      try {
        const response = await api.get('/datuak')
        this.datuak = response.data
      } catch (error) {
        console.error('Errorea datuak kargatzean:', error)
        this.errorea = 'Ezin izan dira datuak kargatu'
      } finally {
        this.kargatzen = false
      }
    },
    
    async bidaliDatuak(datuak) {
      try {
        await api.post('/datuak', datuak)
        // Egin zerbait bidalketa ondo joan bada
      } catch (error) {
        console.error('Errorea datuak bidaltzean:', error)
        throw error // Bota errorea deitzaileari bidaltzeko
      }
    }
  }
}
</script>
```

## Ariketa Praktikoa

Sortu blog-aplikazio bat API baten bidez kudeatutako edukiekin:

1. Sortu erabiltzailearen autentifikazioa (erregistratu, saioa hasi, saioa itxi)
2. Mezuak ikusi, sortu, eguneratu eta ezabatu (CRUD)
3. Iruzkinak gehitu eta kudeatu
4. Etiketak kudeatu eta mezuak etiketen arabera iragazi
5. Errendimendua optimizatu cache-sistema batekin
6. Inplementatu orri-kopurua eta ordenazioa
7. Gehitu bilaketa funtzionalitatea
8. Sortu erabiltzaileen profilak
9. Inplementatu igoerak eta fitxategi-kudeaketa
10. Gehitu benetako denbora-eguneraketak WebSocket-ekin
