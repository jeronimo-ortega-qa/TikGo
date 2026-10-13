# Qué toca TikGO en Windows

Todo lo que la app escribe, instala o lanza fuera de su propia carpeta, y qué lo
dispara. Sale de leer el código el 2026-10-12.

## Lo que pasa siempre

| Qué | Dónde | Por qué |
|---|---|---|
| Su configuración y sus datos | `%APPDATA%\TikGO` (un `.json` por sección, las sesiones en `.enc` cifradas con DPAPI, las Estadísticas en su carpeta) | Es la carpeta de datos de la app. "Privacidad › Tus datos" la exporta o la borra. |
| Un ícono en la bandeja | Área de notificación | Cerrar la ventana deja la app andando para que la voz y los efectos sigan en vivo. Se sale desde el ícono. |
| Una revisión al arrancar | `gamepad-helper.exe hidhide-reset` | Si una sesión anterior se cortó con el control oculto por HidHide, lo vuelve a mostrar (JUE-06). Sin HidHide instalado no hace nada. |

La app **no** se agrega al inicio de Windows, **no** escribe en el registro y **no**
instala servicios.

## Lo que pasa solo si el streamer lo pide

| Qué | Dónde | Lo dispara |
|---|---|---|
| **Una autoridad de certificados raíz** (mkcert) | El almacén de certificados de confianza del usuario y `%LOCALAPPDATA%\mkcert` | El botón "Instalar certificado" de Acciones y Eventos. Hace falta para TikTok LIVE Studio, que rechaza los autofirmados. Su clave puede firmar certificados que esa PC va a creer (SEG-07). Se quita con `mkcert -uninstall`. |
| El certificado de las superposiciones | `%APPDATA%\TikGO\overlay-cert` | El mismo botón. |
| Una línea en el archivo `hosts` | `C:\Windows\System32\drivers\etc\hosts` | **La escribe el streamer a mano.** La app solo abre la carpeta y comprueba que `overlay.tikgo.app` apunte a 127.0.0.1 (SEG-08). |
| Atajos de teclado globales | Windows | Los que el streamer asigna a sus Sonidos. |
| Ganchos de teclado y mouse de bajo nivel | Procesos hijo de PowerShell | Un efecto de Juegos Interactivos que bloquea, remapea o invierte. Viven en procesos aparte a propósito: si el proceso muere, Windows libera el teclado y el mouse. Tienen plazo propio y la app además los mata. |
| Teclas y clics simulados | `keybd_event` / `mouse_event` | Un efecto que simula teclas o clics. "Restaurar mi máquina" suelta todo. |
| Girar el monitor | `ChangeDisplaySettingsEx`, probado antes con `CDS_TEST` | Solo si no hay ventana que espejar para "Voltear pantalla". |
| El control virtual y ocultar el físico | Drivers ViGEmBus y HidHide | El modo control avanzado, apagado por defecto. **Los drivers no vienen con la app**: se bajan de sus páginas oficiales. El modo se apaga solo al cerrar. |
| El servidor de Minecraft y su Java | `%APPDATA%\TikGO\minecraft-server` y `\java` | Crear el servidor desde el minipanel. El Java es propio: no toca el `PATH` ni el Java del sistema. El EULA de Mojang lo acepta el streamer, no la app. |
| Script Hook V .NET y el mod de GTA V | La carpeta del juego: `ScriptHookVDotNet.asi`, `ScriptHookVDotNet2.dll`, `ScriptHookVDotNet3.dll`, `MinHook.x64.dll`, `ScriptHookVDotNet.ini` (solo si no existe) y `scripts\TikGoGtav.dll` + `.ini` | Los botones de instalar del minipanel de GTA V. Script Hook V (de Alexander Blade) lo instala el streamer: no se puede redistribuir. |
| `-nobattleye` | `commandline.txt` en la carpeta de GTA V | "Desactivar BattlEye". Deja al streamer fuera de GTA Online hasta que lo vuelva a activar. |
| BepInEx y el mod de R.E.P.O. | La carpeta del juego: el pack de BepInEx y `BepInEx\plugins\TikGoRepo.dll` | Los botones de instalar del minipanel de R.E.P.O. |

## Lo que lee y no escribe

- Los manifiestos de Epic (`C:\ProgramData\Epic\EpicGamesLauncher\Data\Manifests`) y
  las bibliotecas de Steam, para encontrar los juegos.
- `Documentos\Rockstar Games\GTAV Enhanced\settings.xml`, para saber si el juego se
  pausa al perder el foco.
- Las carpetas conocidas de Java (`Program Files\Java`, Adoptium, Microsoft,
  Corretto, Zulu, `JAVA_HOME`), para usar uno que ya esté instalado.

## Cómo comprobarlo

En las pruebas automáticas la app no puede lanzar ningún proceso: el entorno los
bloquea y los anota, y la prueba `JUE-06` comprueba que la única ejecución al cargar
sea la limpieza del control. Para la app real alcanza con Process Monitor
(Sysinternals) filtrando por `TikGO.exe` y sus procesos hijo.
