# Defectos y controles · Privacidad, seguridad y licencias

Los `PRI` son **requisitos** que salen de una norma o un fallo (detalle en
`docs/privacidad-y-seguridad.md`); se registran aquí porque cada uno tiene su
prueba y puede romperse como cualquier otra cosa. Los `SEG` y `LIC` son hallazgos.

## Privacidad

### PRI-01 · Sin edad y aviso aceptados, nada sale hacia TikTok, Euler ni Microsoft

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Requisito · cumple | `privacidad/`, `ipc/handlers.js`, `tiktok/vigia-de-live.js` | Automática (`tests/cadena/en-vivo.test.js`) |

- **Prueba.** `tiktok:connect` se rechaza con el motivo y no se abre ninguna
  conexión; después de la edad y el aviso, conecta. La fecha de nacimiento no queda
  en disco.

### PRI-02 · !olvidame borra a la persona de todos los módulos y la excluye

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Requisito · cumple · falta en un live | `privacidad/` y las seis fuentes | Automática · caso manual CM-08 |

- **Prueba.** Una espectadora con comentario, regalo y likes aparece en al menos dos
  fuentes; después de "!olvídame" la búsqueda (sin distinguir mayúsculas) no
  encuentra nada, queda excluida y se le confirma en el chat.

### PRI-03 · Lo de una persona excluida no llega a ningún módulo

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Requisito · cumple | `privacidad/index.js`, `ipc/handlers.js` | Automática y mutación |

- **Prueba.** Su comentario no llega a la pantalla ni a Microsoft; su regalo llega
  como "Anónimo", sin foto y con sus monedas, para que las metas sigan siendo
  verdad. Al volver a incluirla, se la vuelve a ver.

### PRI-04 · La voz tapa correos, enlaces, teléfonos y documentos antes de mandarlos

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Requisito · cumple (con corrección en esta auditoría) | `privacidad/redactar.js` | Automática y mutación |

- **Hallazgo de la auditoría.** Un teléfono escrito con paréntesis,
  "(011) 4555-1234", quedaba como "(un número": el paréntesis no entraba en la
  expresión. Corregido el 2026-10-12, sin compilar. Ver también VOZ-04.

### PRI-05 · Exportar todos los datos no incluye claves, tokens ni sesiones

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Requisito · cumple | `privacidad/index.js` | Automática |

### PRI-06 · La pregunta de edad es neutral y un menor queda bloqueado

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Requisito · cumple | `privacidad/index.js`, `components/Bienvenida.jsx` | Automática |

- **Prueba.** El mes del cumpleaños cuenta como no cumplido; fechas futuras, de más
  de 120 años o imposibles no cuentan. Un menor no puede aceptar el aviso ni volver
  a responder hasta 24 horas después.

### PRI-07 · La clave de Euler va cifrada en disco

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Requisito · cumple | `settings/app-settings-store.js` | Automática |

- **Prueba.** Una clave vieja en texto plano se vuelve a guardar cifrada al leerla.
  Si Windows no ofrece cifrado, la clave no se pierde.

### PRI-08 · Con "Conectar con tu sesión" apagado, la cookie no viaja a Euler

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Requisito · cumple | `tiktok/connection.js` | Automática |

- **Nota.** Viene prendido por decisión de producto hasta probar en un live si
  conecta sin la sesión (pendiente 18 de `CLAUDE.md`).

### PRI-09 · !privacidad responde y tiene freno

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Requisito · cumple · falta en un live | `privacidad/index.js` | Automática · caso manual CM-08 |

### PRI-10 · Cada módulo que guarda algo por espectador está registrado

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Requisito · cumple | `ipc/handlers.js` | Automática (estática) |

- **Riesgo que cubre.** Un módulo nuevo que no se registra deja datos después de un
  !olvidame sin que nada avise.

### PRI-11 · Todo evento del live pasa por el filtro antes de repartirse

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Requisito · cumple | `ipc/handlers.js` | Automática (estática) |

### PRI-12 · "Borrar todo" borra al arrancar, antes de cargar ningún módulo

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Requisito · cumple | `privacidad/borrado.js`, `index.js` | Automática |

- **Prueba.** Se van los `.json`, los `.enc` y las Estadísticas; queda lo bajado para
  los juegos. Sin marca no se borra nada.

### PRI-13 · Datos que el borrado todavía no alcanza

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `spotify/`, `minecraft/` | Documentado |

- Las listas en memoria de "resultados recientes" de Spotify conservan nombres hasta
  el final del live, y los mundos del servidor de Minecraft guardan los nombres de
  los jugadores; "Borrar todo" no los toca.

## Seguridad

### SEG-01 · La clave privada del certificado de respaldo es la misma en todas las instalaciones

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `actions-events/certs/` | Revisión de código |

- **Qué es.** `cert.pem` y `key.pem`, autofirmados para `overlay.tikgo.app`,
  `localhost` y `127.0.0.1`, viajan en cada instalador. Se usan solo si el
  streamer no instaló el certificado de mkcert.
- **Riesgo.** Bajo: el servidor escucha solo en 127.0.0.1 y nadie confía en un
  autofirmado sin aceptarlo a mano. Pero cualquiera que tenga el instalador tiene
  la clave.
- **Recomendación.** Generar el respaldo en la primera ejecución, uno por máquina.

### SEG-02 · La clave de la API de YouTube no va cifrada

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Abierto (el aviso lo dice) | `youtube/youtube-store.js` | Documentado |

### SEG-03 · El código del proceso principal va legible dentro del instalador

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Abierto | empaquetado (`package.json` → `build.files`) | Revisión del paquete 0.15.0 |

- **Qué es.** `src/main` se empaqueta tal cual dentro de `app.asar`, que se abre con
  un comando. La interfaz sí sale empaquetada y minificada por Vite.
- **Riesgo.** Quien tenga el instalador tiene el código casi idéntico al del
  repositorio. Publicar el instalador equivale a publicar el código.
- **Recomendación.** Empaquetar y minificar el proceso principal antes de
  distribuir el instalador por fuera de la beta cerrada. Esto no lo vuelve
  imposible de leer, pero sí mucho más caro.

### SEG-04 · Todo servidor escucha solo en 127.0.0.1

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Control · cumple | superposiciones, puentes de los mods, regreso de Spotify | Automática (estática) |

### SEG-05 · El repositorio no lleva binarios de terceros, compilados ni rutas personales

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Control · cumple | `.gitignore` | Automática (estática) |

- **Hallazgo de la auditoría.** El `.gitignore` no excluía `native/*/lib`
  (ScriptHookVDotNet3, BepInEx, 0Harmony: de terceros), ni `bin/` y `obj/`, que
  llevan rutas con el usuario de Windows. Corregido antes del primer commit.

### SEG-06 · Durante un live simulado no sale nada de la máquina salvo lo previsto

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Control · cumple | toda la app | Automática (`tests/cadena/en-vivo.test.js`) |

- **Cómo se mide.** El entorno de pruebas corta `fetch`, `http`, `https`, `net` y
  `tls` hacia afuera y anota cada intento.

### SEG-07 · Instalar el certificado agrega una autoridad raíz a Windows y la pantalla no lo dice

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `shared/mkcert-helper.js`, `ActionsEventsPanel.jsx` | Revisión de código |

- **Qué es.** "Instalar certificado" corre `mkcert -install`, que crea una autoridad
  raíz y la agrega a los certificados de confianza del usuario. Su clave queda en la
  carpeta de mkcert y puede firmar certificados para cualquier dominio que esa PC
  va a creer.
- **Recomendación.** Decirlo en la pantalla antes del botón, junto con cómo
  quitarla (`mkcert -uninstall`).

### SEG-08 · Las superposiciones para Live Studio dependen de `overlay.tikgo.app`

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Abierto | `shared/hosts-helper.js`, superposiciones | Revisión de código |

- **Qué es.** Live Studio exige un nombre con punto y TLD, así que la app usa
  `overlay.tikgo.app` apuntado a 127.0.0.1 desde el archivo hosts. Si falta esa
  línea, el nombre se resuelve por internet.
- **Riesgo.** Si el dominio no es del proyecto, quien lo registre recibe los
  pedidos de Live Studio del streamer que no puso la línea del hosts.
- **Recomendación.** Registrar el dominio (o el del nombre definitivo) antes de
  distribuir, o confirmar que ya es propio.

### SEG-09 · El instalador no está firmado

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto (trámite pago) | `scripts/build-win.js` | `docs/build-windows.md` |

- **Qué pasa.** Windows muestra SmartScreen y no hay forma de que alguien compruebe
  de dónde vino el archivo más que con el SHA-256 publicado
  (`docs/auditoria/integridad.md`).

### SEG-10 · Los DLL de los mods llevan la ruta de compilación con el usuario de Windows

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Abierto | `native/tikgo-gtav-mod`, `native/tikgo-repo-mod` | Revisión de los binarios |

- **Qué es.** `TikGoGtav.dll` y `TikGoRepo.dll` guardan la ruta de su archivo de
  depuración (`C:\Users\<usuario>\Downloads\tikgo-src\native\…\obj\Release\*.pdb`).
  Viajan en el instalador y se copian a la carpeta del juego de cada streamer.
- **Recomendación.** Al próximo recompilado de cada mod, `<PathMap>` o
  `<DebugType>none</DebugType>` en su `.csproj`. `gamepad-helper.exe` no la lleva.

## Licencias

### LIC-01 · `tiktok-live-connector` 2.x tiene licencia AGPL-3.0

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Abierto · bloquea distribuir con código cerrado | dependencia de producción | SBOM (`docs/auditoria/sbom-produccion.cdx.json`) |

- **Qué es.** `tiktok-live-connector` 2.4.4 y `tiktok-live-proto` 0.2.4 son
  `AGPL-3.0-only`. La 1.2.3 (enero de 2025) era la última con MIT; la 2.0.0 (mayo de
  2025) cambió la licencia.
- **Riesgo.** La AGPL pide que quien recibe el programa pueda recibir también el
  código completo, bajo la misma licencia. Distribuir el instalador —también a la
  beta cerrada— con esta dependencia choca con mantener el código cerrado. **No es
  asesoramiento legal**: lo tiene que ver un abogado.
- **Salidas.** Una conexión propia (Pilar 10 de `CLAUDE.md`), pedir a los autores una
  licencia comercial, o volver a la 1.2.3 si todavía funciona contra TikTok (no
  verificado: la app usa funciones de la 2.x).

### LIC-02 · `edge-tts-universal` tiene licencia AGPL-3.0

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Abierto · bloquea distribuir con código cerrado | dependencia de producción | SBOM |

- **Salida.** La migración que ya estaba pendiente por LIC-03: Azure Speech
  (`microsoft-cognitiveservices-speech-sdk`, MIT) resuelve las dos cosas.

### LIC-03 · Edge TTS va contra los términos de Microsoft para uso comercial

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Abierto · bloquea cobrar | `tts/` | `CLAUDE.md`, "Antes de monetizar" |

### LIC-04 · Las portadas de juegos viajan en el instalador con los derechos sin resolver

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `renderer/assets/games/` | Revisión del paquete |

- **Nota.** Quedan fuera del repositorio por el `.gitignore`, pero Vite las mete en
  el instalador si están en la carpeta al compilar.

### LIC-05 · El ícono de la moneda de TikTok viaja en la app

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Abierto | `renderer/assets/tiktok-coin.png` | Revisión del paquete |

- **Qué es.** Una imagen de la moneda de TikTok, que es de TikTok, dentro de la
  interfaz y del instalador. Igual que las portadas (LIC-04), conviene decidirlo
  antes de distribuir: reemplazarla por un dibujo propio cuesta poco.
