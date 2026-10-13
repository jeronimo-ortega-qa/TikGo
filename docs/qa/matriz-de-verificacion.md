# Matriz de verificación

Qué se comprobó de cada función y dónde. Una casilla vacía es algo que **no** se
comprobó todavía: la mayoría de las funciones de TikGO están verificadas con eventos
simulados y en la app real, pero no en un live de verdad.

✔ comprobado · ◐ en parte · — no aplica · vacío: falta

| Función | Pruebas en `tests/` | Simulado de punta a punta | App real | Navegador | Dentro del juego | OBS o Live Studio | Live de verdad |
|---|---|---|---|---|---|---|---|
| Conexión al live: reintentos, silencio, live nuevo | ✔ | ✔ | ✔ | — | — | — | ◐ |
| Conectarse solo al empezar el live | | ✔ | ◐ | — | — | — | |
| Voz (Edge TTS): cola, reloj, idioma, tapado | ✔ | ✔ | ✔ | — | — | — | ◐ |
| Sonidos y atajos de teclado | | ✔ | ✔ | — | — | — | ◐ |
| Acciones y Eventos | ◐ | ✔ | ✔ | ✔ | — | | |
| Alertas con video de Acciones y Eventos | | ✔ | | ✔ | — | | |
| Juegos Interactivos: efectos de escritorio | ◐ | ✔ | ✔ | — | | | |
| Juegos Interactivos: teclado, mouse y control | | | ✔ | — | | — | |
| Superposición de Juegos Interactivos | | ✔ | | ✔ | — | | |
| Metas | | ✔ | ✔ | ✔ | — | | |
| Superposiciones persistentes (las nueve) | | ✔ | | ✔ | — | | |
| Multimedia · Spotify | | ✔ (API simulada) | | — | — | | |
| Multimedia · YouTube | | ✔ (API simulada) | | ✔ | — | | |
| Catálogo · Temporizador | | ✔ | | ✔ | — | | |
| Catálogo · Puntos | ◐ | ✔ | | — | — | — | |
| Catálogo · Rueda | | ✔ | | ✔ | — | | |
| Chatbot (superposición) | ◐ | ✔ | | ✔ | — | | |
| Chatbot (chat de TikTok) | | ✔ (Euler simulado) | | — | — | — | |
| Estadísticas | ◐ | ✔ | ✔ | — | — | — | |
| Privacidad: edad, aviso, !olvidame, exportar, borrar | ✔ | ✔ | ✔ | — | — | — | |
| Inicio: cosas que atender | | ✔ | ✔ | — | — | — | |
| Arranque y esqueletos | ◐ | — | ✔ | — | — | — | — |
| Bandeja del sistema | | — | ✔ | — | — | — | |
| Minecraft: servidor y plugin | ◐ | ✔ | ✔ | — | ◐ (sin jugadores) | — | |
| R.E.P.O. | | ✔ (cliente TCP falso) | ✔ | — | ◐ | — | |
| GTA V: 49 efectos originales | ◐ | ✔ | ✔ | — | ✔ (Enhanced, Epic) | — | |
| GTA V: 21 trampas, trolleos y desastres | ◐ | ✔ | | — | | — | |
| Instalador | — | — | ✔ (0.14.1) | — | — | — | — |

## Qué significa cada columna

- **Pruebas en `tests/`**: corren con `npm test` en cualquier máquina, sin red ni
  Electron. ◐ es que hay pruebas de algunas piezas, no de la función entera.
- **Simulado de punta a punta**: los eventos del live, con su forma real, pasan por
  `handlers.js` y los módulos reales; lo de afuera (TikTok, Euler, Microsoft,
  Spotify, YouTube) es falso. Se hizo durante el desarrollo de cada función.
- **App real**: la app compilada o en desarrollo, en Windows, con una copia de la
  configuración.
- **Navegador**: la página real de la superposición servida por la app.
- **Dentro del juego**: con el juego abierto.
- **OBS o Live Studio** y **Live de verdad**: los casos de `casos-manuales.md`.
