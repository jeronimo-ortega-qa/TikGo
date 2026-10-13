# TikGO

Aplicación de escritorio para streamers de TikTok LIVE, para Windows. Convierte lo
que hace la audiencia (comentarios, regalos, likes, seguidores) en voz sintetizada,
sonidos, superposiciones para OBS y efectos sobre el juego del streamer.

**Estado:** beta cerrada · versión 0.15.0

## Qué hay en este repositorio

Este repositorio es **público para que TikGO se pueda auditar**. Tiene la
documentación de calidad, de seguridad y de privacidad. **El código fuente no está
aquí**: es privado.

| | |
|---|---|
| [Plan de pruebas](docs/qa/plan-de-pruebas.md) | Cómo se prueba y cuándo una versión está lista |
| [Registro de defectos](docs/qa/registro-de-defectos.md) | Cada error conocido: corregidos, abiertos, cómo se verificó cada uno |
| [Matriz de verificación](docs/qa/matriz-de-verificacion.md) | Qué función se comprobó dónde: simulado, app real, juego, OBS, live |
| [Casos manuales](docs/qa/casos-manuales.md) | Lo que falta comprobar en vivo |
| [Último informe de pruebas](docs/qa/informe-0.15.0.md) | El resultado de la última corrida |
| [Auditoría](docs/auditoria/README.md) | Conexiones de red, cambios en Windows, dependencias, SBOM y hashes |
| [Privacidad y seguridad](docs/privacidad-y-seguridad.md) | Qué datos de la audiencia se tratan y con qué base legal |

Las referencias a `src/`, `CLAUDE.md`, `.claude/` y a otros documentos de `docs/`
apuntan al repositorio privado.

## Acceso al código para auditar

Quien necesite revisar el código puede pedir acceso de **solo lectura** al
repositorio privado abriendo un issue con la plantilla "Pedir acceso para auditar".
Con ese acceso se pueden repetir todas las pruebas (`npm test`, sin cuentas ni
claves).

## Reportar un error

Con la plantilla "Reporte de error" de la pestaña Issues. No pegues claves, sesiones
ni nombres de tu audiencia.

## Reportar una vulnerabilidad

En privado, desde **Security › Report a vulnerability**. Ver [SECURITY.md](SECURITY.md).

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
