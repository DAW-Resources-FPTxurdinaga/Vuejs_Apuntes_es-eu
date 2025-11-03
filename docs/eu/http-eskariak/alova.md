# 4.4 Alova-ren Erabilera Vue 3-n

Alova Vue, React eta Svelte-rentzako datu-eskaerarako irtenbide arina da, eta cachea, egoeraren kudeaketa eta eskaeren bertan behera uztea bezalako ezaugarri aurreratuak eskaintzen ditu.

## Instalazioa

```bash
npm install alova
```

## Oinarrizko konfigurazioa

Sortu konfigurazio-fitxategi bat (adibidez, `src/utils/alova.js`):

```javascript
import { createAlova } from 'alova'
import GlobalFetch from 'alova/GlobalFetch'
import VueHook from 'alova/vue'

export const alovaInstance = createAlova({
  baseURL: 'https://api.example.com',
  requestAdapter: GlobalFetch(),
  statesHook: VueHook,
  
  // Goiburu orokorren konfigurazioa
  headers: {
    'Content-Type': 'application/json',
  },
  
  // Lehenetsitako itxaronaldia
  timeout: 10000,
  
  // Eskaeren atzizainak
  beforeRequest(method) {
    const token = localStorage.getItem('auth_token')
    if (token) {
      method.config.headers.Authorization = `Bearer ${token}`
    }
  },
  
  // Erantzunen atzizainak
  responded: {
    onSuccess: async (response, method) => {
      const data = await response.json()
      if (response.status >= 200 && response.status < 300) {
        return data
      }
      throw new Error(data.message || 'Errorea eskaeran')
    },
    onError: (err, method) => {
      console.error('Errorea eskaeran:', err)
      throw err
    }
  }
})

export default alovaInstance
```

## Oinarrizko erabilera Composable-ekin

```vue
<template>
  <div>
    <h2>Produktuen Zerrenda (Alova)</h2>
    <button @click="fetchProducts" :disabled="loading">
      {{ loading ? 'Kargatzen...' : 'Kargatu Produktuak' }}
    </button>
    
    <div v-if="error" class="error">
      {{ error }}
    </div>
    
    <ul v-else-if="products.length > 0" class="product-list">
      <li v-for="product in products" :key="product.id" class="product-item">
        <h3>{{ product.title }}</h3>
        <p>{{ product.description }}</p>
        <span class="price">{{ product.price }}€</span>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRequest } from 'alova'
import { alovaInstance } from '@/utils/alova'

const products = ref([])
const loading = ref(false)
const error = ref(null)

const fetchProducts = async () => {
  loading.value = true
  error.value = null
  
  try {
    const response = await alovaInstance.Get('/products')
    products.value = response
  } catch (err) {
    console.error('Errorea produktuak kargatzean:', err)
    error.value = 'Ezin izan dira produktuak kargatu. Saiatu berriro geroago.'
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
.product-list {
  list-style: none;
  padding: 0;
}

.product-item {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1rem;
  margin-bottom: 1rem;
  background-color: #f9f9f9;
}

.price {
  font-weight: bold;
  color: #2c3e50;
}

.error {
  color: #e74c3c;
  margin: 1rem 0;
}
</style>
```

## Cache eta Egoera Kudeaketa

```javascript
// Produktu baten datuak kargatzeko funtzio bera erabiliz
const { data, loading, error, send } = useRequest(
  // Eskaeraren konfigurazioa
  alovaInstance.Get('/products/1'),
  
  // Konfigurazioa
  {
    // Cachea 5 minutuz
    localCache: 5 * 60 * 1000,
    
    // Emaitzak automatikoki kargatzeko
    immediate: true,
    
    // Emaitzak prozesatzeko
    initialData: {}
  }
)

// Emaitza freskatzeko
const refreshData = () => {
  send()
}
```

## Eskaera aurreratuak

```javascript
// POST eskaera
const { send: createProduct } = useRequest(
  (productData) => alovaInstance.Post('/products', productData),
  {
    immediate: false,
    onSuccess: (data) => {
      // Emaitza onuragarria denean egitekoa
      console.log('Produktua sortu da:', data)
      // Eguneratu produktuen zerrenda
      products.value = [...products.value, data]
    },
    onError: (error) => {
      console.error('Errorea produktua sortzean:', error)
    }
  }
)

// Produktu berria sortu
const newProduct = {
  title: 'Produktu Berria',
  description: 'Deskribapen labur bat',
  price: 19.99
}

createProduct(newProduct)
```

## Ezaugarri aurreratuak

### 1. Cache estrategiak

```javascript
// Erabili cachea baina eguneratu atzeko aldetik
useRequest(
  alovaInstance.Get('/products', {
    localCache: {
      mode: 'restore',
      expire: 5 * 60 * 1000 // 5 minutu
    }
  })
)
```

### 2. Eguneraketa optimista

```javascript
const { send: updateProduct } = useRequest(
  (productId, updates) => alovaInstance.Patch(`/products/${productId}`, updates),
  {
    immediate: false,
    // Eguneraketa optimista
    optimisticData: (updates) => {
      const productIndex = products.value.findIndex(p => p.id === updates.id)
      if (productIndex !== -1) {
        const updatedProducts = [...products.value]
        updatedProducts[productIndex] = { 
          ...updatedProducts[productIndex], 
          ...updates 
        }
        products.value = updatedProducts
      }
    }
  }
)
```

### 3. Eskaera berrabiaraztea

```javascript
// Erabili `useWatcher` datuak automatikoki berrabiarazteko
const { data: userData, onSuccess } = useWatcher(
  () => alovaInstance.Get(`/users/${userId.value}`),
  [userId], // Berrabiarazi userId aldatzen denean
  {
    // Debounce 300ms
    debounce: 300,
    
    // Lehenetsitako datuak kargatzen ari den bitartean
    initialData: {}
  }
)

// Erabiltzailea eguneratu denean, berrabiarazi datuak
onSuccess(() => {
  console.log('Erabiltzailearen datuak eguneratu dira:', userData.value)
})
```

## Abantailak

1. **Arina**: Tamaina txikia, baina boteretsua
2. **Bateragarritasuna**: Vue, React eta Svelte-rentzat eskuragarri
3. **Cache aurreratua**: Emaitzen cachea automatikoki kudeatzen du
4. **Eguneraketa optimista**: Erabiltzaile-esperientzia hobea
5. **Baliabideen kudeaketa**: Baliabideen erabilera optimizatzen du
6. **Eskaeren bertan behera uztea**: Erraza da eskaerak bertan behera uztea
7. **Berriz saio-hasiera automatikoa**: Konfigurazio erraza huts egindako eskaerentzako

## Ondorioak

Alova aukera bikaina da Vue 3 aplikazioetarako, batez ere:
- Datu-eredu konplexuak dituzten aplikazioak
- Errendimendu handiko interfazeak behar dituzten proiektuak
- Baliabideen erabilera optimizatu behar duten aplikazioak
- Erabiltzaile-esperientzia leuna eta erantzunkorra behar duten proiektuak

Hurrengo atalean, HTTP eskaerak probatzeko eta simulatzeko teknikak ikusiko ditugu.
