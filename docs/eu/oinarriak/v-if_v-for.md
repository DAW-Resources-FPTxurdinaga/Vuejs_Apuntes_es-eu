# 2.3 Baldintzak eta Begiztak

## v-if, v-else-if, v-else deribatuak

Baldintzazko deribatzaileek elementuak errendatzea ahalbidetzen dute baldintza baten arabera.

### Erabilera oinarrizkoa:

```html
<div v-if="puntuazioa >= 90">
  Bikain!
</div>
<div v-else-if="puntuazioa >= 70">
  Ondo egin duzu
</div>
<div v-else>
  Jarraitu praktikatzen
</div>
```

### v-if vs v-show

- `v-if`: Baldintzaz errendatzen du elementua (DOM-era gehitzen/ezabatzen du)
- `v-show`: Beti errendatzen du elementua, baina CSS erabiltzen du erakusteko/ezkutatzeko

```html
<div v-if="erakutsi">v-if erabiliz</div>
<div v-show="erakutsi">v-show erabiliz</div>
```

## v-for deribatzailea

Bektoreetan edo objektuetan iteratzeko aukera ematen du.

### Bektoreetan iteratzea:

```html
<ul>
  <li v-for="(zeregin, index) in zereginak" :key="zeregin.id">
    {{ index + 1 }}. {{ zeregin.testua }}
  </li>
</ul>
```

### Objektuetan iteratzea:

```html
<ul>
  <li v-for="(balioa, gakoa) in erabiltzailea" :key="gakoa">
    {{ gakoa }}: {{ balioa }}
  </li>
</ul>
```

### Indizearekin objektuetan iteratzea:

```html
<template>
  <div>
    <h3>Erabiltzailearen informazioa indizearekin:</h3>
    <ul>
      <li v-for="(balioa, gakoa, index) in erabiltzailea" :key="gakoa">
        #{{ index + 1 }} - {{ gakoa }}: {{ balioa }}
        <span v-if="index === Object.keys(erabiltzailea).length - 1"> (Azken elementua)</span>
      </li>
    </ul>
  </div>
</template>

<script setup>
const erabiltzailea = {
  izena: 'Ane',
  adina: 28,
  email: 'ane@adibidea.eus',
  rola: 'garatzailea',
  aktiboa: true
};
</script>
```

Adibide honetan:
- `balioa`: Propietate bakoitzaren balioa du
- `gakoa`: Propietatearen izena du
- `index`: Uneko iterazioaren indizea du

## Ariketa Praktikoa: Zereginen Zerrenda Aurreratua

Ariketa honetan, honakoak dituen zereginen zerrenda bat sortuko dugu:
- Zereginak iragaztea (Guztiak, Zabalik daudenak, Egindakoak)
- Zereginak lerroan editatzea
- Datuen iraunkortasuna localStorage-n
- Zereginen kontadoreak
- Intuitiboa den interfazea

### SFC Osagai Bakarreko Fitxategia

```vue
<template>
  <div class="zereginak-container">
    <h1>Zereginen Zerrenda Aurreratua</h1>
    
    <!-- Zeregin berria gehitzeko formularioa -->
    <div class="form-group">
      <input 
        v-model="zereginBerria" 
        @keyup.enter="gehituZeregina"
        placeholder="Zeregin berria..."
        class="zeregina-input"
      >
      <button @click="gehituZeregina" class="btn btn-gehitu">
        Gehitu
      </button>
    </div>
    
    <!-- Iragazkiak -->
    <div class="iragazkiak">
      <button 
        v-for="iragazkia in iragazkiak" 
        :key="iragazkia"
        @click="unezkoIragazkia = iragazkia"
        :class="['btn-iragazkia', { 'aktiboa': iragazkia === unekoaIragazkia }]"
      >
        {{ iragazkia }}
      </button>
    </div>
    
    <!-- Zereginen zerrenda -->
    <ul class="zereginen-zerrenda">
      <li 
        v-for="zeregina in iragaztutakoZereginak" 
        :key="zeregina.id"
        :class="['zeregina-item', { 'eginda': zeregina.eginda }]"
      >
        <input 
          type="checkbox" 
          v-model="zeregina.eginda"
          @change="eguneratuZeregina(zeregina)"
          class="zeregina-checkbox"
        >
        <span class="zeregina-testua" @dblclick="editatuZeregina(zeregina)">
          {{ zeregina.testua }}
        </span>
        <div class="ekintzak-zeregina">
          <button class="btn btn-editatu" @click="editatuZeregina(zeregina)">
            ✏️
          </button>
          <button class="btn btn-ezabatu" @click="ezabatuZeregina(zeregina.id)">
            🗑️
          </button>
        </div>
        
        <!-- Edizioa (biko klik egitean agertzen da) -->
        <input 
          v-if="zeregina.editatzen"
          v-model="zeregina.testua"
          @blur="gordeEdizioa(zeregina)"
          @keyup.enter="gordeEdizioa(zeregina)"
          @keyup.esc="utziEdizioa(zeregina)"
          v-focus
          class="sarrera-editatu"
        >
      </li>
    </ul>
    
    <!-- Kontadoreak -->
    <div class="kontadoreak">
      <p>Guztira: {{ zereginak.length }}</p>
      <p>Eginda: {{ egindakoZereginak }}</p>
      <p>Zabalik: {{ zabalikDaudenZereginak }}</p>
    </div>
    
    <!-- Mezua zereginik ez dagoenean -->
    <p v-if="zereginak.length === 0" class="zereginik-ez">
      Ez dago zereginik. Gehitu bat hasteko!
    </p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

// Egoera erreaktiboa
const zereginBerria = ref('');
const zereginak = ref(JSON.parse(localStorage.getItem('zereginak')) || []);
const unekoaIragazkia = ref('Guztiak');
const zereginakEditatuAurrekoan = {};

// Konstanteak
const iragazkiak = ['Guztiak', 'Zabalik', 'Eginda'];

// Kalkulatutako propietateak
const egindakoZereginak = computed(() => 
  zereginak.value.filter(z => z.eginda).length
);

const zabalikDaudenZereginak = computed(() => 
  zereginak.value.filter(z => !z.eginda).length
);

const iragaztutakoZereginak = computed(() => {
  switch (unekoaIragazkia.value) {
    case 'Zabalik':
      return zereginak.value.filter(z => !z.eginda);
    case 'Eginda':
      return zereginak.value.filter(z => z.eginda);
    default:
      return zereginak.value;
  }
});

// Metodoak
function gehituZeregina() {
  if (zereginBerria.value.trim() === '') return;
  
  zereginak.value.push({
    id: Date.now(),
    testua: zereginBerria.value.trim(),
    eginda: false,
    editatzen: false
  });
  
  zereginBerria.value = '';
  gordeZereginak();
}

function ezabatuZeregina(id) {
  if (confirm('Ziur zaude zeregina ezabatu nahi duzula?')) {
    zereginak.value = zereginak.value.filter(z => z.id !== id);
    gordeZereginak();
  }
}

function editatuZeregina(zeregina) {
  zereginakEditatuAurrekoan[zeregina.id] = zeregina.testua;
  zeregina.editatzen = true;
}

function gordeEdizioa(zeregina) {
  if (zeregina.testua.trim() === '') {
    zeregina.testua = zereginakEditatuAurrekoan[zeregina.id] || '';
  }
  zeregina.editatzen = false;
  gordeZereginak();
}

function utziEdizioa(zeregina) {
  zeregina.testua = zereginakEditatuAurrekoan[zeregina.id] || zeregina.testua;
  zeregina.editatzen = false;
}

function eguneratuZeregina(zeregina) {
  gordeZereginak();
}

function gordeZereginak() {
  localStorage.setItem('zereginak', JSON.stringify(zereginak.value));
}

// Edizioan automatikoki fokuratzeko deribatu pertsonalizatua
const vFocus = {
  mounted: (el) => el.focus()
};

// Kargatu gordetako zereginak hasieratzean
onMounted(() => {
  const gordetakoZereginak = localStorage.getItem('zereginak');
  if (gordetakoZereginak) {
    zereginak.value = JSON.parse(gordetakoZereginak);
  }
});
</script>

<style scoped>
/* Edukiontzi nagusiaren estiloak */
.zereginak-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

/* Formularioaren estiloak */
.form-group {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.zeregina-input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

/* Botoien estiloak */
.btn {
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.btn-gehitu {
  background-color: #4CAF50;
  color: white;
}

.btn-gehitu:hover {
  background-color: #45a049;
}

/* Iragazkien estiloak */
.iragazkiak {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.btn-iragazkia {
  background-color: #f1f1f1;
  padding: 5px 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  cursor: pointer;
}

.btn-iragazkia.aktiboa {
  background-color: #4CAF50;
  color: white;
  border-color: #45a049;
}

/* Zereginen zerrendaren estiloak */
.zereginen-zerrenda {
  list-style: none;
  padding: 0;
}

.zeregina-item {
  display: flex;
  align-items: center;
  padding: 10px;
  margin-bottom: 8px;
  background-color: #f9f9f9;
  border-radius: 4px;
  transition: background-color 0.3s;
}

.zeregina-item:hover {
  background-color: #f1f1f1;
}

.zeregina-item.eginda {
  opacity: 0.7;
}

.zeregina-checkbox {
  margin-right: 10px;
}

.zeregina-testua {
  flex: 1;
  margin-right: 10px;
  cursor: pointer;
}

.zeregina-item.eginda .zeregina-testua {
  text-decoration: line-through;
  color: #888;
}

.ekintzak-zeregina {
  display: flex;
  gap: 5px;
}

.btn-editatu {
  background-color: #2196F3;
  color: white;
}

.btn-ezabatu {
  background-color: #f44336;
  color: white;
}

.btn-editatu:hover, .btn-ezabatu:hover {
  opacity: 0.9;
}

/* Edizioaren sarrerarako estiloak */
.sarrera-editatu {
  flex: 1;
  padding: 5px;
  margin-left: 10px;
  border: 1px solid #2196F3;
  border-radius: 4px;
}

/* Kontadoreen estiloak */
.kontadoreak {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
  padding: 10px;
  background-color: #f5f5f5;
  border-radius: 4px;
}

/* Zereginik ez dagoenean mezuarentzako estiloak */
.zereginik-ez {
  text-align: center;
  color: #888;
  font-style: italic;
  margin-top: 20px;
}
</style>
```

## Kodearen Azalpena

1. **Osagaiaren Egitura**:
    - `<script setup>` sintaxia erabili dugu kodearen antolaketa hobea lortzeko.
    - Osagaia hiru atal nagusitan banatuta dago: txantiloia, scripta eta estiloak.

2. **Funtzionalitate Nagusiak**:
    - **Zereginak gehitzea**: Sarrera hutsaren balidazioarekin.
    - **Egindako gisa markatzea**: Checkbox-ekin `v-model` erabiliz.
    - **Zereginak editatzea**: Biko klikarekin edo edizio botoiarekin.
    - **Zereginak ezabatzea**: Ezabatu aurretik baieztapenarekin.
    - **Zereginak iragaztea**: Guztiak, Zabalik daudenak edo Egindakoak.
    - **Iraunkortasuna**: Zereginak `localStorage`-n gordetzen dira.

3. **Ezaugarri Aurreratuak**:
    - `v-focus` deribatu pertsonalizatua edizioan automatikoki fokua jartzeko.
    - `computed` erabiliz iragazitako zereginak eta kontadoreak kalkulatzeko.
    - Teklatuaren gertaerak kudeatzea (Sartu, Ihes) erabilgarritasun hobea lortzeko.
    - Erantzunkorrak diren estiloak eta erabiltzailearen elkarrekintzetarako ikusizko feedback-a.

4. **Praktika Onak**:
    - `:key` erabiliz `v-for` begiztetarako errendatze eraginkorra lortzeko.
    - Logika, aurkezpena eta estiloen arteko banaketa argia.
    - Kode garbia eta ondo dokumentatua.

Ariketa honek `v-if`, `v-for` eta `v-model` deribatuen erabilera erakusten du testuinguru praktiko batean, nola konbinatu daitezkeen erakutsiz Vue.js-eko erabiltzaile-interfaze interaktibo eta erantzunkorrak sortzeko.
[Hurrengoa: kalkulatutako propietateak →](kalkulatutako_propietateak.md)