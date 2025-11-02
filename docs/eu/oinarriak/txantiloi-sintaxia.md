# 📝 Txantiloi-sintaxia Vue 3-n

Vue-ren txantiloi-sintaxia HTML-aren hedapena da, eta modu deklaratiboan lotzen du errendatutako DOM-a Vue instantziaren datuekin. Vue-ren txantiloien HTML baliagarria da, eta HTML-eko analizatzaileek eta nabigatzaileek analiza dezakete.

## Testuaren Interpolazioa

Datu-loturarik oinarrizkoena testu-interpolazioa da, bi giltza kizkurren bidez ("Mustache" gisa ere ezaguna):

```html
<span>Mezua: {{ mezua }}</span>
```

## Zuzentarauak

Zuzentarauak `v-` aurrizkia duten atributu bereziak dira. Efektu-kolpeak eragiten dituzte DOM-ean, haien adierazpenaren balioa aldatzen denean.

### v-bind

Atributu edo osagai-propietate bat edo gehiago modu dinamikoan lotzen ditu:

```html
<div v-bind:id="dinamikoId"></div>
<!-- Laburdura -->
<div :id="dinamikoId"></div>
```

### v-model

Bi noranzkotako lotura bat sortzen du formulario-elementu edo osagai batean:

```html
<input v-model="mezua" placeholder="Editatu nazazu">
<p>Mezua hau da: {{ mezua }}</p>
```

### v-if, v-else-if, v-else

Elementu bat baldintzaz errendatzen du, adierazpenaren egiazkotasunaren arabera:

```html
<div v-if="mota === 'A'">
  A
</div>
<div v-else-if="mota === 'B'">
  B
</div>
<div v-else>
  Ez A ez B
</div>
```

### v-for

Elementua edo txantiloi-blokea behin baino gehiagotan errendatzen du, datuen iturriaren arabera:

```html
<ul>
  <li v-for="elementua in elementuak" :key="elementua.id">
    {{ elementua.testua }}
  </li>
</ul>
```

### v-on

Gertaera-atzizki bat gehitzen dio elementuari:

```html
<button v-on:click="handitu">Handitu</button>
<!-- Laburdura -->
<button @click="handitu">Handitu</button>
```

## Atributu Dinamikoak

JavaScript adierazpen bat erabil dezakezu zuzentarau-argumentu baten barruan, kortxeteen artean jarriz:

```html
<button :[gakoa]="balioa"></button>
```

## JavaScript Adierazpenak

Datu-lotura guztietan, Vue.js-ek JavaScript adierazpen osoak onartzen ditu:

```html
{{ zenbakia + 1 }}
{{ ondoDago ? 'BAI' : 'EZ' }}
{{ mezua.split('').reverse().join('') }}
<div :id="'zerrenda-' + id"></div>
```

## Zuzentarau Pertsonalizatuak

Zuzenean DOM-a manipulatzeko zuzentarau pertsonalizatuak erregistra ditzakezu:

```javascript
const app = Vue.createApp({})

app.directive('nabarmendu', {
  beforeMount(el, binding) {
    el.style.backgroundColor = binding.value || 'horia'
  }
})
```

```html
<p v-nabarmendu="'gorria'">Testu hau gorriz nabarmenduko da!</p>
```

## Osagai Dinamikoak

Osagaiak dinamikoki txandatu ditzakezu `<component>` elementua eta `is` atributua erabiliz:

```html
<component :is="unekoOsagaia"></component>
```

## Hurrengo Urratsak

Orain Vue-ren txantiloi-sintaxiari buruz ikasi duzunez, hurrengo atalean Vue 3-ko erantzunkortasun-sistemari buruz sakonduko dugu.

[👉 Hurrengoa: Erantzunkortasuna Vue 3-n](./erantzunkortasuna.md)
