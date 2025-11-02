# 2.10 Ariketa Praktikoa: Film Katalogoa

Orain heldu da Oinarriak atalean ikasitakoa praktikatzeko unea! Ariketa honetan, elkarrekintzako film katalogo bat sortuko duzu, eta hauek izango ditu:

- Film-zerrenda xehetunekin
- Izenburuaren araberako bilaketa eta generoaren araberako iragazketa
- Izardun kalifikazio-sistema
- Gogoko filmen ikuspegia
- Datuen iraupena tokiko biltegian

## Ariketa Baldintzak

1. **Oinarrizko egitura**: Sortu Vue 3 aplikazio bat hurrengo elementuekin:

    - Izenburuaren araberako bilaketa-barra
    - Generoaren araberako iragazkiak
    - Film-txartelak irudiarekin, izenburuarekin, urtearekin eta kalifikazioarekin
    - Izardun kalifikazio-sistema
    - Gogokoen atala

2. **Beharrezko funtzionalitateak**:

    - Bilatu filmak izenburuaren arabera
    - Iragazi generoaren arabera
    - Emaitu filmak
    - Markatu/desmarkatu gogokoen gisa
    - Ikusi filmaren xehetasunak
    - Gorde datuak tokiko biltegian

## Hasierako kodea

Sortu `index.html` fitxategi bat ondorengo edukiarekin:

```html
<!DOCTYPE html>
<html lang="eu">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Film Katalogoa - Vue 3</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            max-width: 1000px;
            margin: 0 auto;
            padding: 20px;
            line-height: 1.6;
            background-color: #f5f5f5;
        }
        .search-container {
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }
        .search-input {
            width: 70%;
            padding: 10px;
            font-size: 16px;
            border: 1px solid #ddd;
            border-radius: 4px;
            margin-right: 10px;
        }
        .filters {
            margin: 15px 0;
        }
        .filter-btn {
            margin-right: 10px;
            padding: 5px 15px;
            cursor: pointer;
            background: #f0f0f0;
            border: 1px solid #ddd;
            border-radius: 15px;
            transition: all 0.3s;
        }
        .filter-btn.active, .filter-btn:hover {
            background-color: #42b983;
            color: white;
            border-color: #42b983;
        }
        .movies-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }
        .movie-card {
            background: white;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
            transition: transform 0.3s;
        }
        .movie-card:hover {
            transform: translateY(-5px);
        }
        .movie-poster {
            width: 100%;
            height: 300px;
            object-fit: cover;
        }
        .movie-info {
            padding: 15px;
        }
        .movie-title {
            font-size: 1.1em;
            margin: 0 0 5px 0;
        }
        .movie-year {
            color: #666;
            font-size: 0.9em;
            margin: 0 0 10px 0;
        }
        .star-rating {
            color: #ffc107;
            margin-bottom: 10px;
        }
        .favorite-btn {
            background: none;
            border: none;
            font-size: 1.2em;
            cursor: pointer;
            color: #ddd;
        }
        .favorite-btn.active {
            color: #ff4081;
        }
    </style>
</head>
<body>
    <div id="app">
        <h1>Film Katalogoa</h1>
        
        <!-- Bilaketa-barra -->
        <div class="search-container">
            <input 
                type="text" 
                v-model="searchQuery" 
                class="search-input" 
                placeholder="Bilatu filmak..."
                @input="filterMovies"
            >
            
            <!-- Generoaren araberako iragazkiak -->
            <div class="filters">
                <button 
                    v-for="generoa in generoak" 
                    :key="generoa"
                    @click="toggleGeneroa(generoa)"
                    :class="['filter-btn', { active: hautatutakoGeneroak.includes(generoa) }]"
                >
                    {{ generoa }}
                </button>
            </div>
        </div>
        
        <!-- Gogokoen atala -->
        <div v-if="gogokoak.length > 0">
            <h2>Zure Gogoko Filmak</h2>
            <div class="movies-grid">
                <div 
                    v-for="filma in gogokoak" 
                    :key="filma.id" 
                    class="movie-card"
                >
                    <img :src="filma.irudia" :alt="filma.izenburua" class="movie-poster">
                    <div class="movie-info">
                        <h3 class="movie-title">{{ filma.izenburua }}</h3>
                        <p class="movie-year">{{ filma.urtea }}</p>
                        <div class="star-rating">
                            <span v-for="i in 5" :key="i">
                                {{ i <= filma.puntuazioa ? '★' : '☆' }}
                            </span>
                        </div>
                        <button 
                            class="favorite-btn active"
                            @click="toggleGogokoa(filma)"
                        >
                            ❤️
                        </button>
                    </div>
                </div>
            </div>
        </div>
        
        <!-- Film-zerrenda -->
        <h2>Filmak</h2>
        <div class="movies-grid">
            <div 
                v-for="filma in iragazitakoFilmak" 
                :key="filma.id" 
                class="movie-card"
            >
                <img :src="filma.irudia" :alt="filma.izenburua" class="movie-poster">
                <div class="movie-info">
                    <h3 class="movie-title">{{ filma.izenburua }}</h3>
                    <p class="movie-year">{{ filma.urtea }}</p>
                    <div class="star-rating">
                        <span 
                            v-for="i in 5" 
                            :key="i" 
                            @click="puntuatu(filma, i)"
                            style="cursor: pointer;"
                        >
                            {{ i <= filma.puntuazioa ? '★' : '☆' }}
                        </span>
                    </div>
                    <button 
                        class="favorite-btn" 
                        :class="{ active: filma.gogokoa }"
                        @click="toggleGogokoa(filma)"
                    >
                        {{ filma.gogokoa ? '❤️' : '🤍' }}
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
    const { createApp, ref, computed, onMounted } = Vue;

    const hasierakoFilmak = [
        {
            id: 1,
            izenburua: 'Aitabitxia',
            urtea: 1972,
            generoa: 'Drama',
            irudia: 'https://via.placeholder.com/200x300?text=Aitabitxia',
            deskribapena: 'Mafiako buru baten eta semearen arteko harreman konplexua.',
            puntuazioa: 0,
            gogokoa: false
        },
        {
            id: 2,
            izenburua: 'Forrest Gump',
            urtea: 1994,
            generoa: 'Drama',
            irudia: 'https://via.placeholder.com/200x300?text=Forrest+Gump',
            deskribapena: 'Gizaki arrunt baten bizitza aparta.',
            puntuazioa: 0,
            gogokoa: false
        },
        {
            id: 3,
            izenburua: 'Inception',
            urtea: 2010,
            generoa: 'Zientzia-fikzioa',
            irudia: 'https://via.placeholder.com/200x300?text=Inception',
            deskribapena: 'Amesetako lapurrak ideiak lapurtzen dituzte jendearen burutik.',
            puntuazioa: 0,
            gogokoa: false
        },
        {
            id: 4,
            izenburua: 'The Dark Knight',
            urtea: 2008,
            generoa: 'Ekintza',
            irudia: 'https://via.placeholder.com/200x300?text=The+Dark+Knight',
            deskribapena: 'Batman Jokerren aurka borrokan.',
            puntuazioa: 0,
            gogokoa: false
        },
        {
            id: 5,
            izenburua: 'Pulp Fiction',
            urtea: 1994,
            generoa: 'Krimena',
            irudia: 'https://via.placeholder.com/200x300?text=Pulp+Fiction',
            deskribapena: 'Hiltzaile, boxeolari eta bi lapurren istorioak gurutzatzen dira.',
            puntuazioa: 0,
            gogokoa: false
        },
        {
            id: 6,
            izenburua: 'The Shawshank Redemption',
            urtea: 1994,
            generoa: 'Drama',
            irudia: 'https://via.placeholder.com/200x300?text=Shawshank',
            deskribapena: 'Gizon bat errugabeki kartzelan sartu dute eta ihes egiteko plana egiten du.',
            puntuazioa: 0,
            gogokoa: false
        }
    ];

    createApp({
        setup() {
            const filmak = ref([...hasierakoFilmak]);
            const gogokoak = ref([]);
            const searchQuery = ref('');
            const generoak = ['Guztiak', 'Drama', 'Zientzia-fikzioa', 'Ekintza', 'Krimena'];
            const hautatutakoGeneroak = ref(['Guztiak']);

            // Kargatu gordetako datuak hasieratzean
            onMounted(() => {
                const gordetakoFilmak = localStorage.getItem('filmak');
                const gordetakoGogokoak = localStorage.getItem('gogokoak');
                
                if (gordetakoFilmak) {
                    filmak.value = JSON.parse(gordetakoFilmak);
                }
                
                if (gordetakoGogokoak) {
                    gogokoak.value = JSON.parse(gordetakoGogokoak);
                }
            });

            // Iragazi filmak bilaketaren eta generoaren arabera
            const iragazitakoFilmak = computed(() => {
                return filmak.value.filter(filma => {
                    const bilaketaBatDator = filma.izenburua.toLowerCase().includes(searchQuery.value.toLowerCase());
                    const generoaEgokia = hautatutakoGeneroak.value.includes('Guztiak') || 
                                        hautatutakoGeneroak.value.includes(filma.generoa);
                    return bilaketaBatDator && generoaEgokia;
                });
            });

            // Puntuatu filma
            const puntuatu = (filma, puntuazioa) => {
                filma.puntuazioa = puntuazioa;
                gordeDatuak();
            };

            // Txandakatu gogokoen zerrendan
            const toggleGogokoa = (filma) => {
                const index = gogokoak.value.findIndex(f => f.id === filma.id);
                
                if (index === -1) {
                    gogokoak.value.push({...filma, gogokoa: true});
                } else {
                    gogokoak.value.splice(index, 1);
                }
                
                filma.gogokoa = !filma.gogokoa;
                gordeDatuak();
            };

            // Iragazkien kudeaketa
            const toggleGeneroa = (generoa) => {
                if (generoa === 'Guztiak') {
                    hautatutakoGeneroak.value = ['Guztiak'];
                } else {
                    const index = hautatutakoGeneroak.value.indexOf(generoa);
                    
                    if (index === -1) {
                        // Kendri 'Guztiak' aukera eta gehitu generoa
                        hautatutakoGeneroak.value = hautatutakoGeneroak.value.filter(g => g !== 'Guztiak');
                        hautatutakoGeneroak.value.push(generoa);
                        
                        // Ziurtatu zerrenda ez dagoela hutsik
                        if (hautatutakoGeneroak.value.length === 0) {
                            hautatutakoGeneroak.value = ['Guztiak'];
                        }
                    } else {
                        hautatutakoGeneroak.value.splice(index, 1);
                        
                        // Ziurtatu zerrenda ez dagoela hutsik
                        if (hautatutakoGeneroak.value.length === 0) {
                            hautatutakoGeneroak.value = ['Guztiak'];
                        }
                    }
                }
            };

            // Gorde datuak tokiko biltegian
            const gordeDatuak = () => {
                localStorage.setItem('filmak', JSON.stringify(filmak.value));
                localStorage.setItem('gogokoak', JSON.stringify(gogokoak.value));
            };

            return {
                filmak,
                gogokoak,
                searchQuery,
                generoak,
                hautatutakoGeneroak,
                iragazitakoFilmak,
                puntuatu,
                toggleGogokoa,
                toggleGeneroa
            };
        }
    }).mount('#app');
    </script>
</body>
</html>
```

## Ariketa Argibideak

1. **Aplikazioa martxan jarri**:
    - Sortu `index.html` fitxategia eta kopiatu goiko kodea bertan.
    - Ireki fitxategia nabigatzailean.

2. **Egin ariketaren zatiak**:
    - Osatu falta diren funtzionalitateak:
      - Bilaketa-funtzionalitatea
      - Generoaren araberako iragazketa
      - Filmak puntuatzea
      - Gogokoetara gehitzea eta kentzea
      - Datuen iraupena tokiko biltegian

3. **Gehitu ezaugarri gehigarriak**:
    - Erakutsi filmaren xehetasunak sakatu ondoren
    - Gehitu film berriak eskuz
    - Erakutsi batez besteko puntuazioa
    - Inplementatu "Garbitu iragazkiak" botoia

4. **Estiloa hobetu**:
    - Egin erantzunkorra pantaila txikietarako
    - Gehitu animazioak erabiltzailearen ekintzetarako
    - Hobetu itxura orokorra

## Aholkuak

- Erabili Vue 3-ren Composition API (`<script setup>` sintaxia)
- Erabili `ref` eta `computed` egoera eraginkorretarako
- Banatu kodea funtzio txikietan kodearen irakurgarritasuna hobetzeko
- Erabili `localStorage` datuak gordetzeko
- Egin proba etengabe zure kodea idazten duzun bitartean

## Ebazpenaren Pausuak

1. Hasieratu egoera aldagaiak
2. Inplementatu bilaketa-funtzionalitatea
3. Sortu generoaren araberako iragazketa
4. Inplementatu kalifikazio-sistema
5. Kudeatu gogokoetarako funtzionalitatea
6. Gorde eta kargatu datuak tokiko biltegian
7. Hobetu erabiltzaile-esperientzia

Zorte on ariketarekin! Gogoratu Vue-ren dokumentazioa kontsultatu dezakezula edozein zalantzatan.

[Atzera Oinarrietara](.)
