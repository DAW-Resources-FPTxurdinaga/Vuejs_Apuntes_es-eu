# Behatzaileak Vue-n

## Sarrera

Behatzaileek (`watchers`) datu-aldaketak behatzen dituzte eta erantzun pertsonalizatuak exekutatzeko aukera ematen dute. Kalkulatutako propietateen antzekoak dira, baina datu-aldaketen ondoriozko efektu sekundarioetarako egokiagoak dira.

## Oinarrizko Erabilera

```vue
<template>
  <div>
    <h2>Galdera eta Erantzuna</h2>
    <input v-model="galdera" placeholder="Zer da zure galdera?" />
    <p>Erantzuna: {{ erantzuna }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      galdera: '',
      erantzuna: 'Galdera egin mesedez...',
      erantzunak: {
        'kaixo': 'Kaixo!',
        'zer moduz': 'Oso ondo, eskerrik asko!',
        'zer da zure izena': 'Nire izena Vue da!',
        'agur': 'Agur! Ikusi arte!'
      },
      debounceTimeout: null
    }
  },
  watch: {
    // Behatu 'galdera' datu-aldagaia
    galdera(berria, zaharra) {
      console.log(`Galdera aldatu da: "${zaharra}" -> "${berria}"`)
      
      // Debounce aplikatu erantzun azkarregiak ekiditeko
      if (this.debounceTimeout) clearTimeout(this.debounceTimeout)
      
      this.debounceTimeout = setTimeout(() => {
        this.erantzuna = this.erantzunak[berria.toLowerCase()] || 
                        'Ez dut ulertu zure galdera. Beste galdera bat egin mesedez.'
      }, 500) // 500ms atzerapena
    }
  },
  created() {
    // Behatu objektu baten propietate jakin bat
    this.$watch(
      () => this.erantzuna,
      (berria, zaharra) => {
        console.log(`Erantzuna aldatu da: "${zaharra}" -> "${berria}"`)
      }
    )
  }
}
</script>
```

## Objektu eta Matrizeen Behaketa Sakona

```vue
<template>
  <div>
    <h2>Erabiltzaile Profila</h2>
    <div>
      <label>Izena: <input v-model="erabiltzailea.izena"></label>
    </div>
    <div>
      <label>Adina: <input type="number" v-model.number="erabiltzailea.adina"></label>
    </div>
    <div>
      <h3>Hizkuntzak:</h3>
      <div v-for="(hizkuntza, index) in erabiltzailea.hizkuntzak" :key="index">
        <input v-model="erabiltzailea.hizkuntzak[index]">
        <button @click="ezabatuHizkuntza(index)">Ezabatu</button>
      </div>
      <button @click="gehituHizkuntza">Hizkuntza Gehitu</button>
    </div>
    <div>
      <h3>Aldaketak:</h3>
      <ul>
        <li v-for="(aldaketa, index) in aldaketak" :key="index">{{ aldaketa }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      erabiltzailea: {
        izena: 'Pertsona',
        adina: 25,
        hizkuntzak: ['Euskara', 'Gaztelania']
      },
      aldaketak: []
    }
  },
  watch: {
    // Objektu baten barne-aldaketak behatu (sakonera handiarekin)
    erabiltzailea: {
      handler(berria, zaharra) {
        this.aldaketak.unshift(`Erabiltzailea eguneratu da: ${JSON.stringify(berria)}`)
      },
      deep: true, // Sakon-behaketa aktibatu
      immediate: true // Exekutatu berehala sortzean
    },
    // Propietate jakin baten aldaketak behatu
    'erabiltzailea.izena': {
      handler(berria, zaharra) {
        this.aldaketak.unshift(`Izena aldatu da: "${zaharra}" -> "${berria}"`)
      }
    },
    // Matrize baten tamaina behatu
    'erabiltzailea.hizkuntzak': {
      handler(berria, zaharra) {
        this.aldaketak.unshift(`Hizkuntza-kopurua: ${berria.length} (Aurretik: ${zaharra?.length || 0})`)
      },
      deep: true
    }
  },
  methods: {
    gehituHizkuntza() {
      this.erabiltzailea.hizkuntzak.push('')
    },
    ezabatuHizkuntza(index) {
      this.erabiltzailea.hizkuntzak.splice(index, 1)
    }
  }
}
</script>

<style scoped>
input {
  margin: 5px;
  padding: 5px;
}
button {
  margin: 5px;
  padding: 5px 10px;
  cursor: pointer;
}
ul {
  max-height: 200px;
  overflow-y: auto;
  border: 1px solid #ddd;
  padding: 10px;
  list-style-type: none;
}
li {
  padding: 5px;
  border-bottom: 1px solid #eee;
}
</style>
```

## $watch API-a

```vue
<template>
  <div>
    <h2>Denbora Errealeko Bilaketa</h2>
    <input v-model="bilaketaTerminoa" placeholder="Bilatu..." />
    <div v-if="kargatzen">Bilatzen...</div>
    <ul v-else>
      <li v-for="(emaitza, index) in emaitzak" :key="index">
        {{ emaitza }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      bilaketaTerminoa: '',
      emaitzak: [],
      kargatzen: false,
      bilaketaZerbitzua: null
    }
  },
  created() {
    // $watch erabiliz behatzaile bat sortu
    this.bilaketaZerbitzua = this.$watch(
      // Behatu beharreko adierazpena
      () => this.bilaketaTerminoa,
      
      // Aldaketaren kudeatzailea
      async (berria, zaharra) => {
        if (!berria.trim()) {
          this.emaitzak = []
          return
        }
        
        this.kargatzen = true
        
        try {
          // Simulatu API deialdi bat
          await new Promise(resolve => setTimeout(resolve, 1000))
          
          // Hemen benetako API deialdia egingo genuke
          this.emaitzak = [
            `Emaitza 1: ${berria}`,
            `Emaitza 2: ${berria} berriro`,
            `Beste emaitza bat: ${berria.toUpperCase()}`
          ]
        } catch (error) {
          console.error('Errorea bilaketan:', error)
          this.emaitzak = ['Errorea gertatu da emaitzak kargatzean']
        } finally {
          this.kargatzen = false
        }
      },
      
      // Aukerak
      {
        immediate: false, // Ez exekutatu sortzean
        deep: false      // Ez da behar testu-soileko datu bat denez
      }
    )
  },
  beforeUnmount() {
    // Desaktibatu behatzailea konponentea suntsitzean
    if (this.bilaketaZerbitzua) {
      this.bilaketaZerbitzua()
    }
  }
}
</script>
```

## Ariketa Praktikoa

Sortu erabiltzaile-interfaze interaktiboa datu-analisirako:

1. Sortu datu-multzo interaktibo bat (adibidez, salmentak, erabiltzaileak, produktuak)
2. Inplementatu behatzaileak datuak iragazteko eta ordenatzeko
3. Gehitu denbora-errealeko eguneraketak simulatzeko aukera
4. Sortu dashboard-a datu-aldaketak bistaratzen dituen grafikoekin
5. Inplementatu desberdintasun-detekzioa (aldaketak nabarmentzea)
6. Gehitu deshacer/redo funtzionalitatea
7. Optimizatu errendimendua behatzaileak erabiliz (adibidez, debounce, throttle)
