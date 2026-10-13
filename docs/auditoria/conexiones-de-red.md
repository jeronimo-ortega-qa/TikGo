# Conexiones de red

A qué servidores se conecta TikGO, cuándo y con qué datos. Sale de leer el código
(`src/main`) y de las bibliotecas que usa; el 2026-10-12 se buscaron todas las
direcciones que aparecen en el proceso principal.

**TikGO no tiene servidores propios.** No manda telemetría, ni informes de errores,
ni estadísticas de uso a nadie. Todo lo que se guarda queda en la PC del streamer.

## Hacia afuera

| Servicio | Direcciones | Cuándo | Qué recibe |
|---|---|---|---|
| **TikTok** | `www.tiktok.com`, `webcast.tiktok.com` y sus servidores de websocket | Al conectar a un live; el vigía, cada 12 s, para saber si la cuenta salió en vivo | El usuario del live. Con sesión iniciada, la cookie de la sesión. La consulta del vigía va **sin** la sesión. |
| **TikTok** (inicio de sesión) | `www.tiktok.com/login` en una ventana de la app | Cuando el streamer inicia sesión | Lo que el streamer escriba en la página de TikTok. La app se queda solo con la cookie de sesión, cifrada. |
| **TikTok CDN** | `*.tiktokcdn.com` | Al mostrar imágenes de regalos y fotos de perfil | La IP del streamer. |
| **Euler Stream** | `tiktok.eulerstream.com`, `api.eulerstream.com` | Al conectar (firma de la conexión), al bajar el catálogo de regalos y, si se activa, al escribir en el chat | La clave de Euler del streamer y el usuario del live. Con "Conectar con tu sesión" prendido, también la cookie de TikTok. |
| **Microsoft (Edge TTS)** | `speech.platform.bing.com` | Cada mensaje que lee la voz | El texto del mensaje, con correos, enlaces y teléfonos ya tapados, y la voz elegida. No el nombre de quien escribió. |
| **Spotify** | `accounts.spotify.com`, `api.spotify.com` | Solo si el streamer lo vincula | El Client ID del streamer, sus tokens, las búsquedas y las canciones que se encolan. |
| **YouTube** | `www.googleapis.com`, `www.youtube.com`, `i.ytimg.com` | Solo si el streamer lo prende | La clave de la API (si la puso) y las búsquedas. El video lo reproduce OBS desde YouTube. |
| **Google Fonts** | `fonts.googleapis.com`, `fonts.gstatic.com` | Las superposiciones, desde OBS, al elegir una de sus ~30 fuentes | La IP del streamer, vista desde OBS. La interfaz de la app ya no las pide (sus fuentes van incluidas). |
| **PaperMC** | `fill.papermc.io` | Al crear o actualizar el servidor de Minecraft | Nada personal: la versión pedida. |
| **Adoptium** | `api.adoptium.net` → GitHub | Al bajar el Java del servidor de Minecraft | Nada personal. |
| **GitHub** | `github.com` | Al instalar Script Hook V .NET para GTA V | Nada personal. |
| **Thunderstore** | `thunderstore.io` | Al instalar BepInEx para R.E.P.O. | Nada personal. |

Todo lo que se baja para los juegos se compara contra un SHA-256 fijo o publicado
antes de usarse (ver `integridad.md`).

Enlaces que la app **solo abre en el navegador**, sin conectarse ella: la página de
Script Hook V (`dev-c.com`), las de ViGEmBus y HidHide en GitHub y el EULA de
Minecraft (`aka.ms`).

## Dentro de la PC

Todos estos servidores escuchan **solo en `127.0.0.1`**: nadie de la red local ni de
internet puede llegar a ellos. Lo comprueba la prueba `SEG-04`.

| Puerto | Qué |
|---|---|
| 8420 / 8421 | Acciones y Eventos (HTTP para OBS / HTTPS para Live Studio) |
| 8430 / 8431 | Juegos Interactivos |
| 8440 / 8441 | Metas |
| 8450 / 8451 | Superposiciones persistentes |
| 8460 / 8461 | Catálogo · Temporizador |
| 8470 / 8471 | Multimedia · YouTube |
| 8480 / 8481 | Chatbot |
| 8490 / 8491 | Catálogo · Rueda |
| 8790 | Puente con el plugin de Minecraft |
| 8791 | Puente con el mod de R.E.P.O. |
| 8792 | Puente con el mod de GTA V |
| 8888 | Regreso del inicio de sesión de Spotify (solo mientras se vincula) |

Si un puerto está ocupado, las superposiciones toman otro libre; los puentes de los
mods avisan el error en vez de quedarse callados.

## Cómo comprobarlo

- `npm test` corre un live simulado entero con la red cortada y falla si la app
  intenta llegar a un servidor que no sea TikTok o Euler (prueba `SEG-06`).
- En la app real, cualquier monitor de red (Resource Monitor de Windows, Wireshark)
  muestra las mismas direcciones.
