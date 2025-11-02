# Gertaeren Kudeaketa Vue-n

## Oinarrizko Erabilera

`v-on` zuzentzailearekin gertaerak entzun ditzakezu:

```vue
<template>
  <div>
    <button v-on:click="kontadorea++">Gehitu</button>
    <p>Zenbakia: {{ kontadorea }}</p>
    
    <!-- Laburdura: @ erabiltzen da v-on: ordez -->
    <button @click="kendu">Kendu</button>
    
    <!-- Metodo baten deia -->
    <button @click="agurraErakutsi('Kaixo, Vue!')">Agurra bistaratu</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      kontadorea: 0
    }
  },
  methods: {
    kendu() {
      this.kontadorea--
    },
    agurraErakutsi(mezua) {
      alert(mezua)
    }
  }
}
</script>
```

## Gertaera-Objektuarekin Lan Egitea

Gertaera-objektura sarbidea lortzeko, `$event` aldagaia erabil dezakezu:

```vue
<template>
  <div>
    <!-- Gertaera-objektuarekin -->
    <button @click="gertaeraArgaztu($event)">Sakatu nire gainean</button>
    
    <!-- Saguaren posizioa -->
    <div 
      @mousemove="eguneratuPosizioa" 
      style="width: 300px; height: 200px; background: #f0f0f0; margin: 20px 0;"
    >
      Mugitu sagua honen gainean: {{ x }}, {{ y }}
    </div>
    
    <!-- Tekla-sakatzeak detektatzea -->
    <input 
      @keyup.enter="bidaliMezua"
      @keyup.esc="garbituSarrera"
      placeholder="Sakatu Enter bidaltzeko"
    >
  </div>
</template>

<script>
export default {
  data() {
    return {
      x: 0,
      y: 0
    }
  },
  methods: {
    gertaeraArgaztu(event) {
      console.log('Gertaera-mota:', event.type)
      console.log('Elementua:', event.target.tagName)
    },
    eguneratuPosizioa(event) {
      this.x = event.offsetX
      this.y = event.offsetY
    },
    bidaliMezua(event) {
      alert('Mezua bidalita: ' + event.target.value)
      event.target.value = ''
    },
    garbituSarrera(event) {
      event.target.value = ''
    }
  }
}
</script>
```

## Gertaera Modifikatzaileak

Gertaerei modifikatzaileak gehitu dakioke:

```vue
<template>
  <div>
    <!-- Gertaera behin bakarrik exekutatu -->
    <button @click.once="behinBakarrik">Sakatu behin bakarrik</button>
    
    <!-- Lehenetsitako gertaera ekidin -->
    <form @submit.prevent="bidaliFormularioa">
      <button type="submit">Bidali</button>
    </form>
    
    <!-- Kateatutako modifikatzaileak -->
    <a 
      href="https://vuejs.org" 
      @click.prevent.stop="orriaIreki"
    >
      Vue.js webgunea (ez da nabigatuko)
    </a>
    
    <!-- Modifikatzaile pertsonalizatuak -->
    <input 
      @keyup.ctrl.enter="bidaliMezua"
      placeholder="Sakatu Ctrl+Enter"
    >
  </div>
</template>

<script>
export default {
  methods: {
    behinBakarrik() {
      alert('Gertaera hau behin bakarrik exekutatuko da')
    },
    bidaliFormularioa() {
      alert('Formularioa bidalita!')
    },
    orriaIreki() {
      console.log('Esteka sakatu da, baina ez da nabigatuko')
    },
    bidaliMezua() {
      alert('Ctrl+Enter sakatu duzu!')
    }
  }
}
</script>
```

## Gertaera Modifikatzaile Komunak

- `.stop`: `event.stopPropagation()` deitzen du
- `.prevent`: `event.preventDefault()` deitzen du
- `.capture`: Barruko elementu batek gertatutako gertaerak lehenago prozesatzeko
- `.self`: Soilik elementuaren buruan gertatutako gertaerak erantzuteko
- `.once`: Gertaera behin bakarrik exekutatzeko
- `.passive`: `{ passive: true }` moduan ezartzen du

## Saguaren Eskumako Klik-a Detektatzea

```vue
<template>
  <div 
    @contextmenu.prevent="menuKontestuala"
    style="padding: 20px; border: 1px solid #ccc;"
  >
    Eskuin-klika egin nire gainean menu kontextuala ikusteko.
  </div>
</template>

<script>
export default {
  methods: {
    menuKontestuala(event) {
      // Menu kontextuala erakusteko kodea
      alert('Eskuin-klika detektatuta: ' + event.button + ' botoia')
      
      // Menu kontextuala erakusteko adibidea
      const menu = document.createElement('div')
      menu.style.position = 'absolute'
      menu.style.left = event.pageX + 'px'
      menu.style.top = event.pageY + 'px'
      menu.style.border = '1px solid #ccc'
      menu.style.padding = '10px'
      menu.style.background = 'white'
      menu.innerHTML = 'Menu Kontextuala<br>Elementu bat<br>Beste bat'
      
      document.body.appendChild(menu)
      
      // Menua itxi klik egitean
      const itxiMenua = () => {
        document.body.removeChild(menu)
        document.removeEventListener('click', itxiMenua)
      }
      
      setTimeout(() => {
        document.addEventListener('click', itxiMenua)
      }, 0)
    }
  }
}
</script>
```

## Ariketa Praktikoa

Sortu arratsezko joko sinple bat:

1. Sortu lauki bat pantailan zehar mugitzen dena
2. Erabiltzaileak laukia klikatu behar du
3. Laukia kolorez aldatuko da erabiltzaileak klik egiten duen bakoitzean
4. Kontagailu bat erakutsi segundo kopuruarekin behar dena har dezakezun
5. Erregistratu erabiltzailearen puntuazioa (denbora gutxien behar izan duena)
6. Gehitu zailtasun-mailak (laukia azkarrago edo motelago mugitzea)
7. Gehitu soinu-efektuak klik egitean eta jokoa irabaztean
