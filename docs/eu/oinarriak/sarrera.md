# Vue 3-ren Oinarriak

## Vue 3 Proiektu Baten Hasierako Konfigurazioa

#### 1. Urratsa: Sortu Proiektu Berri Bat

Vue 3 proiektu berri bat sortzeko, erabili hurrengo komandoa zure terminalean:

```bash
npm create vue@latest
```

Komando honek proiektua konfiguratzeko galdera batzuk egingo dizkizu. Zure beharretara egokien doazen aukerak ahal duzun bezala hautatu ditzakezu.

#### 2. Urratsa: Joan Proiektuaren Katalogoan

Proiektua sortu ondoren, proiektuaren katalogoan sartu:

```bash
cd proiektuaren-izena
```

#### 3. Urratsa: Instalatu Menpekotasunak

Ondoren, proiektuaren menpekotasun guztiak instalatu:

```bash
npm install
```

#### 4. Urratsa: Abiarazi Garapen Zerbitzaria

Amaitzeko, abiarazi garapen-zerbitzaria:

```bash
npm run dev
```

Zure nabigatzailean `http://localhost:5173` helbidean ikusiko duzu zure aplikazioa.

#### 5. Urratsa: Proiektuaren Egitura

Hona hemen proiektuaren fitxategi eta katalogo nagusien azalpena:

- **`node_modules/`**: Proiektuaren menpekotasun guztiak dituen karpeta.
- **`public/`**: `index.html` bezalako fitxategi estatikoei esleitutako karpeta.
- **`src/`**: Aplikazioaren iturburu-kodea.
  - **`assets/`**: Irudiak eta estilo-fitxategiak.
  - **`components/`**: Berrerabilgarriak diren Vue osagaiak.
  - **`views/`**: Aplikazioaren ikuspegi orokorrak, normalean Vue Router-ekin erabiltzen direnak.
  - **`App.vue`**: Aplikazioaren erro-osagaia.
  - **`main.js`**: Vue aplikazioa hasieratzen den sarrera-puntua.
- **`.gitignore`**: Git-ek ez ikusiarena egin behar dien fitxategi eta karpeten zerrenda.
- **`package.json`**: Proiektuaren informazioa eta menpekotasunak.
- **`README.md`**: Proiektuaren dokumentazioa.
- **`vite.config.js`**: Vite-ren konfigurazioa, Vue 3-rako gomendatutako bundler berria.

Konfigurazio honekin, Vue 3 proiektu bat prest duzu garatzen hasteko.
