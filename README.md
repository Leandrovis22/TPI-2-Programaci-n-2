# TPI 2 PROGRAMACIÓN II | Vue Mini-Juegos

Este es un proyecto en Vue.js que presenta dos juegos clásicos de memoria - Juego de parejas de cartas y Simon Dice. Construido con Vue 3, Vite y Tailwind CSS.

## 🎮 Características

### Juego Simon
- Niveles de dificultad progresiva
- Sistema de puntuación
- Secuencias de colores visuales e interactivas
- Botones de colores responsivos
- Retroalimentación inmediata al jugador
- Incremento de longitud de secuencia por nivel

### Juego de Memoria
- Juego clásico de encontrar parejas
- Cartas con emojis
- Seguimiento del progreso (intentos y pares encontrados)
- Animaciones al revelar las cartas
- Retroalimentación instantánea en coincidencias
- Ocultamiento automático de pares no coincidentes

## 🛠️ Tecnologías Utilizadas

- Vue.js 3.5.12
- Tailwind CSS 3.4.14
- Vite 5.4.10

## 📋 Requisitos Previos

- Node.js (Se recomienda la última versión LTS)
- Gestor de paquetes npm o yarn

## ⚙️ Instalación

1. Clona el repositorio:
```bash
git clone https://github.com/Leandrovis22/TPI-2-Programacion-2.git
cd TPI-2-Programación-2
```

2. Instala las dependencias:
```bash
npm install
```

3. Ejecuta el servidor de desarrollo:
```bash
npm run dev
```

4. Construye para producción:
```bash
npm run build
```

## 📁 Estructura del Proyecto

```
.
├── src/
│   ├── components/
│   │   ├── Memory.vue      # Componente del juego de memoria
│   │   └── Simon.vue       # Componente del juego Simon
│   ├── assets/
│   ├── App.vue
│   └── main.js
├── public/
│   └── favicon.ico
├── index.html
└── package.json
```

## 🎯 Reglas de los Juegos

### Juego de Memoria
- Haz clic en las cartas para revelarlas
- Encuentra pares de animales idénticos
- Solo se pueden revelar dos cartas a la vez
- Los pares coincidentes permanecen visibles
- El juego termina cuando se encuentran todos los pares

### Juego Simon
- Observa la secuencia de botones de colores
- Repite la secuencia haciendo clic en los botones
- Cada ronda exitosa agrega un color a la secuencia
- El juego termina si se ingresa una secuencia incorrecta
- La puntuación aumenta con cada ronda exitosa

## 🤝 Contribuciones

1. Pide un fork del repositorio
2. Crea tu rama de características (`git checkout -b feature/CaracteristicaIncreible`)
3. Realiza commit de tus cambios (`git commit -m 'Agrega alguna CaracteristicaIncreible'`)
4. Realiza push a la rama (`git push origin feature/CaracteristicaIncreible`)
5. Abre un Pull Request
