# Plan de pruebas

Cómo se prueba TikGO, qué cubre cada nivel, qué no cubre y cuándo una versión está
lista para salir. Es el punto de entrada de la documentación de calidad:

| Documento | Para qué |
|---|---|
| [`registro-de-defectos.md`](registro-de-defectos.md) | Cada error conocido, con su severidad, estado y verificación |
| [`matriz-de-verificacion.md`](matriz-de-verificacion.md) | Qué función se comprobó dónde |
| [`pruebas-de-campo.md`](pruebas-de-campo.md) | Las sesiones en vivo y con los juegos, y qué encontró cada una |
| [`casos-manuales.md`](casos-manuales.md) | Lo que falta reconfirmar en vivo, en OBS o con el juego |
| [`informe-0.15.0.md`](informe-0.15.0.md) | El resultado de la última corrida completa |

## Qué se prueba primero

El riesgo de esta app está casi todo en el camino caliente del live. Por orden:

1. **Que no se quede muda en vivo.** Una sola conexión alimenta a todas las secciones;
   si se cae sin avisar, se apaga todo junto (CON-01 a CON-07, VOZ-01).
2. **Que cuente bien el dinero.** Combos, regalos por región y totales (REG-01 a
   REG-04, VOZ-03).
3. **Que no trate datos de nadie sin permiso.** Edad, aviso, exclusión, tapado de
   datos y borrado (PRI-01 a PRI-12).
4. **Que aguante un live grande.** Memoria fija por espectador, nada que se reescriba
   con cada evento (REN-01 a REN-10).
5. **Que no deje la máquina del streamer rota.** Teclas trabadas, mandos ocultos,
   procesos que no cierran (JUE-06, JUE-12, ARR-04).

## Niveles

| Nivel | Qué es | Cómo se corre | Cuándo |
|---|---|---|---|
| **Estáticas** | Reglas del código que, rotas, fallan en silencio (variables de color, api de respaldo, listas que tienen que coincidir, puertos, `.gitignore`, trazabilidad del registro) | `npm test` | Siempre |
| **Unidad** | Una pieza sola: combos, umbrales, contadores, idioma, tapado, edad, saldos, descargas, migraciones | `npm test` | Siempre |
| **Cadena** | Eventos con la forma real de TikTok a través de `handlers.js` y todos los módulos reales | `npm test` | Siempre |
| **Mutación** | Vuelve a poner cada error corregido y exige que su prueba falle | `npm run test:mutaciones` | Antes de cada versión y al tocar una prueba |
| **App real** | La app en Windows con una copia de la configuración, sin tocar la de verdad | Manual | Al cambiar la interfaz o el arranque |
| **Dentro del juego** | Minecraft, R.E.P.O. o GTA V abiertos | Manual | Al cambiar un mod |
| **Pruebas de campo** | Lives de verdad y controlados, TikTok LIVE Studio y los juegos abiertos ([sesiones](pruebas-de-campo.md)) | Manual | Al tocar algo que solo se ve en vivo |
| **Confirmación en vivo** | Los casos `CM-xx`, para los arreglos que salieron del campo | Manual | Antes de la 1.0.0, y al tocar esa parte |

## El entorno de las pruebas automáticas

`tests/ayudas/entorno.js` arma, en cada archivo de prueba:

- una **carpeta de datos temporal** que se borra al terminar: nunca se lee ni se
  escribe la configuración real de `%APPDATA%\TikGO`;
- **Electron falso** (ventanas, IPC, cifrado, atajos), **TikTok falso** que emite
  eventos con la forma real y **Edge TTS falso** que anota lo que le llegaría a
  Microsoft;
- **la red cortada**: `fetch`, `http`, `https`, `net` y `tls` hacia afuera fallan y
  quedan anotados. Solo pasa lo que va a `127.0.0.1`;
- **los procesos bloqueados** para el código de la app: no se lanza PowerShell, ni
  mkcert, ni el ayudante del control, ni Java. Cada intento queda anotado.

Así las pruebas corren en cualquier máquina, sin cuentas ni claves, y no pueden
tocar Windows, el juego ni la configuración de nadie.

## Reglas al escribir una prueba

- **Primero la prueba que reproduce el error, después el arreglo.** Si la prueba no
  falla antes del arreglo, no prueba nada.
- **El nombre empieza con el código del registro**: `test('VOZ-04 · …')`. La prueba
  de trazabilidad exige que el código exista y que cada defecto que dice tener prueba
  automática la tenga.
- **Probar la cadena, no la pieza.** Dos veces una corrección quedó como código
  muerto con la prueba de la pieza pasando (INT-05).
- **Con la configuración cargada, no vacía.** Con todo vacío los módulos salen en la
  primera línea y la prueba no ejecuta lo que importa.
- **Si la corrección tiene una mutación clara, sumarla** a `tests/mutaciones.js`.

## Criterio para sacar una versión

Una versión sale si:

1. `npm test` pasa entero.
2. `npm run test:mutaciones` detecta todas las mutaciones.
3. `npx vite build` compila y la app arranca con una copia de la configuración.
4. No hay defectos **abiertos de severidad S1** nuevos respecto de la versión
   anterior. Los que ya estaban (hoy LIC-01, LIC-02 y LIC-03) se listan en el
   informe de la versión.
5. Si se tocó el instalador, se miran sus páginas con una compilación de prueba
   y nunca sobre la instalación real.

## Lo que no se cubre

- Nada contra los servicios de verdad (TikTok, Euler, Microsoft, Spotify, YouTube):
  sus respuestas están simuladas con la forma documentada o medida.
- La interfaz de React no tiene pruebas automáticas; se comprueba a mano y por el
  protocolo de DevTools en la app real.
- El código C# de los mods de GTA V y R.E.P.O. y el plugin de Minecraft tienen sus
  propias pruebas y verificaciones dentro del juego, fuera de `tests/`.
- Los efectos sobre Windows (ganchos, drivers, ventanas transparentes) dependen del
  sistema real y se verifican a mano.
