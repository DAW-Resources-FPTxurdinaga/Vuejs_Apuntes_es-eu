# 🔄 Erantzunkortasuna Vue 3-an

Erantzunkortasuna Vue-ren kontzepturik boteretsuenetako bat da. Datuetan aldaketak jarraitu eta automatikoki eguneratzeko aukera ematen duen sistema da.

## Erantzunkortasunaren Oinarriak

Vue 3-n, erantzunkortasuna JavaScript-en Proxy-ak erabiliz berridatzi da, errendimendu hobea eta aukera gehiago eskainiz.

### `ref()`

`ref` balio primitibo edo objektu baten erreferentzia eraginkorra sortzeko erabiltzen da:

```javascript
import { ref } from 'vue'

const count = ref(0)

console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

### `reactive()`

`reactive`-ek objektu eraginkor bat sortzen du. `ref`-en ez bezala, ez da beharrezkoa `.value` erabiltzea bere propietateetara sarbidea izateko:

```javascript
import { reactive } from 'vue'

const state = reactive({
  count: 0,
  name: 'Vue 3'
})

console.log(state.count) // 0
state.count++
```

## `ref` vs `reactive`

| Ezaugarria | `ref` | `reactive` |
|------------|-------|------------|
| Datu mota | Edozein mota | Objektuak bakarrik |
| Balioetarako sarbidea | `.value` behar du | Zuzenean atzitu |
| Erantzunkortasuna | `.value`-n eraginkorra | Propietateetan eraginkorra |
| Objektu ordezkapena | Ordeztu daiteke | Ezin da zuzenean ordeztu |

## Desegituraketaren Erantzunkortasuna

Objektu eraginkor bat desegitean, erantzunkortasuna galtzen da. Hura mantentzeko, erabili `toRefs`:

```javascript
import { reactive, toRefs } from 'vue'

const state = reactive({
  count: 0,
  name: 'Vue 3'
})

// ❌ Galtzen du erantzunkortasuna
const { count, name } = state

// ✅ Mantentzen du erantzunkortasuna
const { count, name } = toRefs(state)
```

## `computed`

Propietate kalkulatuek balioak modu eraginkorrean deklaratzea ahalbidetzen dizute, beste balio batzuen araberakoak direnak:

```javascript
import { ref, computed } from 'vue'

const count = ref(0)
const doubleCount = computed(() => count.value * 2)

console.log(doubleCount.value) // 0
count.value++
console.log(doubleCount.value) // 2
```

## `watch` eta `watchEffect`

### `watch`

Datu iturri eraginkor bat edo gehiago behatzen ditu eta atzera deialdi-funtzio bat exekutatzen du aldatzen direnean:

```javascript
import { ref, watch } from 'vue'

const count = ref(0)
const name = ref('Vue')

watch(count, (newValue, oldValue) => {
  console.log(`count ${oldValue}-tik ${newValue}-ra aldatu da`)
})

// Iturri anitz behatu
watch([count, name], ([newCount, newName], [oldCount, oldName]) => {
  console.log('count edo name aldatu dira')
})
```

### `watchEffect`

Efektu bat exekutatzen du, bere menpekotasun eraginkorrak automatikoki jarraitzen dituena eta berriro exekutatzen dena aldatzen direnean:

```javascript
import { ref, watchEffect } from 'vue'

const count = ref(0)
const doubleCount = ref(0)

watchEffect(() => {
  doubleCount.value = count.value * 2
  console.log(`Bikoitza da: ${doubleCount.value}`)
})

count.value++ // Erregistratzen du: "Bikoitza da: 2"
```

## Erantzunkortasun Aurreratua

### `shallowRef` eta `shallowReactive`

`ref` eta `reactive`-ren bertsioak, aldaketen jarraipen sakona ez dutenak:

```javascript
import { shallowRef, shallowReactive } from 'vue'

const state = shallowReactive({
  nested: {
    count: 0 // Ez da eraginkorra
  }
})

export default {
  setup() {
    const shallowCount = shallowRef({ count: 0 })
    return {
      state,
      shallowCount
    }
  }
}
```

### `readonly`

Irakurtzeko soilik proxy bat sortzen du objektu eraginkor edo erreferentzia baterako:

```javascript
import { reactive, readonly } from 'vue'

export default {
  setup() {
    const original = reactive({ count: 0 })
    const copy = readonly(original)

    // Oharra: Ezin da irakurtzeko soilik dagoen propietate bat aldatu
    copy.count++ // Honek abisua eragingo du

    return {
      original,
      copy
    }
  }
}
```

## Hurrengo Urratsak

Orain Vue 3-ko erantzunkortasunaren oinarriak ulertzen dituzunez, osagaien sorkuntzari eta konposizioari buruz ikasteko prest zaude.

[👉 Hurrengoa: Erreferentzia azkarra](erreferentzia-azkarra.md)
