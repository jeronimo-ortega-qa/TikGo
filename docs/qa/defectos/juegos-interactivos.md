# Defectos · Juegos Interactivos y efectos sobre Windows

Casi todo lo de esta área depende de Windows real (ventanas transparentes, ganchos
de teclado y mouse, drivers del control) y se verificó a mano en la máquina de
desarrollo. Las mediciones y los porqués están en
`.claude/rules/entorno-windows-electron.md`.

### JUE-01 · El jumpscare no se veía

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido (2026-08-26) | `desktop-overlay-window.js` | Windows real |

- **Causa.** Un `<video>` no se compone dentro de una ventana transparente: queda
  negro en pantalla aunque `capturePage()` lo muestre. La causa anotada antes (el
  límite de los data URI) era real pero no era esta.
- **Corrección.** Esquema `tikgo-media://` y video dibujado en `<canvas>`. Dos
  carreras de paso: `loadedmetadata` antes del listener y el bucle que cortaba antes
  del autoplay.

### JUE-02 · "Voltear pantalla" no funcionaba

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido (2026-08-26) | `interactive-games/system-effects.js` | Windows real |

- **Causas, dos y en orden.** Un comentario con `$null:` dentro del here-string de
  PowerShell rompía el script; arreglado eso, `DEVMODE` y `DISPLAY_DEVICE` no
  declaraban `CharSet` en su `[StructLayout]` y se marshaleaban como ANSI contra
  APIs Unicode.

### JUE-03 · Rotar el monitor no se ve en el stream

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido (2026-09-18) | `espejo-de-ventana.js` | Grabación de Live Studio · sonda de captura |

- **Causa.** Las APIs de captura entregan la imagen sin rotar; ni Live Studio ni el
  grabador de Windows aplican la rotación.
- **Corrección.** Se espeja la ventana del juego en una ventana transparente encima.
  Solo se ve con captura de pantalla completa. Falta medirlo a pantalla completa y
  sobre un juego real.

### JUE-04 · El mouse invertido mandaba el cursor a una esquina

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `interactive-games/mouse-interference.js` | Windows real |

- **Causa.** Sin declarar conciencia de DPI, Windows informa 1536×864 en vez de
  1920×1080 con escalado al 125 %. Ahora se declara y se usa la pantalla virtual.

### JUE-05 · El cursor se trababa contra el borde con el mouse invertido

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Mitigado (no resuelto de fondo) | `interactive-games/mouse-interference.js` | Windows real |

- **Causa.** Windows recorta la posición antes de que el gancho la vea. Margen de
  2 px como mitigación; la solución es leer por Raw Input.

### JUE-06 · Matar el ayudante del control dejaba el mando oculto en todo el sistema

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `gamepad-interference.js`, `native/gamepad-helper` | Windows real · automática (limpieza al cargar) |

- **Síntoma.** Steam dejó de ver el DualSense y no andaba en ningún juego, sin
  ningún mensaje.
- **Causa.** `child.kill()` en Windows es `TerminateProcess` y no corre los
  `finally` que devuelven el control a HidHide.
- **Corrección.** El ayudante sale solo con `quit` o al cerrarse su stdin, y la app
  hace una limpieza de emergencia al cargar (`hidhide-reset`).

### JUE-07 · Bloquear L2 o R2 se guardaba bien y no hacía nada

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `native/gamepad-helper` (`FiltrarGatillos`) | Windows real, leyendo por XInput |

- **Causa.** Los gatillos son ejes analógicos, no bits de `wButtons`.

### JUE-08 · Los listeners del video se acumulaban en cada disparo

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `desktop-overlay-window.js` | Medición con el HTML real |

- **Medido.** 4 listeners por disparo, 120 a los 30, cada uno con su canvas en GPU.

### JUE-09 · La memoria de video no volvía después de un jumpscare

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `desktop-overlay-window.js` | Medición |

- **Medido.** 136 MB → 759 MB en el proceso de GPU con un solo jumpscare. La ventana
  se suelta sola tras dos minutos sin efectos.

### JUE-10 · Simular teclas con SendKeys no apretaba teclas

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `interactive-games/system-effects.js` | Windows real, con un gancho contador |

- **Causa.** SendKeys escribe texto: no conoce `{SPACE}`, no mantiene ni suelta y no
  manda código de escaneo. Ahora todo va por `keybd_event` con `MapVirtualKey`.

### JUE-11 · "Mantener tecla" repetía a 14 por segundo en vez de 30

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `interactive-games/keyboard-interference.js` | Medición |

- **Causa.** `Thread.Sleep` redondea al tic de 15,6 ms. Ahora el bucle va contra un
  horario fijo medido con `Stopwatch`.

### JUE-12 · Una tecla quedaba apretada al matar el proceso que la mantenía

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `interactive-games/system-effects.js` (`soltarTeclas`) | Windows real |

### JUE-13 · Las tarjetas de la superposición de Juegos Interactivos se pisaban

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `game-overlay-server.js` | Navegador |

- **Causa.** `transform: scale()` no ocupa espacio en el layout. Ahora se escalan
  las medidas reales.

### JUE-14 · El ajuste automático de la superposición seguía desbordando

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `game-overlay-server.js` | Navegador |

- **Causa.** `scrollWidth` informa de menos cuando el contenido se sale. Se mide con
  `getBoundingClientRect` del primer y el último elemento.

### JUE-15 · El chroma key recortaba dorados y sombras de la figura

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (0.12.6) | `desktop-overlay-window.js` | Cuadros reales de los dos jumpscares |

- **Causa.** Distancia RGB. Ahora se mide en croma (CbCr), como OBS, y se recorta en
  la GPU (38 % → 17 % de un núcleo).

### JUE-16 · Un archivo que no coincide con el modo del efecto fallaba en silencio

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `interactive-games/index.js` | Prueba de punta a punta al corregirlo (no está en `tests/`) |

- **Ejemplo.** Un `.mp4` con el modo en "Imagen/GIF". Ahora el error llega a
  `lastEffectError` y se ve en la sección.

### JUE-17 · Los efectos de teclado y mouse no se probaron contra un juego real

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Abierto | efectos de sistema | Caso manual CM-04 |

- **Riesgo.** Un juego que lea por DirectInput o Raw Input puede ignorar un gancho de
  bajo nivel; un anti-cheat que bloquee HidHide (Vanguard) deja el modo avanzado sin
  efecto. Los que simulan teclas tienen más chance que los que bloquean.

### JUE-18 · Aplicar un preajuste generó 9 Eventos sin Acción una vez

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | No reproducible · mitigado | `interactive-games/index.js` (`applyGamePreset`) | Revisión de código |

- **Mitigación.** El preajuste falla en bloque antes de crear ningún Evento si una
  Acción no se guardó, y un Evento sin Acción se ve en rojo como "⚠ Sin Acción". La
  causa original nunca se identificó. Queda uno huérfano en el perfil "Fortnite".

### JUE-19 · Invertir la dirección del mouse: mitigado, no resuelto

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `interactive-games/mouse-interference.js` | Windows real |

- **Solución de fondo.** Leer por Raw Input, en un ejecutable nativo (deuda técnica).
