# Vue Router

## Sarrera

Vue Router Vue.js aplikazioetako ofizialeko bideratze-biblioteka da. Aplikazio baten nabigazioa kudeatzeko erabiltzen da, orrialde anitzeko aplikazioak (MPA) eta orrialde bakarreko aplikazioak (SPA) sortzeko.

## Instalazioa eta Konfigurazioa

### Vue CLI erabiliz

```bash
vue add router
```

### Eskuz instalatuz

```bash
npm install vue-router@4
# edo
yarn add vue-router@4
```

### Oinarrizko Konfigurazioa

```js
// src/router/index.js
import { createRouter, createWebHistory } from 'vue-router'
import Etengunea from '../views/Etengunea.vue'

const routes = [
  {
    path: '/',
    name: 'Hasiera',
    component: () => import('../views/Hasiera.vue')
  },
  {
    path: '/azalpenak',
    name: 'Azalpenak',
    component: () => import('../views/Azalpenak.vue')
  },
  {
    path: '/kontaktua',
    name: 'Kontaktua',
    component: () => import('../views/Kontaktua.vue')
  },
  {
    path: '/erabiltzaileak/:id',
    name: 'Erabiltzailea',
    component: () => import('../views/Erabiltzailea.vue'),
    props: true
  },
  {
    path: '/:pathMatch(.*)*',
    name: 'EzDaAurkitu',
    component: () => import('../views/404.vue')
  }
]

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes
})

export default router
```

## Nabigazioa

### `router-link` erabiliz

```vue
<template>
  <nav>
    <router-link to="/">Hasiera</router-link>
    <router-link to="/azalpenak">Azalpenak</router-link>
    <router-link :to="{ name: 'Kontaktua' }">Kontaktua</router-link>
    <router-link :to="{ name: 'Erabiltzailea', params: { id: 1 } }">
      Nire Profila
    </router-link>
  </nav>
  <router-view />
</template>

<style>
.router-link-active {
  font-weight: bold;
  color: #42b983;
}
</style>
```

### Programazioz Nabigatzea

```vue
<template>
  <div>
    <button @click="joanAtzera">Atzera</button>
    <button @click="joanAurrera">Aurrera</button>
    <button @click="joanHasierara">Hasierara joan</button>
    <button @click="joanKontaktura">Kontaktura joan</button>
  </div>
</template>

<script>
export default {
  methods: {
    joanAtzera() {
      this.$router.go(-1)
    },
    joanAurdera() {
      this.$router.go(1)
    },
    joanHasierara() {
      this.$router.push('/')
      // edo
      // this.$router.push({ name: 'Hasiera' })
    },
    joanKontaktura() {
      this.$router.push({ name: 'Kontaktua' })
    },
    async eguneratuErabiltzailea() {
      // Nabigatu erabiltzailearen orrira eguneraketaren ondoren
      await this.eguneratuDatuak()
      this.$router.push(`/erabiltzaileak/${this.erabiltzaileId}`)
    }
  }
}
</script>
```

## Bide-parametroak eta Query parametroak

### Bide-parametroak

```vue
<!-- Erabiltzailearen orrialdea -->
<template>
  <div>
    <h1>Erabiltzailearen Profila</h1>
    <p>Erabiltzaile IDa: {{ $route.params.id }}</p>
    <p>Erabiltzaile izena: {{ erabiltzailea.izena }}</p>
  </div>
</template>

<script>
export default {
  props: ['id'], // props: true konfigurazioari esker
  data() {
    return {
      erabiltzailea: {}
    }
  },
  created() {
    this.kargatuErabiltzailea(this.id || this.$route.params.id)
  },
  watch: {
    // Bide-parametroa aldatzen denean, datuak berriro kargatu
    '$route.params.id': {
      handler(id) {
        this.kargatuErabiltzailea(id)
      },
      immediate: true
    }
  },
  methods: {
    async kargatuErabiltzailea(id) {
      try {
        const erantzuna = await fetch(`/api/erabiltzaileak/${id}`)
        this.erabiltzailea = await erantzuna.json()
      } catch (error) {
        console.error('Errorea erabiltzailea kargatzean:', error)
      }
    }
  }
}
</script>
```

### Query parametroak

```vue
<template>
  <div>
    <h1>Bilaketa Emaitzak</h1>
    <p>Bilaketa-terminoa: {{ $route.query.terminoa }}</p>
    <p>Ordenatu: {{ $route.query.ordenatu || 'lehenetsia' }}</p>
    
    <div v-if="emaitzak.length">
      <div v-for="emaitza in emaitzak" :key="emaitza.id">
        <h3>{{ emaitza.izena }}</h3>
        <p>{{ emaitza.deskribapena }}</p>
      </div>
    </div>
    <div v-else>
      <p>Ez da emaitzarik aurkitu "{{ $route.query.terminoa }}" bilaketarako.</p>
    </div>
    
    <router-link 
      :to="{
        name: 'Bilaketa',
        query: { 
          terminoa: $route.query.terminoa,
          ordenatu: 'berria',
          orria: 2
        }
      }"
    >
      Hurrengo orria
    </router-link>
  </div>
</template>

<script>
export default {
  data() {
    return {
      emaitzak: []
    }
  },
  watch: {
    // Query parametroak aldatzen direnean, bilaketa berria egin
    '$route.query': {
      handler() {
        this.eginBilaketa()
      },
      immediate: true
    }
  },
  methods: {
    async eginBilaketa() {
      const { terminoa, ordenatu = 'lehenetsia', orria = 1 } = this.$route.query
      
      if (!termino) {
        this.emaitzak = []
        return
      }
      
      try {
        const erantzuna = await fetch(
          `/api/bilatu?termino=${encodeURIComponent(terminoa)}` +
          `&ordenatu=${ordenatu}&orria=${orria}`
        )
        this.emaitzak = await erantzuna.json()
      } catch (error) {
        console.error('Errorea bilaketan:', error)
        this.emaitzak = []
      }
    }
  }
}
</script>
```

## Iragazkiak eta Bide-egiaztapenak

### Iragazkiak (Navigation Guards)

```js
// router/index.js

// Iragazki orokorra
router.beforeEach((to, from, next) => {
  // Erabiltzailea autentikatu behar da
  const autentikatuta = localStorage.getItem('token')
  
  if (to.matched.some(record => record.meta.requiresAuth)) {
    if (!autentikatuta) {
      next({ name: 'SaioaHasi' })
    } else {
      next()
    }
  } else {
    next()
  }
})

// Bidearen iragazkia
const routes = [
  {
    path: '/admin',
    name: 'Admin',
    component: () => import('../views/Admin.vue'),
    meta: { requiresAuth: true, adminOnly: true },
    beforeEnter: (to, from, next) => {
      const erabiltzailea = JSON.parse(localStorage.getItem('erabiltzailea'))
      if (erabiltzailea && erabiltzailea.rola === 'admin') {
        next()
      } else {
        next({ name: 'BaimenikEz' })
      }
    }
  }
]
```

### Konponentearen iragazkiak

```vue
<script>
export default {
  beforeRouteEnter(to, from, next) {
    // Konponentea sortu aurretik
    // Ez dago sarbiderik `this`-era
    next(vm => {
      // `vm` konponentearen instantzia da
      console.log('Konponentea sortu da')
    })
  },
  beforeRouteUpdate(to, from, next) {
    // Bide-parametroak aldatzen direnean
    // Adibidez, /erabiltzaile/1 -> /erabiltzaile/2
    // Konponentea birkargatu gabe exekutatzen da
    this.kargatuErabiltzailea(to.params.id)
    next()
  },
  beforeRouteLeave(to, from, next) {
    // Nabigazioa gertatu aurretik
    // Adibidez, aldaketak gorde gabe daudela egiaztatu
    if (this.aldaketakGordeGabe) {
      if (confirm('Aldaketak gorde gabe zaude. Ziur zaude irten nahi duzula?')) {
        next()
      } else {
        next(false)
      }
    } else {
      next()
    }
  }
}
</script>
```

## Bide-aurkibideak (Nested Routes)

```js
const routes = [
  {
    path: '/foroa',
    component: () => import('../views/Foroa.vue'),
    children: [
      {
        // /foroa -> Foroa.vue-ren barruan <router-view> baten bidez bistaratu
        path: '',
        component: () => import('../views/Foroa/Hasiera.vue')
      },
      {
        // /foroa/gaiak
        path: 'gaiak',
        component: () => import('../views/Foroa/Gaiak.vue')
      },
      {
        // /foroa/gaiak/:id
        path: 'gaiak/:id',
        component: () => import('../views/Foroa/Gaia.vue'),
        children: [
          {
            // /foroa/gaias/:id/iruzkinak
            path: 'iruzkinak',
            component: () => import('../views/Foroa/Iruzkinak.vue')
          },
          {
            // /foroa/gaias/:id/editatu
            path: 'editatu',
            component: () => import('../views/Foroa/EditatuGaia.vue'),
            meta: { requiresAuth: true }
          }
        ]
      }
    ]
  }
]
```

## Ariketa Praktikoa

Sortu blog-aplikazio bat Vue Router erabiliz:

1. Sortu hurrengo orrialdeak:
   - Hasiera (mezu berrien zerrenda)
   - Mezua ikusi (bakoitzarentzako orrialde espezifikoa)
   - Kategoriak (kategoria bakoitzaren araberako mezuak)
   - Erabiltzailearen profila
   - Kudeaketa-panela (adminentzako)

2. Inplementatu autentifikazio-sistema:
   - Saio-hasiera eta erregistroa
   - Baimendutako orrialde pribatuak
   - Rolen bidezko baimenak (admin, idazlea, irakurlea)

3. Gehitu bilaketa-funtzionalitatea query parametroekin

4. Inplementatu orri-kopurua eta iragazkiak

5. Sortu nabigazio-menua aktiboen estekekin

6. Gehitu kargatze-egoerak eta errore-kudeaketa

7. Inplementatu nabigazio-iragazkiak:
   - Erabiltzailea saioa hasita ez badago, bideratu saio-hasierara
   - Admin orrialdeetarako sarbidea mugatu
   - Aldaketak gorde gabe daudenean abisua erakutsi

8. Sortu orrialde anidatuak foro baten antzeko egitura batean

9. Inplementatu URL parametroak eta query parametroak datuak partekatzeko

10. Gehitu nabigazio-istorioa eta "Atzera" botoiaren portaera pertsonalizatua
