# Registro de defectos

Todo error conocido de TikGO: los corregidos, los mitigados, los abiertos y los que
dependen de un servicio de afuera. Cada uno dice cómo se verificó. El detalle
(síntoma, causa, corrección y prueba) está en el archivo de su área, en
`defectos/`.

La tabla de abajo se genera con `node scripts/indice-de-defectos.js` a partir de
esos archivos; `npm test` falla si quedó vieja.

## Cómo leerlo

**Severidad**

| | Cuándo |
|---|---|
| **S1** · crítica | Deja sin funcionar lo que la app hace en vivo, pierde o expone datos, o impide distribuir. |
| **S2** · alta | Una sección deja de funcionar o hace algo distinto de lo que dice. |
| **S3** · media | Funciona, pero con un costo alto o falla en un caso común que tiene salida. |
| **S4** · baja | Cosmético, caso de borde o mejora. |

**Estado**: *Corregido* · *Mitigado* (el daño está acotado, la causa sigue) ·
*Abierto* · *No reproducible* · *Por diseño* (límite de un servicio externo) ·
*Requisito* y *Control* (algo que tiene que seguir cumpliéndose y tiene su prueba).

**Verificación**

| | Qué significa |
|---|---|
| **Automática** | Hay una prueba en `tests/` con el mismo código en su nombre. Corre con `npm test`. |
| **Mutación** | `npm run test:mutaciones` vuelve a poner el error y comprueba que la prueba falle. |
| **Windows real** | Comprobado a mano en la máquina de desarrollo. |
| **Dentro del juego** | Comprobado con el juego abierto, leyendo su registro o mirando la pantalla. |
| **Navegador** | La página real de la superposición, abierta en un navegador. |
| **Medición** | Números de antes y después, en `docs/auditoria-rendimiento.md`. |
| **Caso manual CM-xx** | Falta comprobarlo en vivo, en OBS o con el juego: ver `casos-manuales.md`. |

## Índice

<!-- indice:desde -->
**152 entradas.** Corregido: 100 · Abierto: 28 · Por diseño: 2 · Control: 5 · Mitigado: 4 · No reproducible: 1 · Requisito: 12.

**Abiertos de severidad S1 o S2: 8** (REPO-07, GTA-16, JUE-17, SEG-03, SEG-08, LIC-01, LIC-02, LIC-03).

| ID | Qué | Sev. | Estado | Verificación |
|---|---|---|---|---|
| [CON-01](defectos/conexion.md) | Los errores de la librería llegaban envueltos y se clasificaban mal | S1 | Corregido | Automática y mutación (`tests/cadena/conexion.test.js`) |
| [CON-02](defectos/conexion.md) | Un reintento fallido cortaba la cadena de reintentos | S1 | Corregido | Automática y mutación |
| [CON-03](defectos/conexion.md) | Un socket abierto y mudo dejaba la app esperando para siempre | S1 | Corregido · falta confirmar en un live largo | Automática y mutación · caso manual CM-01 |
| [CON-04](defectos/conexion.md) | Cada reconexión ponía metas, temporizador y contadores en cero | S2 | Corregido | Automática y mutación |
| [CON-05](defectos/conexion.md) | El live siguiente heredaba las metas y los contadores del anterior | S2 | Corregido | Automática (unidad y cadena) |
| [CON-06](defectos/conexion.md) | "Desconectar" no cancelaba un reintento que ya estaba en camino | S3 | Corregido | Automática y mutación |
| [CON-07](defectos/conexion.md) | Con la sesión vencida la app reintentaba sin fin | S3 | Corregido | Automática |
| [CON-08](defectos/conexion.md) | La consulta de la cuenta con una sesión real no está verificada | S3 | Abierto | Caso manual CM-02 |
| [INT-01](defectos/interfaz-y-arranque.md) | Una variable de color sin definir dejaba un elemento invisible | S3 | Corregido | Automática (estática) |
| [INT-02](defectos/interfaz-y-arranque.md) | Un campo nuevo en un store dejaba la pantalla en blanco con lo guardado de antes | S2 | Corregido | Automática |
| [INT-03](defectos/interfaz-y-arranque.md) | La api de respaldo del navegador escondía errores | S3 | Corregido (y otro caso en esta auditoría) | Automática (estática) |
| [INT-04](defectos/interfaz-y-arranque.md) | Un efecto nuevo sin metadatos figuraba como "Sin efectos" | S3 | Corregido | Automática |
| [INT-05](defectos/interfaz-y-arranque.md) | El interruptor general de una sección no apagaba nada | S2 | Corregido | Verificado de punta a punta al corregirlo · sin prueba automática |
| [INT-06](defectos/interfaz-y-arranque.md) | Un número fuera de rango se guarda como el valor por defecto, sin avisar | S4 | Por diseño · regla para los formularios | Documentado en `CLAUDE.md` |
| [INT-07](defectos/interfaz-y-arranque.md) | Los modales se cerraban al hacer clic en el fondo | S3 | Corregido | Revisión de código |
| [ARR-01](defectos/interfaz-y-arranque.md) | Pantalla oscura varios segundos al abrir la app en frío | S3 | Corregido (0.14.1) | Medición en la app real · `docs/arranque.md` |
| [ARR-02](defectos/interfaz-y-arranque.md) | Registrar el esquema `tikgo-media` al cargarse rompió el arranque | S1 | Corregido (0.14.1) | Automática (estática) |
| [ARR-03](defectos/interfaz-y-arranque.md) | Un módulo fuera de `MODULOS_EN_ORDEN` carga de un tirón | S4 | Control · cumple | Automática (estática) |
| [ARR-04](defectos/interfaz-y-arranque.md) | El proceso quedaba vivo al cerrar la app | S2 | Corregido (2026-08-26) | Windows real |
| [ARR-05](defectos/interfaz-y-arranque.md) | El instalador no compilaba al personalizar el aviso de "la app está abierta" | S3 | Corregido (0.14.1) | Compilación del instalador |
| [ARR-06](defectos/interfaz-y-arranque.md) | El cierre ordenado al actualizar todavía no se vio funcionar | S3 | Abierto | Caso manual CM-10 |
| [MIN-01](defectos/juegos-con-mod.md) | La API v2 de PaperMC está dada de baja | S1 | Corregido | Contra el servicio real |
| [MIN-02](defectos/juegos-con-mod.md) | Adoptium redirige a GitHub y la descarga guardaba un HTML vacío | S2 | Corregido | Automática (servidor local) |
| [MIN-03](defectos/juegos-con-mod.md) | Un archivo a medias o distinto podía pasar por bueno | S1 | Corregido | Automática y mutación |
| [MIN-04](defectos/juegos-con-mod.md) | Una descarga colgada dejaba la barra de avance trabada | S2 | Corregido | Automática |
| [MIN-05](defectos/juegos-con-mod.md) | Java 8 se leía como "Java 1" y `java -version` escribe en stderr | S2 | Corregido | Automática y mutación |
| [MIN-06](defectos/juegos-con-mod.md) | Un jar viejo en el servidor hacía cosas viejas sin avisar | S2 | Corregido | Contra un Paper real |
| [MIN-07](defectos/juegos-con-mod.md) | El plugin quedaba afuera para siempre desde el segundo arranque | S1 | Corregido | Contra un Paper real |
| [MIN-08](defectos/juegos-con-mod.md) | "Día" y "Noche" no hacían nada | S2 | Corregido | Los 56 comandos contra un Paper real |
| [MIN-09](defectos/juegos-con-mod.md) | La TNT puesta no explotaba | S3 | Corregido | Contra un Paper real |
| [MIN-10](defectos/juegos-con-mod.md) | El servidor se cerraba con código 1 por el Java equivocado | S2 | Corregido | Contra un Paper real |
| [MIN-11](defectos/juegos-con-mod.md) | Un espectador podía pintar su mensaje o imitar avisos del servidor | S3 | Corregido | Contra un Paper real |
| [MIN-12](defectos/juegos-con-mod.md) | Falta probarlo con otros jugadores conectados | S3 | Abierto | Prueba de campo (2026-09-07) · caso manual CM-12 |
| [MIN-13](defectos/juegos-con-mod.md) | Un combo de rosas mandaba un cartel por cada rosa | S3 | Corregido (0.6.2) | Prueba de campo (2026-09-07 y 2026-09-09) · prueba de punta a punta al corregirlo |
| [MIN-14](defectos/juegos-con-mod.md) | La jaula de TNT no atrapaba al streamer si se estaba moviendo | S3 | Corregido (0.6.2) | Prueba de campo (2026-09-07) |
| [MIN-15](defectos/juegos-con-mod.md) | Los rayos caían al costado y no hacían nada | S3 | Corregido (0.6.3) | Prueba de campo (2026-09-07) |
| [REPO-01](defectos/juegos-con-mod.md) | `Update` no se llamaba nunca y no había ningún error | S1 | Corregido (2026-09-04) | Dentro del juego (registro del mod) |
| [REPO-02](defectos/juegos-con-mod.md) | El puente quedaba mudo después de un cambio de escena | S1 | Corregido | Dentro del juego |
| [REPO-03](defectos/juegos-con-mod.md) | El catálogo de enemigos quedaba vacío para siempre | S2 | Corregido | Dentro del juego |
| [REPO-04](defectos/juegos-con-mod.md) | Los objetos y enemigos no aparecían o aparecían dentro de las paredes | S2 | Corregido | Dentro del juego |
| [REPO-05](defectos/juegos-con-mod.md) | Un puerto ocupado se veía como "el mod no está conectado" | S3 | Corregido | Prueba con un cliente TCP falso |
| [REPO-06](defectos/juegos-con-mod.md) | Empujar al jugador no hacía nada | S3 | Corregido | Dentro del juego |
| [REPO-07](defectos/juegos-con-mod.md) | Falta probar cada efecto y que entre un jugador sin el mod | S2 | Abierto | Caso manual CM-13 |
| [GTA-01](defectos/juegos-con-mod.md) | El mod escribía una línea de registro cada 7 s sin tope | S3 | Corregido (2026-09-16) | Prueba en C# (fuera de `tests/`) |
| [GTA-02](defectos/juegos-con-mod.md) | Dos compilaciones distintas del mod pesan lo mismo | S2 | Corregido | Dentro del juego |
| [GTA-03](defectos/juegos-con-mod.md) | El mod no encontraba su configuración y no dejaba rastro | S1 | Corregido | Dentro del juego |
| [GTA-04](defectos/juegos-con-mod.md) | Los efectos de cada cuadro corrían en uno de cada cuatro | S2 | Corregido | Dentro del juego, con sonda |
| [GTA-05](defectos/juegos-con-mod.md) | "Apagar motor" duraba hasta que el jugador aceleraba | S3 | Corregido | Dentro del juego, con sonda |
| [GTA-06](defectos/juegos-con-mod.md) | Los autos quedaban sin agarre después del efecto | S3 | Corregido | Dentro del juego |
| [GTA-07](defectos/juegos-con-mod.md) | El `null` de JSON llegaba como el texto "null" | S3 | Corregido | Prueba en C# (fuera de `tests/`) |
| [GTA-08](defectos/juegos-con-mod.md) | Con el juego en pausa los efectos se juntan y salen de golpe | S3 | Mitigado (límite del juego) | Dentro del juego |
| [GTA-09](defectos/juegos-con-mod.md) | Un efecto mandado con el jugador muerto se perdía y respondía "ok" | S2 | Corregido | Dentro del juego |
| [GTA-10](defectos/juegos-con-mod.md) | "Meterte en el vehículo" no encontró el auto que estaba al lado | S3 | Mitigado · causa sin confirmar | Dentro del juego |
| [GTA-11](defectos/juegos-con-mod.md) | Un enemigo kamikaze desapareció el mismo segundo en que salió | S3 | Corregido | Dentro del juego |
| [GTA-12](defectos/juegos-con-mod.md) | "En llamas" no prendía fuego | S4 | Corregido · falta confirmar | Dentro del juego, parcial |
| [GTA-13](defectos/juegos-con-mod.md) | La lista de efectos de la app y la del mod pueden separarse | S2 | Control · cumple | Automática (estática) |
| [GTA-14](defectos/juegos-con-mod.md) | El reloj de los efectos corría con el juego en pausa | S3 | Corregido | Prueba en C# (fuera de `tests/`) |
| [GTA-15](defectos/juegos-con-mod.md) | El minipanel decía "Listo" con un mod que no se podía conectar | S2 | Corregido | Dentro del juego |
| [GTA-16](defectos/juegos-con-mod.md) | 21 efectos nunca se ejecutaron dentro del juego | S2 | Abierto | Caso manual CM-14 |
| [GTA-17](defectos/juegos-con-mod.md) | Un efecto con duración se vencía mientras el jugador estaba muerto | S3 | Corregido (0.13.2) | Prueba de campo (2026-09-20) · dentro del juego |
| [GTA-18](defectos/juegos-con-mod.md) | "Reinstalar el mod" volvía a poner la versión vieja | S2 | Corregido (0.13.2) | Prueba de campo (2026-09-20) · registro del mod |
| [GTA-19](defectos/juegos-con-mod.md) | Los enemigos que manda la audiencia salían corriendo en vez de pelear | S2 | Corregido (0.13.2) · sin reconfirmar en vivo | Prueba de campo (2026-09-20) · dentro del juego |
| [GTA-20](defectos/juegos-con-mod.md) | Los enemigos se atacaban entre ellos | S3 | Corregido (0.13.2) | Prueba de campo (2026-09-20) · dentro del juego |
| [GTA-21](defectos/juegos-con-mod.md) | El teletransporte "al azar" mandaba muy cerca | S4 | Corregido (0.13.2) | Prueba de campo (2026-09-20) · dentro del juego |
| [REPO-08](defectos/juegos-con-mod.md) | R.E.P.O. todavía decide "al día" por el tamaño del DLL | S3 | Abierto | Revisión de código |
| [JUE-01](defectos/juegos-interactivos.md) | El jumpscare no se veía | S1 | Corregido (2026-08-26) | Windows real |
| [JUE-02](defectos/juegos-interactivos.md) | "Voltear pantalla" no funcionaba | S2 | Corregido (2026-08-26) | Windows real |
| [JUE-03](defectos/juegos-interactivos.md) | Rotar el monitor no se ve en el stream | S2 | Corregido (2026-09-18) | Grabación de Live Studio · sonda de captura |
| [JUE-04](defectos/juegos-interactivos.md) | El mouse invertido mandaba el cursor a una esquina | S2 | Corregido | Windows real |
| [JUE-05](defectos/juegos-interactivos.md) | El cursor se trababa contra el borde con el mouse invertido | S3 | Mitigado (no resuelto de fondo) | Windows real |
| [JUE-06](defectos/juegos-interactivos.md) | Matar el ayudante del control dejaba el mando oculto en todo el sistema | S1 | Corregido | Windows real · automática (limpieza al cargar) |
| [JUE-07](defectos/juegos-interactivos.md) | Bloquear L2 o R2 se guardaba bien y no hacía nada | S2 | Corregido | Windows real, leyendo por XInput |
| [JUE-08](defectos/juegos-interactivos.md) | Los listeners del video se acumulaban en cada disparo | S3 | Corregido | Medición con el HTML real |
| [JUE-09](defectos/juegos-interactivos.md) | La memoria de video no volvía después de un jumpscare | S3 | Corregido | Medición |
| [JUE-10](defectos/juegos-interactivos.md) | Simular teclas con SendKeys no apretaba teclas | S2 | Corregido | Windows real, con un gancho contador |
| [JUE-11](defectos/juegos-interactivos.md) | "Mantener tecla" repetía a 14 por segundo en vez de 30 | S3 | Corregido | Medición |
| [JUE-12](defectos/juegos-interactivos.md) | Una tecla quedaba apretada al matar el proceso que la mantenía | S2 | Corregido | Windows real |
| [JUE-13](defectos/juegos-interactivos.md) | Las tarjetas de la superposición de Juegos Interactivos se pisaban | S3 | Corregido | Navegador |
| [JUE-14](defectos/juegos-interactivos.md) | El ajuste automático de la superposición seguía desbordando | S3 | Corregido | Navegador |
| [JUE-15](defectos/juegos-interactivos.md) | El chroma key recortaba dorados y sombras de la figura | S3 | Corregido (0.12.6) | Cuadros reales de los dos jumpscares |
| [JUE-16](defectos/juegos-interactivos.md) | Un archivo que no coincide con el modo del efecto fallaba en silencio | S3 | Corregido | Prueba de punta a punta al corregirlo (no está en `tests/`) |
| [JUE-17](defectos/juegos-interactivos.md) | Los efectos de teclado y mouse no se probaron contra un juego real | S2 | Abierto | Caso manual CM-04 |
| [JUE-18](defectos/juegos-interactivos.md) | Aplicar un preajuste generó 9 Eventos sin Acción una vez | S3 | No reproducible · mitigado | Revisión de código |
| [JUE-19](defectos/juegos-interactivos.md) | Invertir la dirección del mouse: mitigado, no resuelto | S3 | Abierto | Windows real |
| [JUE-20](defectos/juegos-interactivos.md) | "Mantener tecla" apretaba la tecla una sola vez | S2 | Corregido (2026-09-01) | Prueba de campo (2026-09-01) · Windows real, con F13 |
| [JUE-21](defectos/juegos-interactivos.md) | La superposición de Juegos Interactivos no se leía en el stream | S2 | Corregido (2026-09-05) | Prueba de campo (2026-08-27 y 2026-09-05) · navegador |
| [JUE-22](defectos/juegos-interactivos.md) | Los combos no multiplicaban los efectos de un regalo | S2 | Corregido (0.6.1) | Prueba de campo (2026-09-07) · prueba de punta a punta al corregirlo |
| [JUE-23](defectos/juegos-interactivos.md) | "Meta de Likes" se ofrecía en Juegos Interactivos y no estaba hecha | S2 | Corregido (0.6.2) | Prueba de campo (2026-09-07) · prueba de punta a punta al corregirlo |
| [PRI-01](defectos/privacidad-seguridad-y-licencias.md) | Sin edad y aviso aceptados, nada sale hacia TikTok, Euler ni Microsoft | S1 | Requisito · cumple | Automática (`tests/cadena/en-vivo.test.js`) |
| [PRI-02](defectos/privacidad-seguridad-y-licencias.md) | !olvidame borra a la persona de todos los módulos y la excluye | S1 | Requisito · cumple · falta en un live | Automática · caso manual CM-08 |
| [PRI-03](defectos/privacidad-seguridad-y-licencias.md) | Lo de una persona excluida no llega a ningún módulo | S1 | Requisito · cumple | Automática y mutación |
| [PRI-04](defectos/privacidad-seguridad-y-licencias.md) | La voz tapa correos, enlaces, teléfonos y documentos antes de mandarlos | S2 | Requisito · cumple (con corrección en esta auditoría) | Automática y mutación |
| [PRI-05](defectos/privacidad-seguridad-y-licencias.md) | Exportar todos los datos no incluye claves, tokens ni sesiones | S1 | Requisito · cumple | Automática |
| [PRI-06](defectos/privacidad-seguridad-y-licencias.md) | La pregunta de edad es neutral y un menor queda bloqueado | S1 | Requisito · cumple | Automática |
| [PRI-07](defectos/privacidad-seguridad-y-licencias.md) | La clave de Euler va cifrada en disco | S2 | Requisito · cumple | Automática |
| [PRI-08](defectos/privacidad-seguridad-y-licencias.md) | Con "Conectar con tu sesión" apagado, la cookie no viaja a Euler | S1 | Requisito · cumple | Automática |
| [PRI-09](defectos/privacidad-seguridad-y-licencias.md) | !privacidad responde y tiene freno | S3 | Requisito · cumple · falta en un live | Automática · caso manual CM-08 |
| [PRI-10](defectos/privacidad-seguridad-y-licencias.md) | Cada módulo que guarda algo por espectador está registrado | S1 | Requisito · cumple | Automática (estática) |
| [PRI-11](defectos/privacidad-seguridad-y-licencias.md) | Todo evento del live pasa por el filtro antes de repartirse | S1 | Requisito · cumple | Automática (estática) |
| [PRI-12](defectos/privacidad-seguridad-y-licencias.md) | "Borrar todo" borra al arrancar, antes de cargar ningún módulo | S2 | Requisito · cumple | Automática |
| [PRI-13](defectos/privacidad-seguridad-y-licencias.md) | Datos que el borrado todavía no alcanza | S3 | Abierto | Documentado |
| [SEG-01](defectos/privacidad-seguridad-y-licencias.md) | La clave privada del certificado de respaldo es la misma en todas las instalaciones | S3 | Abierto | Revisión de código |
| [SEG-02](defectos/privacidad-seguridad-y-licencias.md) | La clave de la API de YouTube no va cifrada | S4 | Abierto (el aviso lo dice) | Documentado |
| [SEG-03](defectos/privacidad-seguridad-y-licencias.md) | El código del proceso principal va legible dentro del instalador | S2 | Abierto | Revisión del paquete 0.15.0 |
| [SEG-04](defectos/privacidad-seguridad-y-licencias.md) | Todo servidor escucha solo en 127.0.0.1 | S1 | Control · cumple | Automática (estática) |
| [SEG-05](defectos/privacidad-seguridad-y-licencias.md) | El repositorio no lleva binarios de terceros, compilados ni rutas personales | S2 | Control · cumple | Automática (estática) |
| [SEG-06](defectos/privacidad-seguridad-y-licencias.md) | Durante un live simulado no sale nada de la máquina salvo lo previsto | S2 | Control · cumple | Automática (`tests/cadena/en-vivo.test.js`) |
| [SEG-07](defectos/privacidad-seguridad-y-licencias.md) | Instalar el certificado agrega una autoridad raíz a Windows y la pantalla no lo dice | S3 | Abierto | Revisión de código |
| [SEG-08](defectos/privacidad-seguridad-y-licencias.md) | Las superposiciones para Live Studio dependen de `overlay.tikgo.app` | S2 | Abierto | Revisión de código |
| [SEG-09](defectos/privacidad-seguridad-y-licencias.md) | El instalador no está firmado | S3 | Abierto (trámite pago) | `docs/build-windows.md` |
| [SEG-10](defectos/privacidad-seguridad-y-licencias.md) | Los DLL de los mods llevan la ruta de compilación con el usuario de Windows | S4 | Abierto | Revisión de los binarios |
| [LIC-01](defectos/privacidad-seguridad-y-licencias.md) | `tiktok-live-connector` 2.x tiene licencia AGPL-3.0 | S1 | Abierto · bloquea distribuir con código cerrado | SBOM (`docs/auditoria/sbom-produccion.cdx.json`) |
| [LIC-02](defectos/privacidad-seguridad-y-licencias.md) | `edge-tts-universal` tiene licencia AGPL-3.0 | S1 | Abierto · bloquea distribuir con código cerrado | SBOM |
| [LIC-03](defectos/privacidad-seguridad-y-licencias.md) | Edge TTS va contra los términos de Microsoft para uso comercial | S1 | Abierto · bloquea cobrar | `CLAUDE.md`, "Antes de monetizar" |
| [LIC-04](defectos/privacidad-seguridad-y-licencias.md) | Las portadas de juegos viajan en el instalador con los derechos sin resolver | S3 | Abierto | Revisión del paquete |
| [LIC-05](defectos/privacidad-seguridad-y-licencias.md) | El ícono de la moneda de TikTok viaja en la app | S4 | Abierto | Revisión del paquete |
| [REN-01](defectos/rendimiento.md) | Cada regalo reescribía el catálogo de regalos entero | S1 | Corregido (0.12.4) | Automática y mutación · medición |
| [REN-02](defectos/rendimiento.md) | El catálogo combinado se rearmaba y ordenaba en cada consulta | S2 | Corregido (0.12.4) | Automática · medición |
| [REN-03](defectos/rendimiento.md) | El registro en vivo de Inicio rehacía sus 100 líneas con cada mensaje | S2 | Corregido (0.12.4) | Medición en la app real |
| [REN-04](defectos/rendimiento.md) | Cada regalo empujaba cuatro estados completos a la interfaz | S2 | Corregido (0.12.5) | Automática · medición |
| [REN-05](defectos/rendimiento.md) | Las variables del Chatbot se calculaban todas en cada respuesta | S3 | Corregido (0.12.5) | Medición |
| [REN-06](defectos/rendimiento.md) | Ajustes dibujaba los 2.442 regalos con su imagen | S3 | Corregido (0.12.5) | Medición en la app real |
| [REN-07](defectos/rendimiento.md) | Sonidos leía el audio del disco en cada disparo | S3 | Corregido (0.12.5) | Medición |
| [REN-08](defectos/rendimiento.md) | Un acumulador por espectador ocupaba 73 MB en un live de 200.000 | S2 | Corregido | Automática |
| [REN-09](defectos/rendimiento.md) | Contar espectadores únicos y recurrentes sin que la memoria crezca | S2 | Corregido (por diseño) | Automática |
| [REN-10](defectos/rendimiento.md) | El tope del libro de Puntos no puede borrar ahorros | S2 | Corregido (por diseño) | Automática |
| [REN-11](defectos/rendimiento.md) | El proceso de GPU se queda con 84 MB aunque no haya ventanas | S4 | Abierto (decisión pendiente) | Medición · `docs/memoria-y-bandeja.md` |
| [REN-12](defectos/rendimiento.md) | El ahorro dentro de OBS no se midió | S4 | Abierto | Caso manual CM-06 |
| [SUP-01](defectos/superposiciones-y-multimedia.md) | El Tarro de donaciones se rompía al cargar en una fuente oculta | S3 | Corregido | Navegador |
| [SUP-02](defectos/superposiciones-y-multimedia.md) | La cola de YouTube crecía en silencio sin el visor en OBS | S2 | Corregido | Navegador |
| [SUP-03](defectos/superposiciones-y-multimedia.md) | Si el visor se cuelga, la cola de videos se traba para siempre | S2 | Corregido | Navegador |
| [SUP-04](defectos/superposiciones-y-multimedia.md) | Las alertas con video de Acciones y Eventos no se probaron en OBS | S3 | Abierto | Caso manual CM-05 |
| [SUP-05](defectos/superposiciones-y-multimedia.md) | Las superposiciones le piden sus fuentes a Google desde OBS | S4 | Abierto (decisión pendiente) | Revisión de código |
| [SUP-06](defectos/superposiciones-y-multimedia.md) | Los videos de YouTube dentro de OBS no se probaron | S3 | Abierto | Caso manual CM-07 |
| [SUP-07](defectos/superposiciones-y-multimedia.md) | Falta ver a Spotify pasar las canciones pedidas a su cola | S3 | Abierto | 34 comprobaciones con la API simulada · prueba de campo (2026-09-01) · caso manual CM-11 |
| [SUP-08](defectos/superposiciones-y-multimedia.md) | El chat en vivo se quedaba en los primeros mensajes del live | S2 | Corregido (2026-09-01) · sin reconfirmar con OBS abierto un rato largo | Prueba de campo (2026-09-01) |
| [SUP-09](defectos/superposiciones-y-multimedia.md) | La pantalla de Spotify prometía un botón que no existía | S3 | Corregido (2026-09-01) | Prueba de campo (2026-09-01) |
| [SUP-10](defectos/superposiciones-y-multimedia.md) | Las alertas no cargaban en TikTok LIVE Studio y sí en el navegador | S2 | Corregido · falta confirmar en Live Studio | Prueba de campo (2026-09-02) |
| [VOZ-01](defectos/voz-regalos-y-chatbot.md) | Una síntesis colgada dejaba la voz muda el resto del stream | S1 | Corregido | Automática y mutación (`tests/cadena/en-vivo.test.js`) |
| [VOZ-02](defectos/voz-regalos-y-chatbot.md) | El monedero de la voz no tenía tope y ordenaba a todos en cada comentario | S3 | Corregido (0.12.5) | Automática y mutación · medición |
| [VOZ-03](defectos/voz-regalos-y-chatbot.md) | Los combos multiplicaban el total regalado | S2 | Corregido | Automática y mutación |
| [VOZ-04](defectos/voz-regalos-y-chatbot.md) | Recortar un mensaje largo dejaba medio teléfono sin tapar | S2 | Corregido · en esta auditoría, sin compilar | Automática y mutación |
| [VOZ-05](defectos/voz-regalos-y-chatbot.md) | El idioma del comentario puede venir vacío | S4 | Mitigado · falta medir en vivo | Automática · caso manual CM-03 |
| [REG-01](defectos/voz-regalos-y-chatbot.md) | El tick final de un combo a veces no llega | S1 | Corregido | Automática |
| [REG-02](defectos/voz-regalos-y-chatbot.md) | Un tick final tardío contaba el mismo combo dos veces | S2 | Corregido | Automática y mutación |
| [REG-03](defectos/voz-regalos-y-chatbot.md) | El mismo regalo tiene un id distinto en cada región | S2 | Corregido | Automática |
| [REG-04](defectos/voz-regalos-y-chatbot.md) | Deduplicar por id de mensaje perdía los ticks del combo | S2 | Corregido | Automática y mutación |
| [REG-05](defectos/voz-regalos-y-chatbot.md) | TikTok reenvía el "seguir" de quien vuelve a entrar | S3 | Corregido | Automática |
| [REG-07](defectos/voz-regalos-y-chatbot.md) | Seguir y Compartir no disparaban en Acciones y Eventos ni en Juegos Interactivos | S2 | Corregido (2026-09-01) | Prueba de campo (2026-09-01) · prueba de reproducción al corregirlo |
| [REG-06](defectos/voz-regalos-y-chatbot.md) | Faltan imágenes de regalos (81 %) | S4 | Abierto (por límite externo) | Medición · `docs/imagenes-de-regalos.md` |
| [CHB-01](defectos/voz-regalos-y-chatbot.md) | {espectadores} y {seguidores} daban siempre 0 y "—" | S3 | Corregido (0.12.5) | Automática (`tests/cadena/conexion.test.js`) |
| [CHB-02](defectos/voz-regalos-y-chatbot.md) | Escribir en el chat de TikTok va contra sus términos y necesita un add-on pago | S2 | Por diseño (límite externo) | Caso manual CM-09 |
<!-- indice:hasta -->
