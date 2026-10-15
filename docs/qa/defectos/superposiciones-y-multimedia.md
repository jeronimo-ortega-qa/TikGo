# Defectos · Superposiciones para OBS y Multimedia

Las páginas corren dentro de OBS o de TikTok LIVE Studio, no en TikGO. Se verifican
abriendo las páginas reales en el navegador; dentro de OBS falta casi todo
(ver la matriz de verificación).

### SUP-01 · El Tarro de donaciones se rompía al cargar en una fuente oculta

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `overlays/` (Tarro) | Navegador |

- **Causa.** Con la fuente oculta o recién creada el alto y el ancho son 0, y la pila
  se armaba con una geometría nula. Lo que depende del tamaño espera al
  `ResizeObserver`.

### SUP-02 · La cola de YouTube crecía en silencio sin el visor en OBS

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `youtube/`, `shared/overlay-http-server.js` | Navegador |

- **Corrección.** `hayClientes()` dice si alguna página está abierta y la app avisa
  cuando la superposición que necesita no está en OBS.

### SUP-03 · Si el visor se cuelga, la cola de videos se traba para siempre

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `youtube/` | Navegador |

- **Corrección.** La app corta sola al terminar el turno más 8 s, sin esperar el
  aviso de la página.

### SUP-04 · Las alertas con video de Acciones y Eventos no se probaron en OBS

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `actions-events/overlay-server.js` (`/media/:token`) | Caso manual CM-05 |

- **Contexto.** Se pasaron de data URI a una ruta con soporte de `Range`; dentro de
  OBS no hace falta canvas porque no es una ventana transparente. Nadie lo probó
  todavía con OBS conectado.

### SUP-05 · Las superposiciones le piden sus fuentes a Google desde OBS

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Abierto (decisión pendiente) | estilos de las superposiciones | Revisión de código |

- **Qué es.** Unas 30 fuentes (Roboto, Poppins, Montserrat, Inter, Exo 2, Oswald,
  Anton, Bebas Neue, Comic Neue). La IP que ve Google es la del streamer; el aviso lo
  dice. La interfaz de la app ya no las pide (desde 0.15.0).

### SUP-06 · Los videos de YouTube dentro de OBS no se probaron

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `youtube/` (visor) | Caso manual CM-07 |

- **Qué falta.** Que el video arranque solo y con audio en la fuente de navegador de
  OBS: la política de autoplay del CEF de OBS puede ser otra que la del navegador.

### SUP-07 · Falta ver a Spotify pasar las canciones pedidas a su cola

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `spotify/` | 34 comprobaciones con la API simulada · prueba de campo (2026-09-01) · caso manual CM-11 |

- **Lo que sí se vio en vivo.** El 2026-09-01 la cuenta se vinculó con Spotify de
  verdad y los pedidos del chat entraron a la cola de la app. Lo que falta es ver una
  canción pasar a la cola de Spotify y sonar en su turno.

- **Límites conocidos de Spotify, por diseño.** Hace falta Premium (`403
  PREMIUM_REQUIRED` al encolar), hace falta un dispositivo activo (`404
  NO_ACTIVE_DEVICE`) y su cola solo permite agregar, por eso la app lleva la suya y
  le pasa una canción a 20 s del final.

### SUP-08 · El chat en vivo se quedaba en los primeros mensajes del live

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido (2026-09-01) · sin reconfirmar con OBS abierto un rato largo | `shared/overlay-http-server.js`, páginas de las superposiciones | Prueba de campo (2026-09-01) |

- **Causa.** El websocket entre la app y la página reconectaba solo si se cerraba
  limpio; dentro de OBS puede quedar a medio cerrar y la página nunca se entera.
- **Corrección.** Latido de los dos lados: el servidor suelta a la página que no
  responde y la página se reconecta si pasan 30 s sin recibir nada.

### SUP-09 · La pantalla de Spotify prometía un botón que no existía

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (2026-09-01) | `spotify/`, `MultimediaPanel.jsx` | Prueba de campo (2026-09-01) |

- **Síntoma.** Con la cola automática apagada, la app decía que las canciones esperaban
  "hasta que las mandes a mano", y no había forma de mandarlas.
- **Corrección.** Cada pedido tiene "Mandar a Spotify ahora", y si falla dice el motivo.

### SUP-10 · Las alertas no cargaban en TikTok LIVE Studio y sí en el navegador

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido · falta confirmar en Live Studio | `shared/mkcert-helper.js` | Prueba de campo (2026-09-02) |

- **Causa.** La autoridad del certificado estaba instalada solo para el usuario de
  Windows, y Live Studio no mira ese almacén.
- **Corrección.** Instalarla también para la máquina, con permiso de administrador una
  sola vez. Mientras tanto, la dirección HTTP sirve la misma superposición.
