# Privacidad y seguridad

TikGO corre en la computadora del streamer y su autor no recibe nada (no hay
servidores propios, telemetría ni informes de errores; verificado en el código).
Pero la app sí trata datos de terceros —la audiencia— y manda algunos a otros
servicios. Esta sección existe para que el streamer pueda cumplir con eso, y
cada función sale de una norma o de un fallo concreto.

**No es asesoramiento legal.** Antes de cobrar por la app, un abogado tiene que
revisar el aviso (ver "Antes de monetizar" en `CLAUDE.md`).

## Por qué el streamer es responsable

- **Transmitir no es un uso privado.** El TJUE resolvió en *Buivids* (C-345/17,
  2019) que publicar un video sin restricciones, a un número indeterminado de
  personas, queda fuera de la excepción de uso personal o doméstico. Un live
  público es exactamente eso: frente a su audiencia, el streamer es responsable.
- **Configurar la herramienta también cuenta.** En *Wirtschaftsakademie*
  (C-210/16, 2018) el administrador de una página de Facebook resultó
  corresponsable con Facebook, aunque no tuviera acceso a los datos.
- **Mandar datos a un tercero lo hace corresponsable de ese envío.** En
  *Fashion ID* (C-40/17, 2019) quien incrustó un botón que transmitía datos a
  Facebook resultó corresponsable de la recogida y la transmisión, no de lo que
  Facebook hizo después. Por eso el aviso lista a cada tercero, qué recibe y
  cuándo, y la app no promete nada sobre lo que hacen ellos.

## Qué hace cada función y de dónde sale

| Función | Base |
|---|---|
| **Edad al primer arranque**: mes y año, con los dos campos vacíos, sin decir antes qué edad hace falta. Menos de 18 bloquea la app; volver a responder recién a las 24 horas. No se guarda la fecha, solo el resultado. | TikTok exige 18 años para salir en vivo y para recibir regalos. La forma de preguntar sigue la guía de la FTC sobre COPPA ("neutral age screen": que la persona pueda poner su edad real, sin un valor por defecto y sin avisar qué se pierde por debajo de cierta edad). Guardar solo el resultado es minimización (RGPD art. 5.1.c). |
| **Aviso aceptado con un botón**, nunca con una casilla premarcada; se guarda la versión y la fecha, y si el aviso cambia se vuelve a pedir. | *Planet49* (C-673/17, 2019): el silencio o una casilla marcada de antemano no son consentimiento. RGPD art. 7.1: hay que poder demostrarlo. La nueva ley mexicana (DOF 20/03/2025) exige el aviso de privacidad. |
| **Sin aceptar, la app no se conecta**: ni a mano ni sola (la vigía). | Nada sale hacia TikTok, Euler o Microsoft antes de que el streamer sepa que sale. |
| **Qué datos y a dónde van**: la lista de terceros, qué recibe cada uno, cuándo, y si está en uso ahora. | Transparencia (RGPD arts. 13 y 14) y el límite de *Fashion ID*. |
| **Textos para la audiencia**: uno para la biografía (80 caracteres) y uno largo para la descripción o un comentario fijado. | El responsable tiene que informar a los interesados. El streamer no tiene otra forma de llegar a su audiencia que su perfil y su chat. |
| **!privacidad y !olvidame en el chat.** El primero responde qué hace la app; el segundo borra lo que la app tiene de esa persona y deja de leerla y mostrarla. Se responde por el Chatbot, con un freno para no inundar el chat. | Derecho de supresión y de oposición (RGPD arts. 17 y 21; Ley 25.326 art. 16; LGPD art. 18), y el deber de facilitar su ejercicio (RGPD art. 12.2). |
| **Espectadores**: buscar a alguien en todos los módulos, exportar lo que hay en un archivo, borrarlo, y excluirlo o volver a incluirlo. | Acceso, portabilidad y supresión (RGPD arts. 15, 17 y 20). Plazos: Argentina, 10 días corridos para el acceso y 5 hábiles para suprimir (Ley 25.326 arts. 14 y 16); Europa, un mes (RGPD art. 12.3). |
| **Excluir sin romper las metas**: el chat de alguien excluido se descarta antes de llegar a cualquier módulo; sus regalos, likes y seguimientos siguen contando, como "Anónimo". | Oposición sin castigar al streamer: los números del live siguen siendo verdad sin procesar a la persona. Solo queda su usuario en la lista, que es lo mínimo para respetar su pedido. |
| **Conservación**: los nombres de las Estadísticas se borran a los 90 días (los números quedan), los pedidos de canciones también; los puntos no vencen salvo que el streamer lo elija. | Limitación del plazo de conservación (RGPD art. 5.1.e). Los puntos no vencen por defecto porque la audiencia los considera suyos (ver la lección del libro de Puntos en `CLAUDE.md`). |
| **Tapar datos en la voz**: correos, teléfonos, documentos y enlaces se cambian por "un correo", "un número", "un enlace" antes de mandarlos a Microsoft. | Minimización antes de un envío a un tercero. De paso evita que se lea en vivo el teléfono de alguien. La voz ya no mandaba el nombre de quien escribe. |
| **Menores en la audiencia**: la app no arma perfiles de comportamiento y guarda lo mínimo. | TikTok admite espectadores desde los 13 años. La DPC irlandesa multó a TikTok con 345 millones de euros (2023) por dejar públicas por defecto las cuentas de 13 a 17 años (arts. 5.1.c y 25); la FTC obligó a Epic Games (2022) a apagar por defecto el chat de voz y texto de menores. La LGPD (art. 14) pide el interés superior del menor. |
| **Tus datos**: exportar todo en un archivo (sin claves, tokens ni sesiones) y borrar todo. | Portabilidad y supresión de los datos del propio streamer. |
| **Fuentes de la app incluidas** (IBM Plex, licencia OFL, con la licencia en `resources/licencias/`). | *LG München I*, 3 O 17493/20 (2022): cargar Google Fonts desde Google le manda la IP al visitante a Google sin base legal; 100 € de daños más la orden de cesar. |

## Seguridad

- **La clave de Euler va cifrada** con `safeStorage` (DPAPI, atada al usuario de
  Windows), como ya iban las sesiones de TikTok, Spotify y la cuenta del Chatbot.
  Una clave vieja en texto plano se vuelve a guardar cifrada la primera vez que
  se lee. Si Windows no ofrece cifrado, queda como estaba para no perderla.
- **"Conectar con tu sesión"**: con sesión iniciada, la librería reenvía la cookie
  de TikTok al servidor de firmas de Euler (`authenticateWs`). Esa cookie da
  acceso a la cuenta. Queda **prendido por defecto** (decisión de Jerónimo: no
  cambiar cómo conecta hasta probar en un live si anda sin ella), explicado en el
  aviso y con interruptor. Apagado, la sesión se sigue usando para hablar con
  TikTok directamente, pero no viaja a Euler.
- **Todo lo que abre un puerto escucha solo en `127.0.0.1`**: las
  superposiciones, el puente de los mods, el de Minecraft y el regreso del login
  de Spotify (verificado en los cuatro lugares donde se llama a `listen`). Los datos de la audiencia se insertan con `textContent`, o
  escapados cuando hace falta HTML (las alertas de Acciones y Eventos).

## Qué es un "borrado"

Cada módulo que guarda algo de un espectador registra en `handlers.js` cómo
**buscarlo** y cómo **olvidarlo** (`privacidad.registrarFuente`), y si tiene
plazo, cómo **podarlo** (`registrarConservacion`). Hoy son seis fuentes: Puntos,
Superposiciones y récords (incluido el ranking y el feed en pantalla),
Estadísticas (el live en curso y los guardados), Spotify (historial y pedidos
pendientes), YouTube (saldo, cola e historial del live) y la voz (su monedero).

**Borrar todo** no puede hacerse con la app andando: los módulos tienen los
datos en memoria y los volverían a escribir al cerrar. Deja una marca, reinicia
la app, y se borra al arrancar, antes de cargar ningún módulo. Borra los `.json`
y `.enc` de la carpeta de datos y las Estadísticas; deja los archivos internos de
Chromium y lo bajado para los juegos.

## Lo que falta

- **Las superposiciones siguen pidiendo sus fuentes a Google**: son unas 30
  (Roboto, Poppins, Montserrat, Inter, Exo 2, Oswald, Anton, Bebas Neue y Comic
  Neue, que el streamer elige en cada estilo). La IP que ve Google es la del
  streamer, desde OBS; el aviso lo dice.
- **La clave de la API de YouTube no va cifrada** (el aviso lo dice).
- Las listas en memoria de "resultados recientes" de Spotify conservan nombres
  hasta el final del live.
- Los mundos del servidor de Minecraft guardan los nombres de los jugadores y
  "Borrar todo" no los toca.
- Probar !privacidad y !olvidame en un live de verdad.
- **Chile** (Ley 21.719) entra en vigencia el 1 de diciembre de 2026, con
  autoridad propia y multas. Conviene revisar el aviso antes.

## Cómo se probó

- **En `tests/`, con `npm test`** (códigos PRI-01 a PRI-12 del registro de defectos):
  el tapado de datos, la edad y su bloqueo, el filtro, los comandos, buscar y borrar,
  exportar sin secretos, el borrado total y el cifrado de la clave de Euler. Y un live
  simulado de punta a punta por `handlers.js` y los módulos reales
  (`tests/cadena/en-vivo.test.js`): una espectadora con comentario, regalo y likes
  escribe !olvídame y la búsqueda ya no la encuentra en ninguna fuente; después sus
  mensajes no llegan a la pantalla ni a la voz y sus regalos cuentan como Anónimo.
  Microsoft recibe el teléfono tapado, también cuando el mensaje se recorta. Con
  "Conectar con tu sesión" apagado, la cookie no viaja a Euler.
- Las pruebas con que salió la 0.15.0 vivían en una carpeta temporal y se perdieron;
  las de `tests/` las reemplazan y encontraron dos fallas del tapado (VOZ-04 y PRI-04).
- La app real con datos vacíos: la bienvenida, el bloqueo de un menor (y que
  recargar no lo saltea), la aceptación y las cinco pestañas.

## Fuentes

- [LG München I, 3 O 17493/20 (Google Fonts)](https://blog.eprivacy.eu/?p=1398)
- [TJUE, *Buivids*, C-345/17](https://www.dpcuria.eu/case?reference=C-345%2F17)
- [TJUE, *Wirtschaftsakademie*, C-210/16](https://iabeurope.eu/data-privacy-hub/wirtschaftsakademie-schleswig-holstein/)
- [TJUE, *Fashion ID*, C-40/17](https://gdprhub.eu/index.php?title=CJEU_-_C%E2%80%9140/17_-_Fashion_ID)
- [TJUE, *Planet49*, C-673/17](https://gdprhub.eu/index.php?title=CJEU_-_C-673%2F17_-_Planet49)
- [FTC, preguntas frecuentes sobre COPPA (age screen neutral)](https://www.ftc.gov/business-guidance/resources/complying-coppa-frequently-asked-questions)
- [FTC, acuerdo con Epic Games (2022)](https://www.ftc.gov/news-events/news/press-releases/2022/12/fortnite-video-game-maker-epic-games-pay-more-half-billion-dollars-over-ftc-allegations)
- [DPC Irlanda, multa a TikTok por datos de menores (2023)](https://www.matheson.com/insights/dpc-fines-tiktok-345m-for-gdpr-violations-concerning-childrens-data/)
- [TikTok, requisitos de edad para LIVE](https://www.tiktok.com/safety/en/tools-and-guides/live-safety-guide)
- [Ley 25.326 (Argentina)](https://www.oas.org/juridico/pdfs/arg_ley25326.pdf)
- [LGPD, Ley 13.709 (Brasil)](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)
- [Nueva LFPDPPP de México (2025)](https://www.diputados.gob.mx/LeyesBiblio/pdf/LFPDPPP.pdf)
- [Ley 21.719 (Chile)](https://academiajudicial.cl/recursos/actualizaciones-normativas/ley-21-719-que-regula-la-proteccion-y-el-tratamiento-de-los-datos-personales-y-crea-la-agencia-de-proteccion-de-datos-personales/)
- [tiktok-live-connector: `authenticateWs` reenvía la sesión al servidor de firmas](https://github.com/zerodytrash/TikTok-Live-Connector) (documentado en su `index.d.ts`)
