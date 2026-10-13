# Defectos · Interfaz, arranque e instalador

### INT-01 · Una variable de color sin definir dejaba un elemento invisible

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `renderer/styles.css` | Automática (estática) |

- **Síntoma.** La barra de progreso de Minecraft no se veía.
- **Causa.** `var(--accent-500)` no existía: la propiedad queda sin valor sin
  romper nada visible.
- **Prueba.** Toda variable usada en la interfaz tiene que estar definida.

### INT-02 · Un campo nuevo en un store dejaba la pantalla en blanco con lo guardado de antes

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | todos los stores (`migrateAction`) | Automática |

- **Causa.** El formulario asumía que el campo existía en las Acciones guardadas.
- **Corrección.** Cada store migra al leer y rellena lo que falta.
- **Prueba.** Una Acción guardada con un solo efecto abre con todos los demás
  completos y respeta lo guardado.

### INT-03 · La api de respaldo del navegador escondía errores

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (y otro caso en esta auditoría) | `renderer/App.jsx` | Automática (estática) |

- **Antes.** La lista de estilos de la superposición salía vacía: el store los
  exportaba pero nada los pasaba a la interfaz, y la api de respaldo devolvía una
  lista vacía que lo disimulaba.
- **Hallazgo de la auditoría.** Faltaba `interactiveGamesInstallGamepadDriver` en la
  api de respaldo: fuera de Electron el botón de instalar un driver tiraba
  excepción. Agregado el 2026-10-12.
- **Prueba.** Cada método del preload tiene que existir en la api de respaldo.

### INT-04 · Un efecto nuevo sin metadatos figuraba como "Sin efectos"

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `InteractiveGamesPanel.jsx` (`EFFECT_META`) | Automática |

- **Prueba.** Cada efecto que crea el store tiene ícono y etiqueta en la pantalla.

### INT-05 · El interruptor general de una sección no apagaba nada

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `shared/module-enabled-store.js` y cada módulo | Verificado de punta a punta al corregirlo · sin prueba automática |

- **Causa.** La guarda nunca se insertó: la edición no coincidió y no rompió nada
  visible. El store funcionaba y la prueba de la pieza pasaba.
- **Lección.** Verificar la cadena completa: disparar el evento y contar los
  efectos.

### INT-06 · Un número fuera de rango se guarda como el valor por defecto, sin avisar

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Por diseño · regla para los formularios | `sanitizeNumber` en los stores | Documentado en `CLAUDE.md` |

- **Qué pasa.** Escribir 250 donde el máximo es 100 guarda el valor por defecto,
  no el tope. Cada formulario tiene que acotar antes de mandar.

### INT-07 · Los modales se cerraban al hacer clic en el fondo

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `components/Modal.jsx` | Revisión de código |

- **Síntoma.** Se perdían formularios a medio llenar.
- **Corrección.** Todos los modales usan `Modal.jsx`, que cierra solo con la ×,
  Cancelar o Escape.

### ARR-01 · Pantalla oscura varios segundos al abrir la app en frío

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (0.14.1) | `index.js`, `ipc/arranque.js` | Medición en la app real · `docs/arranque.md` |

- **Corrección.** La ventana aparece con la pantalla de carga y recién después se
  cargan los módulos. Ventana visible a ~187 ms del arranque; app completa a ~1,1 s.

### ARR-02 · Registrar el esquema `tikgo-media` al cargarse rompió el arranque

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido (0.14.1) | `index.js`, `desktop-overlay-window.js` | Automática (estática) |

- **Síntoma.** "protocol.registerSchemesAsPrivileged should be called before app is
  ready" y la app no arrancaba.
- **Causa.** Al pasar los módulos a después de la ventana, el registro que corría
  "al cargarse" quedó después de `ready`.
- **Corrección.** `index.js` lo llama explícito antes de `whenReady`.

### ARR-03 · Un módulo fuera de `MODULOS_EN_ORDEN` carga de un tirón

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Control · cumple | `index.js` | Automática (estática) |

- **Riesgo.** Carga igual, pero sin mover la barra de la pantalla de carga.

### ARR-04 · El proceso quedaba vivo al cerrar la app

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido (2026-08-26) | `index.js` | Windows real |

- **Causa.** La ventana de efectos de Juegos Interactivos es una `BrowserWindow`
  más; mientras siga abierta, `window-all-closed` no dispara. Por eso pasaba solo
  si se había disparado un efecto de pantalla en esa sesión.
- **Corrección.** Se cierra junto con la ventana principal.

### ARR-05 · El instalador no compilaba al personalizar el aviso de "la app está abierta"

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (0.14.1) | `build/instalador/instalador.nsh` | Compilación del instalador |

- **Causa.** `customCheckAppRunning` usa `${GetProcessInfo}`, que electron-builder
  incluye solo con su macro por defecto.
- **Corrección.** `!include "getProcessInfo.nsh"` y `Var pid` en `customHeader`.

### ARR-06 · El cierre ordenado al actualizar todavía no se vio funcionar

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `index.js` (`--salir`), instalador | Caso manual CM-10 |

- **Qué falta.** El instalador le pide a la app abierta que se cierre con
  `--salir`; la versión instalada tiene que entenderlo, y la 0.14.0 no lo entendía.
  Se comprueba en la próxima actualización con la app abierta.
