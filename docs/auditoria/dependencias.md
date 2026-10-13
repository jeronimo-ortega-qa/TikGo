# Dependencias y licencias

TikGO tiene **6 dependencias de producción** a propósito. Con lo que ellas traen son
40 paquetes en la app; con las herramientas de desarrollo, 388.

| Archivo | Qué es |
|---|---|
| [`sbom-produccion.cdx.json`](sbom-produccion.cdx.json) | Lo que viaja en el instalador, en CycloneDX 1.5 (`npm sbom --omit dev`) |
| [`sbom-completo.cdx.json`](sbom-completo.cdx.json) | Todo, incluidas las herramientas de compilación |

`npm sbom --omit dev` (npm 12.0.1) deja afuera `dotenv` aunque es de producción;
está en el SBOM completo. Se regeneran con:

```bash
npm sbom --sbom-format cyclonedx --omit dev > docs/auditoria/sbom-produccion.cdx.json
npm sbom --sbom-format cyclonedx > docs/auditoria/sbom-completo.cdx.json
```

## Las de producción

| Paquete | Versión | Licencia | Para qué |
|---|---|---|---|
| `tiktok-live-connector` | 2.4.4 | **AGPL-3.0-only** | La conexión al live (con `tiktok-live-proto` 0.2.4, también AGPL-3.0-only) |
| `edge-tts-universal` | 1.4.0 | **AGPL-3.0** | La voz sintetizada |
| `react` / `react-dom` | 19.2.8 | MIT | La interfaz |
| `ws` | 8.21.3 | MIT | Los websockets de las superposiciones |
| `dotenv` | 16.6.1 | BSD-2-Clause | Leer el `.env` en desarrollo |

El resto de lo que traen es MIT, salvo `@bufbuild/protobuf` (Apache-2.0 y
BSD-3-Clause), `webidl-conversions` (BSD-2-Clause) y `type-fest` (MIT o CC0-1.0).
`xml-escape` 1.1.0 figura sin licencia en el SBOM porque su `package.json` dice
"MIT License" en vez del identificador `MIT`; su archivo `LICENSE` es MIT.

**Las dos AGPL son el hallazgo más importante de esta auditoría** (LIC-01 y LIC-02
en el registro de defectos): la AGPL pide que quien recibe el programa pueda recibir
el código completo bajo la misma licencia. `tiktok-live-connector` fue MIT hasta la
1.2.3 (enero de 2025) y pasó a AGPL en la 2.0.0 (mayo de 2025). No es asesoramiento
legal; lo tiene que revisar un abogado antes de distribuir.

## Lo que viaja en el instalador además del código

| Qué | Licencia | Nota |
|---|---|---|
| Electron 43 (con Chromium y Node) | MIT, y las de Chromium | electron-builder incluye `LICENSE.electron.txt` y `LICENSES.chromium.html` |
| IBM Plex Sans y Mono | SIL OFL 1.1 | La licencia va en `resources\licencias\IBM-Plex-OFL.txt` |
| `mkcert.exe` | BSD-3-Clause | De Filippo Valsorda; compilado con Go 1.18 |
| `gamepad-helper.exe` | Propio | Fuente en `native/gamepad-helper` |
| `TikGoGtav.dll`, `TikGoRepo.dll`, `TikGO.jar` | Propios | Fuente en `native/` |
| Portadas de juegos | **Sin resolver** | LIC-04 |

## Lo que se baja al usarlo, y no viaja

| Qué | Versión | Licencia | De dónde |
|---|---|---|---|
| Script Hook V .NET Enhanced | 1.1.0.6 | zlib | GitHub, verificado por SHA-256 |
| BepInEx (pack de R.E.P.O.) | 5.4.23.5 | LGPL-2.1 | Thunderstore, verificado por SHA-256 |
| Paper | la que elija el streamer | GPL-3.0 | PaperMC, verificado por el hash que publica |
| Java (Temurin JRE) | 21 o 25 | GPLv2 con excepción de clase | Adoptium, verificado por el hash que publica |
| ViGEmBus / HidHide | 1.22.0 / 1.5.230.0 | BSD-3-Clause / MIT | Los baja el streamer de su página oficial |
| Script Hook V | — | No redistribuible | Lo instala el streamer; la app abre su página |
