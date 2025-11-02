# 2.2 v-bind eta v-model

## v-bind sakontasunean

`v-bind` zuzentzaileak (edo bere `:` laburdurak) HTML atributu edo osagaien propietateak dinamikoki lotzeko aukera ematen digu.

### Erabilera oinarrizkoa:

```vue
<template>
  <!-- Sintaxi osoa -->
  <a v-bind:href="url">Esteka</a>

  <!-- Laburdura -->
  <a :href="url">Esteka</a>
</template>

<script setup>
const url = 'https://www.ejemplo.eus';
</script>
```

### Klase eta estilo dinamikoak:

```vue
<template>
  <!-- Klase dinamikoak -->
  <div :class="{ 'aktiboa': aktiboaDago, 'testu-gorria': erroreaDauka }"></div>

  <!-- Estilo lerroan -->
  <div :style="{ color: testuKolorea, fontSize: letraTamaina + 'px' }"></div>
</template>

<script setup>
const aktiboaDago = true;
const erroreaDauka = false;
const testuKolorea = 'blue';
const letraTamaina = 16;
</script>
```

## v-model - Bikoizteko lotura

`v-model`-ek formulario-elementuetan bikoizteko lotura sortzen du.

### Sarrera oinarrizkoekin:

```vue
<template>
  <div>
    <!-- Eremua .lazy aldatzailearekin, fokua galtzean eguneratzeko -->
    <div>
      <label>Izena (lazy):</label>
      <input v-model.lazy="izena" placeholder="Idatzi zure izena">
      <p>Izena fokua galtzean eguneratuta: {{ izena }}</p>
    </div>
    
    <!-- Eremua .trim aldatzailearekin, zuriuneak kentzeko -->
    <div>
      <label>Erabiltzailea (trim):</label>
      <input v-model.trim="erabiltzailea" placeholder="Zuriune gabe hasieran/amaieran">
      <p>Erabiltzailea ({{ erabiltzailea.length }} karaktere): {{ erabiltzailea }}</p>
    </div>
    
    <!-- Eremua aldatzaile anitzekin -->
    <div>
      <label>Bilaketa (lazy + trim):</label>
      <input v-model.lazy.trim="bilaketa" placeholder="Idatzi eta sakatu tab">
      <p>Eguneratutako bilaketa: {{ bilaketa }}</p>
    </div>
    
    <!-- Hautaketa lehenetsiarekin -->
    <div>
      <select v-model="hautatua">
        <option disabled value="">Aukeratu aukera bat</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
      </select>
      <p>Hautatua: {{ hautatua || 'Bat ere ez' }}</p>
    </div>
    
    <!-- Egoeradun kontrol-laukitxoa -->
    <div>
      <input type="checkbox" id="egiaztatua" v-model="egiaztatua">
      <label for="egiaztatua">
        {{ egiaztatua ? 'Aktibatuta' : 'Desaktibatuta' }}
      </label>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const izena = ref('');
const erabiltzailea = ref('');
const bilaketa = ref('');
const hautatua = ref('');
const egiaztatua = ref(false);
</script>

<style scoped>
div {
  margin-bottom: 1rem;
}

label {
  display: inline-block;
  min-width: 200px;
  margin-right: 10px;
}

input, select {
  padding: 5px;
  margin-right: 10px;
}

p {
  margin: 5px 0 0 210px;
  color: #666;
  font-style: italic;
}
</style>
```

### v-model-eko aldatzaileak:

- `.lazy`: Balioa eguneratzen du `change` gertaeraren ondoren
- `.number`: Sarrera zenbakira bihurtzen du
- `.trim`: Zuriuneak kentzen ditu hasieran eta bukaeran

```vue
<template>
  <input v-model.lazy="mezua">
  <input v-model.number="adin" type="number">
  <input v-model.trim="izena">
</template>

<script setup>
const mezua = ref('');
const adin = ref(0);
const izena = ref('');
</script>
```

## Ariketa Praktikoa: Erregistro Inprimakia

Sortu erregistro inprimaki bat balidazio oinarrizkoekin:

1. Izena (derrigorrezkoa, gutxienez 3 karaktere)
2. Posta elektronikoa (formatua baliozkoa izan behar da)
3. Pasahitza (gutxienez 6 karaktere)
4. Pasahitza berretsi (bat etorri behar da)
5. Erabilera-baldintzak (derrigorrezko aukera-koadroa)

```html
<template>
  <form @submit.prevent="bidaliInprimakia">
    <div>
      <label>Izena:</label>
      <input v-model="inprimakia.izena" 
             :class="{ 'errorea': erroreak.izena }">
      <span v-if="erroreak.izena" class="errore-testua">{{ erroreak.izena }}</span>
    </div>
    
    <div>
      <label>Posta elektronikoa:</label>
      <input v-model="inprimakia.posta" type="email"
             :class="{ 'errorea': erroreak.posta }">
      <span v-if="erroreak.posta" class="errore-testua">{{ erroreak.posta }}</span>
    </div>
    
    <div>
      <label>Pasahitza:</label>
      <input v-model="inprimakia.pasahitza" type="password"
             :class="{ 'errorea': erroreak.pasahitza }">
      <span v-if="erroreak.pasahitza" class="errore-testua">{{ erroreak.pasahitza }}</span>
    </div>
    
    <div>
      <label>Berretsi pasahitza:</label>
      <input v-model="inprimakia.pasahitzaBerretsi" type="password"
             :class="{ 'errorea': erroreak.pasahitzaBerretsi }">
      <span v-if="erroreak.pasahitzaBerretsi" class="errore-testua">{{ erroreak.pasahitzaBerretsi }}</span>
    </div>
    
    <div>
      <label>
        <input type="checkbox" v-model="inprimakia.baldintzak">
        Erabilera-baldintzak onartzen ditut
      </label>
      <span v-if="erroreak.baldintzak" class="errore-testua">
        {{ erroreak.baldintzak }}
      </span>
    </div>
    
    <button type="submit" :disabled="bidaltzen">
      {{ bidaltzen ? 'Bidaltzen...' : 'Bidali' }}
    </button>
  </form>
  
  <!-- Datuen aurrebista -->
  <div v-if="bidalita" class="bidalitako-datuak">
    <h3>Bidalitako datuak:</h3>
    <pre>{{ JSON.stringify(inprimakia, null, 2) }}</pre>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue';

const inprimakia = reactive({
  izena: '',
  posta: '',
  pasahitza: '',
  pasahitzaBerretsi: '',
  baldintzak: false
});

const bidaltzen = ref(false);
const bidalita = ref(false);

function balioztatuEmail(email) {
  const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return re.test(email);
}

function balioztatuInprimakia() {
  let baliozkoa = true;
  
  // Garbitu aurreko erroreak
  Object.keys(erroreak.value).forEach(gakoa => delete erroreak.value[gakoa]);
  
  // Balioztatu izena
  if (inprimakia.izena.length < 3) {
    erroreak.value.izena = 'Izenak gutxienez 3 karaktere izan behar ditu';
    baliozkoa = false;
  }
  
  // Balioztatu posta elektronikoa
  if (!inprimakia.posta) {
    erroreak.value.posta = 'Posta elektronikoa derrigorrezkoa da';
    baliozkoa = false;
  } else if (!balioztatuEmail(inprimakia.posta)) {
    erroreak.value.posta = 'Sartu posta elektroniko baliagarri bat';
    baliozkoa = false;
  }
  
  // Balioztatu pasahitza
  if (inprimakia.pasahitza.length < 6) {
    erroreak.value.pasahitza = 'Pasahitzak gutxienez 6 karaktere izan behar ditu';
    baliozkoa = false;
  }
  
  // Balioztatu pasahitzen bat etorrera
  if (inprimakia.pasahitza !== inprimakia.pasahitzaBerretsi) {
    erroreak.value.pasahitzaBerretsi = 'Pasahitzak ez datoz bat';
    baliozkoa = false;
  }
  
  // Balioztatu baldintzak
  if (!inprimakia.baldintzak) {
    erroreak.value.baldintzak = 'Erabilera-baldintzak onartu behar dituzu';
    baliozkoa = false;
  }
  
  return baliozkoa;
}

const erroreak = computed(() => ({}));

async function bidaliInprimakia() {
  if (balioztatuInprimakia()) {
    bidaltzen.value = true;
    
    // Simulatu zerbitzarira bidalketa
    await new Promise(resolve => setTimeout(resolve, 1000));
    
    console.log('Bidalitako datuak:', inprimakia);
    bidaltzen.value = false;
    bidalita.value = true;
  }
}
</script>

<style scoped>
.errorea {
  border-color: #ff3860;
}

.errore-testua {
  color: #ff3860;
  font-size: 0.8em;
  margin-top: 0.25rem;
  display: block;
}

button:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}

.bidalitako-datuak {
  margin-top: 2rem;
  padding: 1rem;
  background-color: #f5f5f5;
  border-radius: 4px;
}

form > div {
  margin-bottom: 15px;
}

label {
  display: inline-block;
  min-width: 200px;
}

input[type="text"],
input[type="email"],
input[type="password"] {
  padding: 5px;
  width: 200px;
}

button {
  margin-top: 10px;
  padding: 5px 15px;
  cursor: pointer;
}
</style>
```

## Hurrengo urratsa

Orain `v-bind` eta `v-model`-ekin nola lan egin ikusi dugunez, hurrengo atalean `v-if` eta `v-for` erabiliko ditugu errendatze baldintzatu eta zerrenda dinamikoak sortzeko.

[Hurrengoa: V-if eta V-for →](v-if_v-for.md) 