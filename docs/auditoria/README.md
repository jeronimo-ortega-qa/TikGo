# Auditoría

Lo necesario para revisar TikGO sin tener que creerle a nadie: qué hace con la red,
qué toca en Windows, de qué depende, cómo se comprueba que un instalador es el
verdadero y cómo se prueba.

| Documento | Qué responde |
|---|---|
| [`conexiones-de-red.md`](conexiones-de-red.md) | A qué servidores se conecta, cuándo y con qué datos; qué puertos abre |
| [`cambios-en-el-sistema.md`](cambios-en-el-sistema.md) | Qué escribe, instala o lanza fuera de su carpeta, y qué lo dispara |
| [`dependencias.md`](dependencias.md) | Las bibliotecas, sus licencias y lo que viaja en el instalador |
| [`integridad.md`](integridad.md) | El SHA-256 de cada instalador y de cada binario incluido |
| [`../privacidad-y-seguridad.md`](../privacidad-y-seguridad.md) | Qué datos de la audiencia se tratan, con qué base legal y cómo se ejercen los derechos |
| [`../qa/plan-de-pruebas.md`](../qa/plan-de-pruebas.md) | Cómo se prueba, el registro de defectos y la matriz de verificación |

## Hallazgos abiertos de esta auditoría (2026-10-12)

Por orden de gravedad. El detalle está en
[`../qa/defectos/privacidad-seguridad-y-licencias.md`](../qa/defectos/privacidad-seguridad-y-licencias.md).

| | Hallazgo |
|---|---|
| **LIC-01** | `tiktok-live-connector` 2.x es AGPL-3.0 |
| **LIC-02** | `edge-tts-universal` es AGPL-3.0 |
| **LIC-03** | Edge TTS va contra los términos de Microsoft para uso comercial |
| **SEG-03** | El código del proceso principal va legible dentro del instalador |
| **SEG-08** | Las superposiciones para Live Studio dependen del dominio `overlay.tikgo.app` |
| **SEG-07** | "Instalar certificado" agrega una autoridad raíz y la pantalla no lo dice |
| **SEG-01** | La clave del certificado de respaldo es la misma en todas las instalaciones |
| **SEG-09** | El instalador no está firmado |

## Acceso al código

El código es privado. Quien necesite auditarlo puede pedir acceso al
repositorio privado (ver el README del repositorio público). Con el
código, todo lo de esta carpeta se puede comprobar con:

```bash
npm ci
npm test
npm run test:mutaciones
```
