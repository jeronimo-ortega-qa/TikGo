# Pruebas de campo

La ejecución manual de este proyecto: la app usada en condiciones reales, con lo que
eso trae de imprevisible. Lives de TikTok (algunos controlados, otros con audiencia),
una grabación hecha con TikTok LIVE Studio y tandas de prueba dentro de los juegos.

Cada hallazgo se reportó con lo que se vio en pantalla, se reprodujo antes de
corregirlo y quedó en el [registro de defectos](registro-de-defectos.md) con su código.
Lo que se corrigió después de una sesión y todavía no volvió a pasar por un live dice
"sin reconfirmar" en su estado; para eso están los [casos manuales](casos-manuales.md).

## Sesiones

| Fecha | Dónde | Qué se encontró | Defectos |
|---|---|---|---|
| 2026-08-27 | Grabación con TikTok LIVE Studio y uso con el stream corriendo | "Voltear pantalla" giraba el monitor, pero la grabación del live salía derecha: la audiencia no lo veía. La superposición de Juegos Interactivos se veía borrosa al agrandarla. "Compartir Live" no disparaba. | JUE-03, JUE-21, REG-07 |
| 2026-09-01 | Live controlado | 11 hallazgos. Seguir y Compartir no disparaban en Acciones y Eventos ni en Juegos Interactivos (era un solo error); "Mantener tecla" la apretaba una vez; el chat en vivo se quedaba en los primeros mensajes; la pantalla volteada no se veía; el mouse invertido iba con retraso; Spotify se vinculó y recibió pedidos, pero la pantalla prometía un botón que no existía; y dos disparadores que sobraban. | REG-07, JUE-20, SUP-08, JUE-03, JUE-19, SUP-09, SUP-07 |
| 2026-09-02 | En vivo | Las alertas cargaban en el navegador pero no en TikTok LIVE Studio. | SUP-10 |
| 2026-09-05 | En vivo, con la 0.5.0 | La superposición de Juegos Interactivos se desarmaba con resolución personalizada y no se leía en el stream. La repetición solo aceptaba algunos números; al hacerla libre apareció que un número fuera de rango se guardaba como el valor por defecto. | JUE-21, INT-06 |
| 2026-09-07 | En vivo con Minecraft | Los combos no multiplicaban: 3 rosas daban 3 esqueletos, no 9. Un cartel por cada rosa del combo. "Meta de Likes" no hacía nada. La jaula de TNT no atrapaba en movimiento. Los rayos caían al costado. | JUE-22, MIN-13, JUE-23, MIN-14, MIN-15 |
| 2026-09-10 | En vivo | A los 10-20 minutos de conectada dejaban de funcionar la voz, el chat y los sonidos, todos juntos y sin ningún error. Tenía tres causas. | CON-01, CON-02, CON-03, VOZ-01 |
| 2026-09-12 | En vivo, con regalos de verdad | Super GG, Money Gun y el osito Mishka no caían en el tarro de donaciones: TikTok no siempre manda el último tick del combo. | REG-01 |
| 2026-09-16 | GTA V Enhanced, dentro del juego | Los 49 efectos originales, uno por uno y en tandas, con resultado Sí o No para cada uno. Fallaron, entre otros, el súper salto y un efecto mandado mientras el jugador reaparecía. "Prenderse fuego" resultó no ser un error: un jugador invencible no arde. | GTA-04, GTA-09 |
| 2026-09-20 | En vivo con GTA V | Los enemigos huían y se atacaban entre ellos; el teletransporte al azar caía cerca; un efecto se vencía con el jugador muerto. Y durante un rato ningún arreglo hacía efecto: el botón "Reinstalar el mod" había vuelto a poner la versión vieja. | GTA-19, GTA-20, GTA-21, GTA-17, GTA-18 |

**27 defectos del registro salieron de estas sesiones.** Casi todos eran invisibles
fuera de un live: dependían de la carga real de un stream, de regalos de verdad, de
cómo TikTok LIVE Studio captura la pantalla o de cómo se comporta el juego.

## Cómo se reportaba

Cada hallazgo se mandó con lo que se veía y, cuando se podía, la evidencia: capturas,
la grabación de Live Studio, el registro del mod o la salida de la herramienta de
diagnóstico del control. Antes de corregir se reproducía el fallo (con una prueba, el
juego abierto o la app real) y después se comprobaba que la prueba pasara.

Cuando el streamer estaba en vivo, la prioridad era salir del paso sin cerrar la app:
una dirección HTTP en vez de la HTTPS para Live Studio, un tamaño fijo para la
superposición, o un mod corregido que se recarga con Insert dentro de GTA V. La
corrección de fondo y el instalador nuevo quedaban para después del live.
