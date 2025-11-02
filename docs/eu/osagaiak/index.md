# Osagaiak Vue-n

## Sarrera

Vue-ren osagaiak aplikazio modular eta berrerabilgarriak sortzeko aukera ematen dute. Osagai bakoitza instantzia berrerabilgarri bat da, bere datu, metododun eta estilo propioak dituena.

## Osagai Baten Oinarriak

### Osagai Baten Definizioa

```js
// Erregistratu osagai global bat
const App = {
  // Datuak
  data() {
    return {
      mezua: 'Kaixo, Vue!',
      kontadorea: 0
    }
  },
  
  // Metodoak
  methods: {
    gehitu() {
      this.kontadorea++
    }
  },
  
  // Plantila
  template: `
    <div class="osagaia">
      <h2>{{ mezua }}</h2>
      <p>Kontadorea: {{ kontadorea }}</p>
      <button @click="gehitu">Gehitu</button>
    </div>
  `
}

// Aplikazioa sortu eta muntatu
const app = Vue.createApp({})
app.component('nire-osagaia', App)
app.mount('#app')
```

### Osagaiak HTML-ean Erabiltzea

```html
<div id="app">
  <nire-osagaia></nire-osagaia>
  <nire-osagaia></nire-osagaia>
  <nire-osagaia></nire-osagaia>
</div>
```

## Osagaien Arteko Komunikazioa

### Prop-ak: Gurasotik Seme-Alabara Datuak Bidaltzea

```js
// Seme-alabaren osagaia
const BlogPost = {
  props: ['titulua', 'edukia'],
  template: `
    <div class="blog-post">
      <h3>{{ titulua }}</h3>
      <div v-html="edukia"></div>
    </div>
  `
}

// Gurasoaren osagaia
const App = {
  components: { BlogPost },
  data() {
    return {
      mezuak: [
        { id: 1, titulua: 'Lehenengo Mezua', edukia: 'Hau da lehenengo mezua' },
        { id: 2, titulua: 'Bigarren Mezua', edukia: 'Hau da bigarren mezua' }
      ]
    }
  },
  template: `
    <div>
      <h1>Nire Blog-a</h1>
      <blog-post
        v-for="mezua in mezuak"
        :key="mezua.id"
        :titulua="mezua.titulua"
        :edukia="mezua.edukia"
      ></blog-post>
    </div>
  `
}
```

### Gertaerak: Seme-Alabetik Gurasora Mezuak Bidaltzea

```js
// Seme-alabaren osagaia
const ButtonCounter = {
  template: `
    <button @click="gehitu">
      Klikatu {{ count }} aldiz
    </button>
  `,
  data() {
    return {
      count: 0
    }
  },
  methods: {
    gehitu() {
      this.count++
      // Bidali gertakizuna gurasoari
      this.$emit('gehitu', this.count)
    }
  }
}

// Gurasoaren osagaia
const App = {
  components: { ButtonCounter },
  data() {
    return {
      guztira: 0
    }
  },
  methods: {
    gehituKontagailua(kopurua) {
      this.guztira = kopurua
    }
  },
  template: `
    <div>
      <p>Guztira klikak: {{ guztira }}</p>
      <button-counter @gehitu="gehituKontagailua"></button-counter>
    </div>
  `
}
```

## Slots

### Oinarrizko Erabilera

```js
// Alert osagaia
const AlertBox = {
  template: `
    <div class="alert">
      <div class="alert-content">
        <slot></slot>
      </div>
      <button class="alert-close" @click="$emit('close')">
        ×
      </button>
    </div>
  `
}

// Erabilera
const App = {
  components: { AlertBox },
  data() {
    return {
      showAlert: false
    }
  },
  template: `
    <div>
      <button @click="showAlert = true">Erakutsi Alert-a</button>
      
      <alert-box v-if="showAlert" @close="showAlert = false">
        <h3>Kontuz!</h3>
        <p>Hau alerta-mezu bat da.</p>
      </alert-box>
    </div>
  `
}
```

### Izeneko Slot-ak

```js
const BaseLayout = {
  template: `
    <div class="container">
      <header>
        <slot name="header"></slot>
      </header>
      <main>
        <slot></slot>
      </main>
      <footer>
        <slot name="footer"></slot>
      </footer>
    </div>
  `
}

// Erabilera
const App = {
  components: { BaseLayout },
  template: `
    <base-layout>
      <template #header>
        <h1>Nire Webgunea</h1>
      </template>
      
      <p>Eduki nagusia hemen doa.</p>
      
      <template #footer>
        <p>© 2023 Nire Enpresa</p>
      </template>
    </base-layout>
  `
}
```

## Dinamikoki Kargatutako Osagaiak

### Lazy Loading

```js
const AsyncComponent = () => ({
  // Kargatzen ari den konponentea (Promise bat itzuli behar du)
  component: import('./HeavyComponent.vue'),
  // Kargatzen ari denean erakutsiko den konponentea
  loading: LoadingComponent,
  // Errorea gertatzean erakutsiko den konponentea
  error: ErrorComponent,
  // Karga-konponentearen erakusteko atzerapena. Lehenetsia: 200ms
  delay: 200,
  // Denbora-muga milisegundutan. Honek gainditzen bada, errore-konponentea erakutsiko da
  timeout: 3000
})

const app = Vue.createApp({
  components: {
    'heavy-component': AsyncComponent
  },
  template: `
    <div>
      <h1>Nire Aplikazioa</h1>
      <suspense>
        <template #default>
          <heavy-component />
        </template>
        <template #fallback>
          <div>Kargatzen...</div>
        </template>
      </suspense>
    </div>
  `
})
```

## Ariketa Praktikoa

Sortu blog-sistema bat osagaiekin:

1. Sortu `BlogPost` osagaia titulu, egilea, data, edukia eta irudiarekin
2. Sortu `Comment` osagaia erabiltzailearen izena, data eta edukirekin
3. Sortu `Pagination` osagaia orrialdekatzerako
4. Sortu `SearchBar` osagaia bilaketak egiteko
5. Sortu `UserProfile` osagaia erabiltzailearen informazioa erakusteko
6. Inplementatu komentarioen erantzunak (reply)
7. Gehitu like/dislike funtzionalitatea
8. Sortu `TagList` osagaia etiketak erakusteko eta iragazteko
9. Inplementatu erabiltzaile-rolak (administratzailea, idazlea, irakurlea)
10. Sortu `RelatedPosts` osagaia antzeko mezuak erakusteko
