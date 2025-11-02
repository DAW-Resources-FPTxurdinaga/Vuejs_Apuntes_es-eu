# 1.1 Vue-ri Sarrera

## Zer da Vue.js?

Vue (ahoskatua /vjuː/, ingelesezko 'view' hitzaren antzera) erabiltzaile-interfazeak sortzeko framework progresiboa da. Beste framework monolitikoekin alderatuta, Vue-ren diseinua mailaz maila integratzeko aukera ematen du.

## Ezaugarri nagusiak

- **Erreaktiboa**: Datuetan aldaketak egiten direnean, automatikoki eguneratzen du ikuspegia
- **Osagaiak**: HTML elementu pertsonalizatu eta berrerabilgarriak sortzeko aukera ematen du
- **Ikasteko erraza**: HTML eta JavaScript-ean oinarritutako sintaxi erraza
- **Malgua**: Proiektu existenteetan mailaz maila integra daiteke

## Adibide oinarrizkoa

### 1. Aukera: HTML estandarreko fitxategi gisa

```html
<!DOCTYPE html>
<html>
<head>
  <title>Nire Lehen Vue Aplikazioa</title>
  <!-- Inportatu Vue 3 CDN-tik -->
  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
</head>
<body>
  <div id="app">
    <h1>{{ mezua }}</h1>
    <button @click="aldatuMezua">Aldatu mezua</button>
  </div>

  <script>
  const { createApp, ref } = Vue

  createApp({
    setup() {
      const mezua = ref('Kaixo Vue 3!')
      
      function aldatuMezua() {
        mezua.value = 'Mezua aldatu da!'
      }
      
      return {
        mezua,
        aldatuMezua
      }
    }
  }).mount('#app')
  </script>
</body>
</html>
```

### 2. Vue-ren Fitxategi Bakarreko Osagai (SFC) gisa

```vue
<template>
  <div>
    <h1>{{ mezua }}</h1>
    <button @click="aldatuMezua">Aldatu mezua</button>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const mezua = ref('Kaixo Vue 3!')
    
    function aldatuMezua() {
      mezua.value = 'Mezua aldatu da!'
    }
    
    return {
      mezua,
      aldatuMezua
    }
  }
}
</script>
```

## Vue 3-ren ezaugarri berriak

- **Composition API**: Kodea modu logikoagoan antolatzeko modua
- **Erreaktibotasun hobea**: Proxy-ren bidezko erreaktibotasuna
- **TypeScript euskarria**: Hobetutako TypeScript bateragarritasuna
- **Fragmentak**: Hainbat erroko osagaiak
- **Teleport**: DOM elementuak lekuz aldatzeko aukera
- **Suspense**: Osagai asinkronoentzako euskarria

### Nola probatu aukera bakoitza:

#### 1. Aukera (HTML estandarra):
1. Kopiatu 1. Aukerako kodea
2. Gorde `index.html` izenarekin
3. Zure nabigatzailean ireki

#### 2. Aukera (SFC):

1. **Vue Playground-n**:
    - Joan [Vue Playground-era](https://play.vuejs.org/)
    - Ordeztu `App.vue`-ren edukia 2. Aukerakoarekin

2. **StackBlitzen**:
    - Sortu proiektu berria [StackBlitzen](https://stackblitz.com/edit/vue-afbpxwxy?file=src%2FApp.vue)
    - Ordeztu `App.vue`-ren edukia 2. Aukerakoarekin

3. **Lokalki**:
    - Sortu `NireOsagaia.vue` fitxategia 2. Aukerako edukiaz
    - Ziurtatu Vue CLI edo Vite konfiguratuta duzula `.vue` fitxategientzako

## Ariketa praktikoa: Zure lehen Vue aplikazioa

Ariketa honetan, zure lehen Vue aplikazioa sortuko duzu. Jarraitu urrats hauek:

### Helburuak
- Sortu oinarrizko HTML fitxategi bat Vue 3-rekin
- Ulertu Vue aplikazio baten oinarrizko egitura
- Esperimentatu Vue-ren erreaktibotasunarekin

### Urratsak

1. **Prestatu zure lantokia**
   - Ireki zure kode-editorea (VS Code, Sublime Text, etab.)
   - Sortu karpeta berri bat proiektu honetarako

2. **Sortu fitxategi berria**
   - Sortu `kaixo-vue.html` izeneko fitxategi bat
   - Kopiatu eta itsatsi ondorengo kodea:

```html
<!DOCTYPE html>
<html lang="eu">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nire Lehen Vue Aplikazioa</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            line-height: 1.6;
        }
        #app {
            text-align: center;
            margin-top: 50px;
        }
        button {
            background-color: #42b983;
            color: white;
            border: none;
            padding: 10px 20px;
            margin: 10px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #3aa876;
        }
    </style>
</head>
<body>
    <!-- Aplikazioaren edukiontzi nagusia -->
    <div id="app">
        <h1>{{ mezua }}</h1>
        <button @click="aldatuMezua">
            {{ botoiTestua }}
        </button>
    </div>

    <!-- Sartu Vue 3 CDN-tik -->
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    
    <script>
    // Aplikazioaren JavaScript kodea
    const { createApp, ref } = Vue;

    // Sortu Vue aplikazioa
    const app = createApp({
        // setup() da konponentearen logika definitzen dugun lekua
        setup() {
            // Aldagai erreaktiboak
            const mezua = ref('Kaixo Vue 3!');
            const botoiTestua = ref('Egin klik mezua aldatzeko');
            
            // Botoian klik egitean exekutatzen den funtzioa
            function aldatuMezua() {
                mezua.value = 'Mezua ongi aldatu da!';
                botoiTestua.value = 'Mezua aldatu da!';
            }
            
            // Itzuli aldagaiak eta funtzioak template-ra
            return {
                mezua,
                botoiTestua,
                aldatuMezua
            };
        }
    });

    // Muntatu aplikazioa "app" IDa duen elementuan
    app.mount('#app');
    </script>
</body>
</html>
```

3. **Gorde eta ireki fitxategia**
   - Gorde `kaixo-vue.html` izenarekin
   - Ireki fitxategia zure web-nabigatzailean (bi klik egin gainean)

### Zer ikusi behar zenuke?

- "Kaixo Vue 3!" esaten duen izenburu bat
- "Egin klik mezua aldatzeko" esaten duen botoi berde bat
- Botoian klik egitean, izenburuaren eta botoiaren testua aldatuko dira

### Esperimentatu eta ikasi

Aplikazioa ikusi ondoren, saiatu ondorengo aldaketak egiten:

1. **Aldatu hasierako mezua**:
    - Aldatu `'Kaixo Vue 3!'` testua beste mezu batera
    - Eguneratu orria aldaketak ikusteko

2. **Pertsonalizatu botoia**:
    - Aldatu botoiaren kolorea CSS-eko `background-color` aldatuz
   - Doitu testuaren tamaina edo betegitea

3. **Gehitu kontadore bat**:
    - Gehitu `kontadorea` aldagaia `const kontadorea = ref(0)` erabiliz
    - Sortu botoi bat kontadorea handitzeko klik bakoitzean
    - Erakutsi kontadorearen balioa orrian `{{ kontadorea }}` erabiliz

4. **Gehitu sarrera-eremu bat**:
    - Gehitu `<input v-model="izena">` HTML-ean
    - Sortu `izena` aldagaia `setup()`-en barruan
    - Erakutsi izenarekin pertsonalizatutako mezua

## Berrikusteko galderak

1. Zer esan nahi du Vue "progresiboa" dela?
2. Zein abantaila du Vue-k HTML-ean oinarritutako sintaxia erabiltzeak?
3. Erakutsitako adibidean, zer gertatzen da "Aldatu mezua" botoian klik egitean?
4. Zer egiten du `mount('#app')` metodoak adibideko kodean?

## Baliabide gehigarriak

- [Vue 3-ren dokumentazio ofiziala](https://v3.vuejs.org/)
- [Vue Mastery - Doako ikastaroa](https://www.vuemastery.com/courses/intro-to-vue-3/intro-to-vue3/)

[Hurrengoa: Zure lehen aplikazioa →](lehen-aurkezpena.md)
