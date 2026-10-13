# Defectos · Juegos con mod: Minecraft, R.E.P.O. y GTA V

Los tres siguen el mismo molde: un módulo que encuentra el juego, instala lo que
hace falta y pone un puente TCP local. Lo que pasa dentro del juego solo se ve con
el juego abierto; las reglas y las mediciones están en `.claude/rules/minecraft.md`,
`repo.md` y `gtav.md`.

## Minecraft

### MIN-01 · La API v2 de PaperMC está dada de baja

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `minecraft/server-manager.js` | Contra el servicio real |

- **Síntoma.** `api.papermc.io/v2` responde `{"ok":false,"error":"sunset"}` a todo.
  La que funciona es `fill.papermc.io/v3`, con otra forma de respuesta.

### MIN-02 · Adoptium redirige a GitHub y la descarga guardaba un HTML vacío

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `shared/bajar.js` | Automática (servidor local) |

### MIN-03 · Un archivo a medias o distinto podía pasar por bueno

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `shared/bajar.js` | Automática y mutación |

- **Corrección.** Todo lo que se baja (Paper, Java, BepInEx, Script Hook V .NET) se
  compara contra su SHA-256 antes de usarse, y si falla no queda nada a medias.

### MIN-04 · Una descarga colgada dejaba la barra de avance trabada

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `shared/bajar.js` | Automática |

### MIN-05 · Java 8 se leía como "Java 1" y `java -version` escribe en stderr

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `minecraft/java-runtime.js` | Automática y mutación |

### MIN-06 · Un jar viejo en el servidor hacía cosas viejas sin avisar

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `minecraft/server-manager.js`, `home/index.js` | Contra un Paper real |

- **Causa.** Bukkit carga el plugin al arrancar, y se comparaba por tamaño: dos
  versiones distintas pesaban lo mismo. Ahora se refresca al arrancar comparando
  por sha1, e Inicio avisa si el servidor prendido tiene uno viejo.

### MIN-07 · El plugin quedaba afuera para siempre desde el segundo arranque

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `minecraft/` | Contra un Paper real |

- **Causa.** Un token nuevo en cada arranque; el plugin tenía el viejo y recibía
  `401`. El token se guarda y el `config.yml` del plugin se reescribe antes de
  lanzar Paper.

### MIN-08 · "Día" y "Noche" no hacían nada

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `native/tikgo-plugin` | Los 56 comandos contra un Paper real |

- **Causa.** `setTime` tira en el Nether y el End, y la excepción abortaba el bucle
  antes del mundo normal. Se filtra por `Environment.NORMAL`.

### MIN-09 · La TNT puesta no explotaba

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `native/tikgo-plugin` | Contra un Paper real |

- **Causa.** Un bloque de TNT no se enciende solo; hay que cambiarlo por `TNTPrimed`.

### MIN-10 · El servidor se cerraba con código 1 por el Java equivocado

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `minecraft/` | Contra un Paper real |

- **Causa.** 1.21.x pide Java 21 y 26.x pide Java 25; los servidores viejos tenían el
  mínimo por defecto. Se confirma contra Paper antes de cada arranque y se lee el
  error del propio servidor.

### MIN-11 · Un espectador podía pintar su mensaje o imitar avisos del servidor

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `native/tikgo-plugin` | Contra un Paper real |

- **Corrección.** Se le saca el `§` al texto que viene del live.

### MIN-12 · Falta probarlo con jugadores de verdad

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `minecraft/`, `native/tikgo-plugin` | Caso manual CM-12 |

- **Qué falta.** Casi todos los efectos recorren los jugadores conectados, y el OP
  al primero que entra necesita un cliente. Y el plugin se compiló contra Paper
  1.21.4 con Java 21: hay que confirmar que siga cargando en 26.2 al recompilar.

## R.E.P.O.

### REPO-01 · `Update` no se llamaba nunca y no había ningún error

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido (2026-09-04) | `native/tikgo-repo-mod` | Dentro del juego (registro del mod) |

- **Causa.** El GameObject del plugin no sobrevive a la primera carga de nivel, aun
  con `DontDestroyOnLoad`, y un MonoBehaviour destruido "es null".
- **Corrección.** El estado vive en una clase estática y un `Bombeador` sin
  referencias se rehace en cada escena.

### REPO-02 · El puente quedaba mudo después de un cambio de escena

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `native/tikgo-repo-mod`, `shared/puente-mod.js` | Dentro del juego |

- **Corrección.** Cerrar el puente en `OnApplicationQuit` y no en `OnDestroy`,
  latido cada 10 s y `ReceiveTimeout` de 30 s del lado del mod.

### REPO-03 · El catálogo de enemigos quedaba vacío para siempre

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `repo/`, `native/tikgo-repo-mod` | Dentro del juego |

- **Causa.** En el menú principal todavía no hay enemigos cargados. El mod reintenta
  cada 5 s hasta conseguirlos y la app nunca deja que una lista vacía pise una buena.

### REPO-04 · Los objetos y enemigos no aparecían o aparecían dentro de las paredes

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `native/tikgo-repo-mod` | Dentro del juego |

- **Causa.** Rutas y posiciones armadas a mano. Se copió el comando `/spawn` del
  propio juego: `PrefabRef.ResourcePath` y un `LevelPoint` válido.

### REPO-05 · Un puerto ocupado se veía como "el mod no está conectado"

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `shared/puente-mod.js` | Prueba con un cliente TCP falso |

### REPO-06 · Empujar al jugador no hacía nada

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `native/tikgo-repo-mod` | Dentro del juego |

- **Causa.** Sin voltearlo primero, su cuerpo es cinemático y la fuerza no se aplica.

### REPO-07 · Falta probar cada efecto y que entre un jugador sin el mod

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Abierto | `native/tikgo-repo-mod` | Caso manual CM-13 |

- **Qué falta.** La premisa del diseño es que quien entra a la partida del streamer
  no necesita nada; eso no se comprobó con otro jugador.

## GTA V

### GTA-01 · El mod escribía una línea de registro cada 7 s sin tope

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (2026-09-16) | `native/tikgo-gtav-mod` | Prueba en C# (fuera de `tests/`) |

### GTA-02 · Dos compilaciones distintas del mod pesan lo mismo

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `gtav/index.js` | Dentro del juego |

- **Corrección.** "Al día" compara el SHA-256, no el tamaño. R.E.P.O. todavía compara
  tamaños (ver REPO-08).

### GTA-03 · El mod no encontraba su configuración y no dejaba rastro

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `native/tikgo-gtav-mod` | Dentro del juego |

- **Causa.** SHVDN carga los scripts desde memoria y `Assembly.Location` viene vacío.
  La carpeta sale de `Script.BaseDirectory`.

### GTA-04 · Los efectos de cada cuadro corrían en uno de cada cuatro

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `native/tikgo-gtav-mod` | Dentro del juego, con sonda |

- **Causa.** `Interval` de 50 ms. Con 0 corren en el 100 % de los cuadros.

### GTA-05 · "Apagar motor" duraba hasta que el jugador aceleraba

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `native/tikgo-gtav-mod` | Dentro del juego, con sonda |

### GTA-06 · Los autos quedaban sin agarre después del efecto

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `native/tikgo-gtav-mod` | Dentro del juego |

### GTA-07 · El `null` de JSON llegaba como el texto "null"

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `native/tikgo-gtav-mod` (`Mensaje.cs`) | Prueba en C# (fuera de `tests/`) |

### GTA-08 · Con el juego en pausa los efectos se juntan y salen de golpe

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Mitigado (límite del juego) | `gtav/` | Dentro del juego |

- **Mitigación.** La app lee `PauseOnFocusLoss` de la configuración del juego y lo
  avisa; si un efecto no se confirma en 8 s, lo dice.

### GTA-09 · Un efecto mandado con el jugador muerto se perdía y respondía "ok"

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `native/tikgo-gtav-mod` | Dentro del juego |

- **Corrección.** Espera hasta 20 s y se aplica al reaparecer; si no llega, falla con
  el motivo.

### GTA-10 · "Meterte en el vehículo" no encontró el auto que estaba al lado

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Mitigado · causa sin confirmar | `native/tikgo-gtav-mod` | Dentro del juego |

### GTA-11 · Un enemigo kamikaze desapareció el mismo segundo en que salió

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `native/tikgo-gtav-mod` (`Estilos.cs`) | Dentro del juego |

- **Causa.** Un ped soltado al juego se puede borrar cuando le sobra gente. Los que se
  vigilan se sueltan al morir.

### GTA-12 · "En llamas" no prendía fuego

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Corregido · falta confirmar | `native/tikgo-gtav-mod` | Dentro del juego, parcial |

- **Causa.** Una entidad a prueba de fuego no arde. Ahora son partículas; en la única
  prueba los enemigos murieron al aparecer.

### GTA-13 · La lista de efectos de la app y la del mod pueden separarse

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Control · cumple | `gtav/efectos.js`, `Efectos.cs` | Automática (estática) |

- **Riesgo.** Un efecto que sale de la app y el mod no conoce no hace nada y no da
  ningún error.

### GTA-14 · El reloj de los efectos corría con el juego en pausa

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `native/tikgo-gtav-mod` (`Temporizados.cs`) | Prueba en C# (fuera de `tests/`) |

### GTA-15 · El minipanel decía "Listo" con un mod que no se podía conectar

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `gtav/index.js` | Dentro del juego |

- **Causa.** Se miraba el DLL y no el `.ini`: con el token o el puerto de otra copia,
  el mod no conecta nunca. Ahora devuelve `motivo: 'conexion'` y ofrece reinstalar.

### GTA-16 · 21 efectos nunca se ejecutaron dentro del juego

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Abierto | `Trampas.cs`, `Trolleos.cs`, `Desastres.cs` | Caso manual CM-14 |

- **Qué falta.** Compilan y pasan las pruebas, pero las fuerzas del terremoto, el
  agujero negro, el tornado y el cono del aimbot son valores a ojo. También falta
  Legacy y las instalaciones de Steam y Rockstar.

### REPO-08 · R.E.P.O. todavía decide "al día" por el tamaño del DLL

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Abierto | `repo/index.js` | Revisión de código |

- **Riesgo.** El mismo de GTA-02: una versión vieja con el mismo tamaño pasa por
  actual. La corrección es la misma, comparar el SHA-256.
