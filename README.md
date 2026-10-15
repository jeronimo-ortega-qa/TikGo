# TikGO · Proyecto de QA

Plan de pruebas, pruebas automatizadas, registro de defectos y auditoría de
**TikGO**, una aplicación de escritorio para streamers de TikTok LIVE que convierte
lo que hace la audiencia (comentarios, regalos, likes, seguidores) en voz
sintetizada, sonidos, superposiciones para OBS y efectos dentro de los juegos.

**Estado:** beta cerrada · versión 0.15.0 · probada entre agosto y octubre de 2026

## 1. Información general

| | |
|---|---|
| **Nombre del proyecto** | TikGO · QA de una app de escritorio para TikTok LIVE |
| **Especialidad** | QA |
| **Fuente del proyecto** | App propia, en desarrollo: Electron (Node.js y React) para Windows, con mods para Minecraft, R.E.P.O. y GTA V |
| **Fuente original** | [`jeronimo-ortega-qa/Tikgo-code`](https://github.com/jeronimo-ortega-qa/Tikgo-code), **privado**: el código no se publica. Acceso a pedido, para revisarlo (ver [Acceso al código](#acceso-al-código)) |
| **Proyecto publicado** | [`jeronimo-ortega-qa/TikGo`](https://github.com/jeronimo-ortega-qa/TikGo), este repositorio |

## 2. Objetivo

Un streamer no puede enterarse de un fallo cuando ya está en vivo: si la conexión
con TikTok se cae en silencio, se apagan a la vez la voz, los sonidos, las metas y
el chat, delante de su audiencia. El plan de pruebas busca evitar que eso llegue a un
live, y además que la app cuente mal el dinero de los regalos, que trate datos de la
audiencia sin permiso o que deje la PC del streamer con el teclado o el control
trabados.

## 3. Plan de trabajo

| Paso | Qué se hizo | Dónde está |
|---|---|---|
| **1. Análisis de requisitos** | Los requisitos de cada sección de la app y los de las leyes de privacidad (RGPD, leyes de Argentina, Brasil, México y Chile, COPPA y fallos del TJUE). Con eso, los riesgos ordenados por impacto: que la app no se quede muda en vivo, que cuente bien el dinero, que no trate datos sin permiso, que aguante un live grande y que no rompa la máquina del streamer. | [Plan de pruebas](docs/qa/plan-de-pruebas.md#qué-se-prueba-primero) · [Privacidad](docs/privacidad-y-seguridad.md) |
| **2. Diseño de casos de prueba** | 72 pruebas automatizadas, cada una atada al código de un defecto, y 16 casos manuales con preparación, pasos y resultado esperado para lo que solo se comprueba en vivo, en OBS o con el juego abierto. | [Casos manuales](docs/qa/casos-manuales.md) · [Pruebas incluidas](#pruebas-incluidas) |
| **3. Ejecución** | Pruebas exploratorias en la app real y en lives controlados; los 49 efectos originales de GTA V probados uno por uno dentro del juego; mediciones de CPU, memoria y disco con la configuración real; y la suite automatizada. | [Registro de defectos](docs/qa/registro-de-defectos.md) · [Matriz de verificación](docs/qa/matriz-de-verificacion.md) |
| **4. Evaluación** | Cobertura de código, pruebas de mutación para comprobar que cada prueba detecta su error, y los defectos clasificados por severidad y estado. | [Informe de la 0.15.0](docs/qa/informe-0.15.0.md) |
| **5. Conclusiones y próximos pasos** | Lo aprendido, lo que falta y los bloqueantes antes de distribuir. | [Conclusiones](#7-conclusiones) |

## 4. Preguntas clave

**¿Qué pasa si la app se queda muda en medio de un live?**
Pasó: a los 10-20 minutos dejaban de andar la voz, los sonidos y el registro, todos
juntos y sin ningún error. Tenía tres causas (errores de la biblioteca que llegaban
envueltos, reintentos que se cortaban solos y un socket abierto que dejaba de mandar
datos) y cada una tiene hoy su prueba ([CON-01 a CON-07](docs/qa/defectos/conexion.md)).
Falta confirmarlo en un live largo de verdad (caso CM-01).

**¿Qué pasa si un dato de un espectador llega a donde no debe?**
Frente a su audiencia, el streamer es el responsable de esos datos. La voz tapa
correos y teléfonos antes de mandar el texto a Microsoft, y cualquier espectador puede
escribir `!olvidame`. Al rehacer las pruebas apareció que un mensaje recortado dejaba
medio teléfono a la vista ([VOZ-04](docs/qa/defectos/voz-regalos-y-chatbot.md)).

**¿Qué pasa si la app se distribuye así como está?**
No se puede todavía: dos dependencias tienen licencia AGPL-3.0, que choca con el
código cerrado, y el código del proceso principal viaja legible dentro del instalador
([LIC-01, LIC-02 y SEG-03](docs/qa/defectos/privacidad-seguridad-y-licencias.md)).
Por eso el instalador no está publicado.

## 5. Qué se hizo y cómo

**Tipos de prueba**

| Tipo | Qué cubre |
|---|---|
| Estáticas | Reglas del código que, rotas, fallan en silencio: variables de color sin definir, listas que tienen que coincidir entre la app y el mod, puertos abiertos a la red, la trazabilidad del registro de defectos |
| Unitarias | Una pieza sola: combos de regalos, umbrales de likes, contadores de espectadores, detección de idioma, tapado de datos, edad, saldos con tope, descargas verificadas |
| Integración de punta a punta | Eventos con la forma real de TikTok pasando por todos los módulos de la app: un live simulado entero |
| Mutación | Se vuelve a poner cada error corregido y se exige que su prueba falle |
| Exploratorias y en vivo | La app real en Windows, lives controlados y los juegos abiertos |
| No funcionales | CPU, memoria y disco con la configuración real y un live simulado lleno |
| Seguridad y licencias | Conexiones de red, cambios en Windows, dependencias con su licencia (SBOM) y hashes del instalador |

**Herramientas:** el corredor de pruebas de Node.js (`node:test` y `node:assert`),
dobles de prueba propios para Electron, TikTok y la voz de Microsoft, la cobertura
integrada de Node, `npm sbom` (CycloneDX), el protocolo de DevTools de Chromium para
medir la interfaz, y Git y GitHub con plantillas de reporte.

**El entorno de las pruebas** corta la red y bloquea los procesos del sistema, y usa
una carpeta de datos temporal: ninguna prueba puede tocar la configuración real, la
PC ni un juego, y corren en cualquier máquina sin cuentas ni claves.

**Criterios de aceptación de una versión:** todas las pruebas pasan, todas las
mutaciones se detectan, la interfaz compila, y no aparece ningún defecto abierto de
severidad S1 nuevo respecto de la versión anterior.
[Detalle](docs/qa/plan-de-pruebas.md#criterio-para-sacar-una-versión).

## 6. Resultados

| | Resultado |
|---|---|
| Pruebas automatizadas | **72 de 72** pasan, en unos 6 segundos |
| Pruebas de mutación | **16 de 16** errores reintroducidos fueron detectados |
| Cobertura de código (`src/main`) | **48 %** de las líneas, **70 %** de las ramas, **36 %** de las funciones |
| Defectos registrados | **136** entradas |
| Casos manuales | 16 diseñados, ninguno ejecutado todavía |

**Defectos por severidad y estado**

| Severidad | Corregidos | Abiertos | Mitigados, por diseño o no reproducibles | Requisitos y controles que se cumplen | Total |
|---|---|---|---|---|---|
| **S1** · crítica | 15 | 3 | — | 9 | 27 |
| **S2** · alta | 36 | 5 | 1 | 6 | 48 |
| **S3** · media | 32 | 13 | 4 | 1 | 50 |
| **S4** · baja | 1 | 7 | 2 | 1 | 11 |
| **Total** | **84** | **28** | **7** | **17** | **136** |

**Encontrados probando en vivo o con el juego abierto**, entre otros:

- La app dejaba de funcionar a los 10-20 minutos de conectada (CON-03).
- "Voltear pantalla" se veía en la PC pero no en el live: la grabación de TikTok
  LIVE Studio salía derecha (JUE-03).
- El mouse invertido mandaba el cursor a una esquina y no lo dejaba salir (JUE-04).
- Después de probar el bloqueo de botones, el DualSense dejó de andar en todo el
  sistema (JUE-06).
- Regalos grandes que no caían en el tarro de donaciones porque TikTok no siempre
  manda el último tick del combo (REG-01).
- En Minecraft, la TNT no explotaba (MIN-09); en R.E.P.O., el mod no hacía nada sin
  dar ningún error (REPO-01).

**Encontrados al rehacer la suite automatizada** (octubre de 2026): la voz mandaba
medio teléfono a Microsoft (VOZ-04), un teléfono con paréntesis quedaba a medio tapar
(PRI-04), un método faltante en la api de respaldo de la interfaz (INT-03) y binarios de
terceros que el `.gitignore` dejaba pasar (SEG-05). Las cuatro tenían una prueba que
fallaba antes de corregirlas.

### Pruebas incluidas

<details>
<summary>Las 72 pruebas, por archivo</summary>

**Estáticas · reglas del código**

| Código | Qué comprueba |
|---|---|
| ARR-02 | El esquema `tikgo-media` se registra antes de que la app esté lista |
| ARR-03 | Cada módulo que pide `handlers.js` está en `MODULOS_EN_ORDEN` |
| INT-01 | Toda variable de color que se usa está definida |
| INT-03 | La api de respaldo de la interfaz tiene cada método del preload |
| GTA-13 | Cada efecto del catálogo de GTA V tiene su caso en el mod, y al revés |
| SEG-04 | Todo servidor escucha solo en 127.0.0.1 |
| PRI-10 | Cada módulo que guarda algo por espectador está registrado en Privacidad |
| PRI-11 | Todo evento del live pasa por el filtro de privacidad antes de repartirse |
| SEG-05 | El `.gitignore` deja afuera binarios de terceros, compilados y secretos |

**Estáticas · trazabilidad del registro de defectos** (6): cada código es único; el
índice está al día; cada prueba nombra un defecto que existe; cada defecto que dice
tener prueba automática la tiene; cada mutación está declarada; cada caso manual
nombrado existe.

**Unitarias · combos y umbrales**

| Código | Qué comprueba |
|---|---|
| REG-01 | Un combo cuyo tick final no llega se cierra solo, una vez y con el último total |
| REG-01 | Un regalo que no es combo sale en el acto |
| REG-02 | El tick final que llega tarde no cuenta el mismo combo dos veces |
| REG-02 | Un contador más chico después del cierre es un combo nuevo |
| REN-08 | "Cada 50 likes" sale de comparar dos totales, sin guardar nada por espectador |
| REG-05 | Quien vuelve a entrar y TikTok reenvía su "follow" no dispara otra vez |
| REG-05 | El registro de seguidores tiene tope |
| REN-04 | Trescientos avisos seguidos se mandan en dos o tres |

**Unitarias · contadores y reglas**

| Código | Qué comprueba |
|---|---|
| REN-09 | 200.000 espectadores distintos se cuentan con 4 KB fijos y error menor al 3 % |
| REN-09 | "Ya vino a otro live" con 1 MB fijo: nunca olvida y se equivoca menos del 1 % |
| CON-05 | Una reconexión es el mismo live salvo sala nueva y 30 minutos sin recibir nada |
| VOZ-05 | El idioma sale primero de TikTok, después del alfabeto y después de las palabras |
| PRI-04 | La voz tapa correos, enlaces y teléfonos, y deja los números cortos |
| PRI-06 | La edad cuenta el mes de nacimiento como no cumplido y rechaza fechas imposibles |

**Unitarias · descargas y Java**

| Código | Qué comprueba |
|---|---|
| MIN-02 | La descarga sigue redirecciones en vez de guardar una página vacía |
| MIN-03 | Un archivo con otro hash se rechaza y no queda nada a medias |
| MIN-04 | Una conexión que deja de mandar corta la descarga en vez de trabar la barra |
| MIN-04 | Un bucle de redirecciones termina con un error |
| MIN-05 | Java 8 se lee como 8 (no como "1") y la versión se lee de stderr |

**Unitarias · saldos, catálogo y migraciones**

| Código | Qué comprueba |
|---|---|
| VOZ-02 | El monedero de la voz topa en 5.000 y descarta al que menos tiene |
| VOZ-02 | Preguntar si alguien es top gifter no ordena a todos en cada comentario |
| VOZ-03 | La voz ignora los ticks intermedios de un combo aunque le lleguen sueltos |
| REN-10 | El libro de Puntos descarta al que menos tiene, nunca al más viejo con saldo |
| REG-03 | El mismo regalo con un id distinto en cada región se reconoce como el mismo |
| REN-01 | Un regalo que ya se conocía no reescribe el catálogo |
| REN-02 | El catálogo combinado se arma una vez y se invalida solo al cambiar |
| INT-02 | Una Acción guardada antes de que existiera un campo se abre con sus valores por defecto |
| INT-04 | Cada efecto de Juegos Interactivos tiene ícono y etiqueta en la interfaz |

**Integración · la conexión al live**

| Código | Qué comprueba |
|---|---|
| CON-01 | Un error envuelto en `{info, exception}` se clasifica por lo que es |
| CON-01 | Si Euler frena, se espera lo que Euler pide |
| CON-02 | Si el reintento falla sin abrir el socket, la cadena se rearma sola |
| CON-03 | Un socket abierto que deja de mandar se reconecta a la fuerza |
| CON-03 | Cualquier trama recibida cuenta como vida, aunque no sea un evento |
| CON-04 | Una reconexión a la misma sala no pone metas ni contadores en cero |
| CON-05 | Otra sala después de la pausa sí es un live nuevo, y antes no |
| CON-06 | "Desconectar" cancela el reintento que ya estaba en camino |
| CON-07 | Con la sesión vencida no se reintenta y la sesión se borra |
| REG-04 | Un comentario repetido se descarta, pero cada tick de un combo llega |
| CHB-01 | `{espectadores}` muestra el número de la sala, no `[object Object]` |

**Integración · un live simulado de punta a punta**

| Código | Qué comprueba |
|---|---|
| PRI-01 | Sin edad ni aviso aceptados la app no se conecta |
| PRI-06 | Una fecha imposible no cuenta como respuesta |
| PRI-01 | Con edad y aviso aceptados se conecta, y no se guarda la fecha de nacimiento |
| PRI-04 | Microsoft recibe el teléfono y el correo tapados |
| VOZ-03 | Un combo de 20 rosas suma 20 monedas, no la suma de los ticks |
| VOZ-01 | Una síntesis que no vuelve no deja la voz muda el resto del stream |
| VOZ-04 | Recortar un mensaje largo no deja medio teléfono a la vista |
| PRI-09 | `!privacidad` responde una vez y no inunda el chat |
| PRI-02 | `!olvidame` borra a la espectadora de todos los módulos y la excluye |
| PRI-03 | Lo que escribe una persona excluida no llega a ningún lado y sus regalos cuentan como Anónimo |
| PRI-08 | Con "Conectar con tu sesión" apagado, la cookie no viaja a Euler |
| SEG-06 | Durante todo el live simulado no salió nada de la máquina salvo lo previsto |

**Integración · menores, secretos y borrado**

| Código | Qué comprueba |
|---|---|
| PRI-07 | Una clave de Euler vieja en texto plano se vuelve a guardar cifrada al leerla |
| PRI-07 | Si Windows no ofrece cifrado, la clave no se pierde |
| PRI-06 | Un menor de 18 queda bloqueado y no puede volver a responder hasta el día siguiente |
| PRI-05 | Exportar todos los datos no incluye claves, tokens ni sesiones |
| PRI-12 | "Borrar todo" borra los datos al arrancar y deja lo que no es de la audiencia |
| JUE-06 | Al cargar, la app suelta un control que HidHide pudo haber dejado oculto |

</details>

```text
$ npm test
ℹ tests 72
ℹ pass 72
ℹ fail 0

$ npm run test:mutaciones
CON-01: detectada (fallan: CON-01, CON-07)
CON-02: detectada (fallan: CON-02)
…
PRI-04: detectada (fallan: PRI-04)

$ npm run test:cobertura
ℹ file       | line % | branch % | funcs %
ℹ all files  |  47.65 |    69.65 |   35.62
```

## 7. Conclusiones

**Qué aprendí**

- **"Funciona" no es lo mismo que "le llega al usuario".** "Voltear pantalla" giraba
  mi monitor sin problemas, pero en la grabación de TikTok LIVE Studio salía derecho:
  la audiencia nunca lo veía. Desde entonces el resultado esperado de cada prueba es lo
  que ve el usuario final, no lo que pasa en mi máquina.
- **Los defectos más graves no daban ningún error.** La app que se quedaba muda a los
  10-20 minutos no mostraba nada: el reporte era "dejó de funcionar". Convertir un síntoma
  así en una prueba reproducible (un socket que deja de mandar datos sin cerrarse) fue
  lo que permitió encontrar las tres causas, y llevó a que todo fallo llegue a la
  pantalla de Inicio en vez de quedarse en una consola que nadie ve.
- **Una causa que suena razonable no es una causa verificada.** Tres errores tenían
  anotada una causa que resultó equivocada, y uno se "arregló" cinco veces con teorías
  distintas antes de descubrir que no era un error. Ahora primero va la prueba que lo
  reproduce y después el arreglo.
- **Probar la pieza no alcanza: hay que probar la cadena.** El interruptor que apaga
  una sección no apagaba nada: el código nuevo nunca se insertó, y la prueba de la
  pieza pasaba igual. Lo agarró disparar el evento y contar los efectos.
- **Las pruebas también se prueban.** Una prueba de la voz pasaba aunque la defensa que
  decía probar no existiera, porque otra pieza le hacía el trabajo. La prueba de
  mutación lo mostró, y hoy cada error corregido se vuelve a poner para confirmar que su
  prueba lo detecta.
- **Lo no funcional pesa tanto como lo funcional.** Un streamer ya tiene la PC al
  límite: medí 1 GB de memoria después de un jumpscare y 5,9 % de CPU por una sola
  alerta. Y aprendí que medir con la configuración vacía no mide nada, porque el código
  caro ni se ejecuta.
- **El entorno de prueba tiene que estar aislado.** Probar contra la máquina real me
  dejó el control oculto para todo Windows y una vez instaló una versión encima de la
  mía. Además, los scripts de prueba de los primeros meses se perdieron por vivir en una
  carpeta temporal. Hoy las pruebas viven en el repositorio, cortan la red y los
  procesos, y usan una carpeta de datos propia.
- **Trabajar con un asistente de IA que escribe el código obliga a verificar todo.**
  La herramienta escribió buena parte del código siguiendo mis instrucciones y propuso
  causas que sonaban bien y eran falsas. Mi trabajo fue definir qué tenía que pasar,
  probarlo en vivo, reportar lo que veía y no aceptar un arreglo sin evidencia.

**Qué mejoraría con más tiempo o recursos**

- Ejecutar los 16 casos manuales en lives reales, empezando por el live largo con un
  corte de Wi-Fi (CM-01).
- Subir la cobertura del 48 %: la interfaz de React no tiene pruebas automatizadas, ni
  los efectos sobre Windows.
- Correr las pruebas en cada cambio con integración continua (GitHub Actions).
- Llevar los defectos en un tracker desde el primer día, con severidad y prioridad, en
  vez de reconstruir el registro al final.
- Auditar las licencias de las dependencias al empezar: las dos AGPL aparecieron recién
  al final y hoy bloquean distribuir la app.

**Qué contaría en una entrevista**

- La app que se quedaba muda a los 10-20 minutos de live: un síntoma vago en producción,
  tres causas, una prueba para cada una y mutaciones que demuestran que esas pruebas
  detectan la regresión.
- "Voltear pantalla": cómo una grabación del propio live cambió el criterio de
  aceptación de toda la sección.
- La auditoría final: rehacer la suite automatizada encontró cuatro defectos nuevos,
  uno de ellos de privacidad, y un riesgo de licencias que nadie había mirado.

## Cómo está organizado este repositorio

| | |
|---|---|
| [Plan de pruebas](docs/qa/plan-de-pruebas.md) | Riesgos, niveles, entorno, reglas y criterio de salida |
| [Registro de defectos](docs/qa/registro-de-defectos.md) | Los 136, con síntoma, causa, corrección y verificación, por área |
| [Matriz de verificación](docs/qa/matriz-de-verificacion.md) | Qué función se comprobó dónde: simulado, app real, juego, OBS, live |
| [Casos manuales](docs/qa/casos-manuales.md) | Lo que falta comprobar en vivo |
| [Informe de la 0.15.0](docs/qa/informe-0.15.0.md) | El resultado de la última corrida completa |
| [Auditoría](docs/auditoria/README.md) | Conexiones de red, cambios en Windows, dependencias, SBOM y hashes |
| [Privacidad y seguridad](docs/privacidad-y-seguridad.md) | Qué datos de la audiencia se tratan y con qué base legal |

Las referencias a `src/`, `tests/`, `CLAUDE.md` y a otros documentos de `docs/`
apuntan al repositorio privado.

## Acceso al código

El código de la app y de las pruebas es privado. Quien necesite revisarlo puede pedir
acceso abriendo un issue con la plantilla "Pedir acceso para auditar". Con
ese acceso se repiten todas las pruebas con `npm ci` y `npm test`, sin cuentas ni
claves.

## Reportar un error o una vulnerabilidad

- **Error**: con la plantilla "Reporte de error" de la pestaña Issues. No pegues claves,
  sesiones ni nombres de tu audiencia.
- **Vulnerabilidad**: en privado, desde **Security › Report a vulnerability**. Ver
  [SECURITY.md](SECURITY.md).

## Verificar un instalador

El instalador todavía no está firmado. Su SHA-256 está en
[docs/auditoria/integridad.md](docs/auditoria/integridad.md):

```powershell
Get-FileHash "TikGO Setup 0.15.0.exe" -Algorithm SHA256
```

## Autoría y derechos

TikGO es obra de **Jerónimo Ortega Sosa**, que la concibió, la diseñó, la dirige y la
prueba en vivo. Buena parte del código se escribió con asistencia de herramientas de
inteligencia artificial bajo su dirección y revisión.

© 2026 Jerónimo Ortega Sosa. Todos los derechos reservados. Ver [LICENSE](LICENSE).
Los componentes de terceros conservan sus propias licencias
([dependencias](docs/auditoria/dependencias.md)).
