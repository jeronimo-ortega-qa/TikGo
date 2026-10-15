# Informe de pruebas · 0.15.0

| | |
|---|---|
| **Fecha** | 2026-10-12 |
| **Código** | 0.15.0 más las correcciones de esta auditoría (sin compilar todavía) |
| **Instalador revisado** | `TikGO Setup 0.15.0.exe`, SHA-256 `61636fc8…5099744` |
| **Máquina** | Windows 11, Node 24.15.0, npm 12.0.1 |
| **Alcance** | Primera corrida de la suite de `tests/`, revisión del registro de defectos y auditoría de dependencias, red y sistema |

## Resultado

| | Resultado |
|---|---|
| Pruebas automáticas | **72 de 72** pasan, en unos 6 s |
| · estáticas | 15 (reglas del código y trazabilidad del registro) |
| · unidad | 28 |
| · cadena (live simulado de punta a punta) | 29 |
| Mutaciones | **16 de 16** detectadas: cada error corregido, vuelto a poner, hace fallar su prueba |
| Cobertura de `src/main` | **48 %** de las líneas, 70 % de las ramas y 36 % de las funciones (`npm run test:cobertura`) |
| Compilación de la interfaz | `npx vite build` sin errores |
| Defectos registrados | 136 entradas: 84 corregidas, 28 abiertas, 4 mitigadas, 12 requisitos y 5 controles que se cumplen |

## Encontrado en esta auditoría

**Corregido** (en el código; entra en la próxima compilación):

| | Sev. | Qué |
|---|---|---|
| VOZ-04 | S2 | Con un límite de caracteres corto, a Microsoft le llegaba medio teléfono: se recortaba antes de tapar. |
| PRI-04 | S4 | Un teléfono con paréntesis quedaba como "(un número". |
| INT-03 | S3 | La api de respaldo no tenía `interactiveGamesInstallGamepadDriver`. |
| SEG-05 | S2 | El `.gitignore` no excluía DLLs de terceros ni carpetas compiladas con rutas personales. |

Las cuatro se encontraron con una prueba que fallaba **antes** de corregir.

**Abierto, nuevo:**

| | Sev. | Qué |
|---|---|---|
| LIC-01 | S1 | `tiktok-live-connector` 2.x y `tiktok-live-proto` son AGPL-3.0-only. |
| LIC-02 | S1 | `edge-tts-universal` es AGPL-3.0. |
| SEG-03 | S2 | `src/main` va legible dentro de `app.asar`. |
| SEG-08 | S2 | Las superposiciones para Live Studio dependen de `overlay.tikgo.app`. |
| SEG-07 | S3 | "Instalar certificado" agrega una autoridad raíz y la pantalla no lo dice. |
| SEG-01 | S3 | La clave del certificado de respaldo es la misma en todas las instalaciones. |
| REPO-08 | S3 | R.E.P.O. decide "al día" por el tamaño del DLL. |
| SEG-10 | S4 | Los DLL de los mods llevan la ruta de compilación con el usuario de Windows. |
| LIC-05 | S4 | El ícono de la moneda de TikTok viaja en la app. |

## Bloqueantes para distribuir fuera de la beta

LIC-01, LIC-02 y LIC-03 (licencias y términos de terceros) y SEG-03 (el código
legible en el instalador). Ninguno se resuelve con una prueba: los tres primeros
necesitan reemplazar dependencias o una revisión legal, y el cuarto, cambiar cómo se
empaqueta el proceso principal.

## Lo que esta corrida no cubre

Todo lo de `casos-manuales.md` (CM-01 a CM-16): un live de verdad, OBS, Live Studio y
los juegos abiertos. Ninguno se ejecutó en esta corrida.

## Cómo repetirla

```bash
npm ci
npm test
npm run test:mutaciones
npm run test:cobertura
npx vite build
```
