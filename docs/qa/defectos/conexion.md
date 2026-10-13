# Defectos · Conexión al live

Todo lo que la app hace en vivo cuelga de un solo websocket. Cuando se cae, se
apagan a la vez la voz, los Sonidos, las Metas, el Chatbot y el registro, sin
nada en pantalla que lo explique. Detalle técnico en `docs/conexion-en-vivo.md`.

### CON-01 · Los errores de la librería llegaban envueltos y se clasificaban mal

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `tiktok/connection.js` | Automática y mutación (`tests/cadena/conexion.test.js`) |

- **Síntoma.** Cuenta que no está en vivo, sesión vencida, límite de Euler y clave
  rechazada se trataban igual: mismo mensaje, mismo tiempo de reintento.
- **Causa.** `tiktok-live-connector` emite `{ info, exception }`, no el error. Un
  `instanceof UserOfflineError` contra ese objeto nunca da verdadero.
- **Corrección.** `desenvolver()` saca `exception` antes de clasificar.
- **Prueba.** Un error envuelto `UserOfflineError` deja el estado en `user-offline`
  y espera más que un corte; `SignatureRateLimitError` respeta el `retryAfter`.

### CON-02 · Un reintento fallido cortaba la cadena de reintentos

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `tiktok/connection.js` | Automática y mutación |

- **Síntoma.** Después de un corte, si el primer reintento fallaba, la app quedaba
  desconectada hasta que alguien tocara "Conectar".
- **Causa.** La librería emite `disconnected` solo si el websocket llegó a
  abrirse. Si falla el intento, no hay evento y nadie programaba el siguiente.
- **Corrección.** Cada rama de error vuelve a programar el reintento, con el piso
  de su caso (2 s a 30 s, 20 s si la cuenta no está en vivo, lo que pida Euler).
- **Prueba.** Corte seguido de dos reintentos que fallan sin evento: la conexión
  vuelve sola al tercero.

### CON-03 · Un socket abierto y mudo dejaba la app esperando para siempre

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido · falta confirmar en un live largo | `tiktok/connection.js` | Automática y mutación · caso manual CM-01 |

- **Síntoma.** Reportado como "a los 10-20 minutos dejan de andar la voz, los
  Sonidos y el registro, todos juntos".
- **Causa.** Si TikTok deja de mandar sin cerrar la conexión no hay `close` ni
  `error`: la app seguía en "Conectado" sin recibir nada.
- **Corrección.** Un vigía compara contra la última trama recibida
  (`websocketData`, cualquier tráfico) y reconecta a los 90 s de silencio.
- **Prueba.** Sin tráfico, la conexión se suelta y se rehace; con tráfico que no
  son eventos, no se toca.

### CON-04 · Cada reconexión ponía metas, temporizador y contadores en cero

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `tiktok/connection.js`, `ipc/handlers.js` | Automática y mutación |

- **Síntoma.** A mitad del stream, después de un corte, las metas volvían a cero.
- **Causa.** Los `resetLive()` colgaban del estado `connecting`, que también pasa
  en cada reconexión automática.
- **Corrección.** Cuelgan de `onNuevoLive`, que dispara al conectar a mano.
- **Prueba.** Conectar a mano cuenta un live; cortar y reconectar a la misma sala,
  ninguno más.

### CON-05 · El live siguiente heredaba las metas y los contadores del anterior

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `shared/mismo-live.js` | Automática (unidad y cadena) |

- **Síntoma.** Con la app conectada de un día para el otro, el live de mañana
  arrancaba con las metas de hoy.
- **Causa.** El live siguiente entraba como una reconexión más (corrección CON-04).
- **Corrección.** Una reconexión es un live nuevo solo con otra sala y 30 minutos
  o más sin recibir nada. Un live que se cae y se vuelve a abrir al minuto conserva
  sus metas.
- **Prueba.** Tabla de los tres casos en unidad; en cadena, otra sala enseguida no
  reinicia y otra sala después de la pausa sí.

### CON-06 · "Desconectar" no cancelaba un reintento que ya estaba en camino

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `tiktok/connection.js` | Automática y mutación |

- **Síntoma.** El streamer desconectaba y la app se volvía a conectar sola.
- **Corrección.** Lo último que pidió el usuario manda: desconectar limpia el
  reintento pendiente y el vigía de silencio.
- **Prueba.** Corte, desconectar en el acto: no se abre ninguna conexión nueva.

### CON-07 · Con la sesión vencida la app reintentaba sin fin

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `tiktok/connection.js`, `auth/session-store.js` | Automática |

- **Causa.** Reintentar con una cookie inválida solo suma fallas.
- **Corrección.** Es el único caso que corta la cadena: la sesión se borra y hay
  que volver a iniciarla. Inicio lo avisa.
- **Prueba.** `AuthenticatedWebSocketConnectionError`: estado `session-expired`, ni
  un intento más y el archivo de la sesión borrado.

### CON-08 · La consulta de la cuenta con una sesión real no está verificada

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `auth/cuenta.js`, `tiktok/vigia-de-live.js` | Caso manual CM-02 |

- **Qué falta.** El vigía que conecta solo al empezar el live le pregunta a TikTok
  de quién es la sesión. Está probado que responde, que una sesión falsa no
  inventa una cuenta y que sin respuesta usa el último usuario conectado a mano.
  No está probado con una sesión de verdad.
