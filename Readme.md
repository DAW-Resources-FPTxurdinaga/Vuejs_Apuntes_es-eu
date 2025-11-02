# Tutorial de Safe Exam Browser / Safe Exam Browser Tutoriala

Tutorial bilingüe (Español/Euskera) sobre Safe Exam Browser (SEB) para entornos educativos.

## 📚 Contenido / Edukia

### Español
- **Introducción**: ¿Qué es SEB? Ventajas, inconvenientes y casos de uso
- **Instalación**: Guía paso a paso para Windows, macOS e iPad

### Euskara
- **Sarrera**: Zer da SEB? Abantailak, desabantailak eta erabilera kasuak
- **Instalazioa**: Urrats-urratseko gida Windows, macOS eta iPad-erako

## 🚀 Cómo usar este tutorial

### Instalar dependencias

```bash
pip install mkdocs mkdocs-material mkdocs-static-i18n
```

### Iniciar el servidor

```bash
cd Tutorial_SEB
mkdocs serve
```

### Abrir en el navegador

Abre tu navegador en: `http://127.0.0.1:8000`

## 📁 Estructura del proyecto

```
Tutorial_SEB/
├── docs/
│   ├── eu/                    # Contenido en euskera
│   │   ├── index.md
│   │   ├── sarrera/
│   │   │   └── zer_da_seb.md
│   │   └── instalazioa/
│   │       └── instalatu.md
│   ├── es/                    # Contenido en español
│   │   ├── index.md
│   │   ├── sarrera/
│   │   │   └── zer_da_seb.md
│   │   └── instalazioa/
│   │       └── instalatu.md
│   └── stylesheets/
│       └── extra.css
├── mkdocs.yml
└── README.md
```

## 🌐 Cambio de idioma

El tutorial incluye un selector de idioma en la esquina superior derecha que permite cambiar entre español y euskera.

## 🔗 Enlaces útiles

- [Safe Exam Browser - Sitio oficial](https://safeexambrowser.org/)
- [Descargas de SEB](https://safeexambrowser.org/download_en.html)
- [Documentación Moodle + SEB](https://docs.moodle.org/403/en/Safe_Exam_Browser)

## 📝 Notas

- Este tutorial sigue la misma estructura que otros materiales del Instituto
- El tutorial es bilingüe usando el plugin `mkdocs-static-i18n`
- El idioma por defecto es euskera

## 📄 Licencia

Este material educativo está disponible para uso en entornos educativos.
