# Konponenteen Bizitza Zikloa

## Sarrera

Vue konponenteek bizitza-ziklo bat dute, sortzetik suntsitu arte. Ziklo honen puntu garrantzitsuenetan kode propioa exekutatu dezakezu.

## Bizitza-Zikloaren Diagrama

```
beforeCreate  -->  created  -->  beforeMount  -->  mounted
      |               |              |               |
      v               v              v               v
data()      methods  template     render()       DOM-a
      \               |              |               |
       \              v              v               v
        \         beforeUpdate  ->  updated  ->  nextTick()
         \              |              |
          \             v              v
           \------> beforeUnmount  ->  unmounted
```

## Bizitza-Zikloaren Hook-ak

### Sortzearen Hurrenean (Creation)

1. **beforeCreate**: Konponentea sortu aurretik exekutatzen da
2. **created**: Konponentea sortu ondoren exekutatzen da

```vue
<script>
export default {
  data() {
    return {
      mezua: 'Kaixo, Vue!',
      datuak: null
    }
  },
  beforeCreate() {
    console.log('beforeCreate - Datuak ez daude oraindik eskuragarri', this.mezua) // undefined
  },
  created() {
    console.log('created - Datuak eskuragarri', this.mezua) // 'Kaixo, Vue!'
    
    // API deialdiak eta datuak kargatzea
    this.kargatuDatuak()
  },
  methods: {
    async kargatuDatuak() {
      try {
        const erantzuna = await fetch('https://api.ejemplo.eus/datuak')
        this.datuak = await erantzuna.json()
      } catch (errorea) {
        console.error('Errorea datuak kargatzean:', errorea)
      }
    }
  }
}
</script>
```

### Muntatzearen Hurrenean (Mounting)

3. **beforeMount**: DOM-a muntatu aurretik exekutatzen da
4. **mounted**: DOM-a muntatu ondoren exekutatzen da

```vue
<template>
  <div ref="nireElementua">Elementu bat</div>
</template>

<script>
export default {
  beforeMount() {
    console.log('beforeMount - Oraindik ezin dugu DOM elementurik atzitu')
    console.log(this.$refs.nireElementua) // undefined
  },
  mounted() {
    console.log('mounted - Orain DOM elementua eskuragarri dago')
    console.log(this.$refs.nireElementua) // <div>Elementu bat</div>
    
    // Hirugarrenen API-ekin integrazioa
    this.mapaInprimatu()
  },
  methods: {
    mapaInprimatu() {
      // Adibidez, Google Maps inplementazioa
      // this.mapa = new google.maps.Map(this.$refs.mapa, { ... })
    }
  },
  beforeUnmount() {
    // Garbitu errekurtsoak
    // clearInterval(this.temporizador)
    // this.mapa = null
  }
}
</script>
```

### Eguneraketaren Hurrenean (Updating)

5. **beforeUpdate**: Datuak aldatzen direnean, baina DOM-a eguneratu aurretik
6. **updated**: Datuak eta DOM-a eguneratu ondoren

```vue
<template>
  <div>
    <p>Kontagailua: {{ kontadorea }}</p>
    <button @click="kontadorea++">Gehitu</button>
    <div ref="kontadoreElementua"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      kontadorea: 0,
      eguneraketaKopurua: 0
    }
  },
  beforeUpdate() {
    console.log('beforeUpdate - Kontadorea:', this.kontadorea)
    console.log('DOM-eko balioa:', this.$refs.kontadoreElementua.textContent)
  },
  updated() {
    this.eguneraketaKopurua++
    console.log('updated - DOM eguneratu da')
    console.log('DOM-eko balio berria:', this.$refs.kontadoreElementua.textContent)
    
    // Kontuz: updated hook-ean datuak aldatzeak beste eguneraketa bat eragin dezake
    // Hau saihestu behar da, bestela begizta infinitua sor daiteke
    // this.kontadore++ // Hau ez!
  }
}
</script>
```

### Desmuntatzearen Hurrenean (Unmounting)

7. **beforeUnmount**: Konponentea suntsitu aurretik
8. **unmounted**: Konponentea suntsitu ondoren

```vue
<script>
export default {
  data() {
    return {
      intervalId: null,
      kontadorea: 0
    }
  },
  created() {
    // Sortu denbora-eremuko eguneraketa
    this.intervalId = setInterval(() => {
      console.log('Tic-tac:', this.kontadorea++)
    }, 1000)
  },
  beforeUnmount() {
    console.log('beforeUnmount - Garbitu errekurtsoak')
    // Garbitu denbora-eremua
    if (this.intervalId) {
      clearInterval(this.intervalId)
      this.intervalId = null
    }
    
    // Beste garbiketa ekintzak
    // window.removeEventListener('scroll', this.handleScroll)
    // this.socket?.disconnect()
  },
  unmounted() {
    console.log('unmounted - Konponentea suntsitu da')
  }
}
</script>
```

## Beste Hook Garrantzitsu Batzuk

### errorCaptured

Hau atzizpi-konponente batean errore bat harrapatzen denean aktibatzen da.

```javascript
errorCaptured(err, instance, info) {
  // Errorea kudeatu
  console.error('Errorea harrapatuta:', err)
  console.log('Konponentearen instantzia:', instance)
  console.log('Errorearen informazioa:', info)
  
  // Errorea zabaltzea ekiditeko
  return false
}
```

### renderTracked eta renderTriggered

**Garapenerako** erabilgarriak, birtualaren DOM-aren berrerenderizazioak aztertzeko.

```javascript
renderTracked({ key, target, type }) {
  console.log(`renderTracked - ${type}:`, key, target)
},
renderTriggered({ key, target, type }) {
  console.log(`renderTriggered - ${type}:`, key, target)
}
```

## Ariketa Praktikoa

Sortu denbora-errealeko datuen bidezko panel bat:

1. Erabili `setInterval` datuak eguneratzeko
2. Erabili WebSocket konexio bat benetako denbora errealeko datuetarako
3. Inplementatu kargatze-egoerak kudeatzeko logika
4. Erabili `beforeUnmount` konexioak eta denbora-eremuak garbitzeko
5. Gehitu errore-kudeaketa eta kargatze-egoeraren bidezko erabiltzaileari feedbacka
6. Sortu cache-sistema sinple bat datuak berreskuratzeko
7. Inplementatu `errorCaptured` erroreak harrapatzeko eta erakutsi mezu adierazgarriak erabiltzaileari
