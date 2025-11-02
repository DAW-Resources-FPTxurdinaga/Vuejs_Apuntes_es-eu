# Kalkulatutako Propietateak

## Sarrera

Kalkulatutako propietateak (`computed` propietateak) datu-ereduaren balioetan oinarritutako balio kalkulatuak gordetzeko erabiltzen dira. Hauek cachean gordetzen dira eta beren mendekotasunak aldatzen direnean bakarrik berriro kalkulatzen dira.

## Oinarrizko Adibidea

```vue
<template>
  <div>
    <h2>Erosketa Txartela</h2>
    <div v-for="produktua in produktuak" :key="produktua.id" class="produktua">
      <span>{{ produktua.izena }} - {{ produktua.prezioa }}€</span>
      <input type="number" v-model.number="produktua.kopurua" min="0">
    </div>
    
    <div class="totalak">
      <p>Guztira produktuak: {{ guztiraKopurua }}</p>
      <p>Guztira prezioa (BEZ gabe): {{ guztiraPrezioa }}€</p>
      <p>BEZ (21%): {{ bez }}€</p>
      <p><strong>Guztira (BEZ barne): {{ guztiraBEZrekin }}€</strong></p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      produktuak: [
        { id: 1, izena: 'Teklatua', prezio: 25, kopurua: 0 },
        { id: 2, izena: 'Sagua', prezio: 15, kopurua: 0 },
        { id: 3, izena: 'Monitorea', prezio: 150, kopurua: 0 }
      ]
    }
  },
  computed: {
    guztiraKopurua() {
      return this.produktuak.reduce((total, p) => total + p.kopurua, 0);
    },
    guztiraPrezioa() {
      return this.produktuak.reduce((total, p) => total + (p.prezio * p.kopurua), 0);
    },
    bez() {
      return (this.guztiraPrezioa * 0.21).toFixed(2);
    },
    guztiraBEZrekin() {
      return (this.guztiraPrezioa * 1.21).toFixed(2);
    }
  }
}
</script>

<style scoped>
.produktua {
  margin: 10px 0;
  padding: 10px;
  border: 1px solid #eee;
  border-radius: 4px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

input[type="number"] {
  width: 60px;
  padding: 5px;
}

.totalak {
  margin-top: 20px;
  padding: 15px;
  background-color: #f8f9fa;
  border-radius: 4px;
}

.totalak p {
  margin: 5px 0;
}
</style>
```

## Kalkulatutako Propietateak vs Metodoak

### Metodoaren bidez:
```vue
<template>
  <div>
    <p>Alderantzizko mezua: {{ irakurriAtzerantz() }}</p>
    <p>Alderantzizko mezua (berriro): {{ irakurriAtzerantz() }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      mezua: 'Kaixo, Vue!'
    }
  },
  methods: {
    irakurriAtzerantz() {
      console.log('Metodoa deitu da');
      return this.mezua.split('').reverse().join('');
    }
  }
}
</script>
```

### Kalkulatutako Propietatearekin:
```vue
<template>
  <div>
    <p>Alderantzizko mezua: {{ irakurriAtzerantz }}</p>
    <p>Alderantzizko mezua (berriro): {{ irakurriAtzerantz }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      mezua: 'Kaixo, Vue!'
    }
  },
  computed: {
    irakurriAtzerantz() {
      console.log('Kalkulatutako propietatea berrikusi da');
      return this.mezua.split('').reverse().join('');
    }
  }
}
</script>
```

**Desberdintasun nagusia**: Kalkulatutako propietateak cachean gordetzen dira eta beren mendekotasunak aldatzen direnean bakarrik berriro kalkulatzen dira, metodoak aldiz, beraien emaitza ez dute cachean gordetzen eta deitu ahala beti exekutatzen dira.

## Setter-a duen Kalkulatutako Propietatea

Kalkulatutako propietateek setter bat izan dezakete:

```vue
<template>
  <div>
    <h2>Izena eta Abizena</h2>
    <div>
      <label>Izena:</label>
      <input v-model="izena">
    </div>
    <div>
      <label>Abizena:</label>
      <input v-model="abizena">
    </div>
    <div>
      <label>Izen osoa:</label>
      <input v-model="izenOsoa">
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      izena: 'Pertsona',
      abizena: 'Bat'
    }
  },
  computed: {
    izenOsoa: {
      // getter
      get() {
        return `${this.izena} ${this.abizena}`.trim();
      },
      // setter
      set(berria) {
        const izenak = berria.split(' ');
        this.izena = izenak[0] || '';
        this.abizena = izenak.length > 1 ? izenak.slice(1).join(' ') : '';
      }
    }
  }
}
</script>
```

## Ariketa Praktikoa

Sortu eskola-kuaderno bat ikasleen kalifikazioak kudeatzeko:

1. Datuak:
   - Ikasleen zerrenda (izena, abizena, hainbat gairen kalifikazioak)
   - Gairen zerrenda (Matematika, Euskara, Historia, ...)

2. Kalkulatutako propietateak:
   - Batez besteko kalifikazioa ikasle bakoitzarentzat
   - Batez besteko kalifikazioa gai bakoitzarentzat
   - Kalifikazio altuena eta baxuena
   - Onenak diren ikasleak (batez bestekoa >= 9)
   - Kalifikazioen histograma

3. Aukera eman ikasleen zerrenda iragazteko eta ordenatzeko (izena, abizena, batez bestekoa).
