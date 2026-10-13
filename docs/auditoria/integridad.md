# Integridad: hashes de lo que se distribuye

El instalador no está firmado (SEG-09). Hasta que lo esté, la forma de comprobar que
un instalador es el que salió de aquí es su SHA-256.

## Instaladores

| Versión | Archivo | Bytes | SHA-256 |
|---|---|---|---|
| 0.15.0 | `TikGO Setup 0.15.0.exe` | 104.369.675 | `61636fc8d261ac356423f747722b88d5fa844e1313468b9b58bce647b5099744` |

Para comprobarlo en Windows:

```powershell
Get-FileHash "TikGO Setup 0.15.0.exe" -Algorithm SHA256
```

## Binarios que viajan dentro del instalador

Al 2026-10-12, en `src/main/shared/bin/`:

| Archivo | Bytes | SHA-256 |
|---|---|---|
| `mkcert.exe` | 4.896.256 | `d2660b50a9ed59eada480750561c96abc2ed4c9a38c6a24d93e30e0977631398` |
| `gamepad-helper.exe` | 33.280 | `f1b65e60baa733e12a089746c7df06ff05668347b2009a5d35830d891da9de85` |
| `gtav/TikGoGtav.dll` | 85.504 | `af0c4cba533740b8ab07fb8ca4a9689e300dafe17899f984904ee6bffcc7ef49` |
| `repo/TikGoRepo.dll` | 27.136 | `e7b5c43ab270e885f4bd0da4d625bc3a7b30917a2ef83245864f5829dac80fbe` |
| `minecraft/TikGO.jar` | 23.244 | `e658b4d57d42bf849433f6024bafd07591e5be77ad3cc1c3dbb6b1dffc26a68b` |

## Lo que la app baja y verifica antes de usar

| Qué | Hash que se exige | Dónde está fijado |
|---|---|---|
| Script Hook V .NET Enhanced 1.1.0.6 | `f10da8819fa6814fb0a04553567d2516934eac96b16af06ad8f315f0da00473a` | `src/main/gtav/index.js` |
| BepInEx 5.4.23.5 | `f99b6302b6bdca5bab26f275c261cca448b29bbac34eaa5cbebb42c4246fb244` | `src/main/repo/index.js` |
| Paper | el SHA-256 que publica PaperMC para esa versión | `src/main/minecraft/server-manager.js` |
| Java | el SHA-256 que publica Adoptium para ese paquete | `src/main/minecraft/java-runtime.js` |

Si el archivo no coincide, no se instala nada y no queda nada a medias (prueba
`MIN-03`). Un cambio de versión cambia la dirección y el hash juntos.

## Al sacar una versión

Agregar su fila a la tabla de instaladores con el resultado de `Get-FileHash` del
archivo que se publica, y actualizar la de binarios si alguno cambió.
