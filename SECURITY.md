# Seguridad

## Cómo reportar una vulnerabilidad

**En privado**, desde la pestaña **Security › Report a vulnerability** de este
repositorio. No abras un issue público: lo ve todo el mundo antes de que haya un
arreglo.

Conviene incluir:

- la versión de TikGO y de Windows;
- qué puede hacer alguien que la aproveche, y desde dónde (la misma PC, la red local,
  un espectador del live, internet);
- los pasos para reproducirla.

TikGO lo hace un solo desarrollador: la respuesta llega lo antes posible, sin un
plazo fijo. Se avisa cuando el arreglo está publicado y, si quieres, se te menciona.

## Versiones con soporte

Solo la última versión publicada.

## Cómo está pensada

- **Todo corre en la PC del streamer.** No hay servidores propios, telemetría ni
  informes de errores.
- **Nada escucha fuera de la propia PC**: las superposiciones y los puentes de los
  mods abren puertos solo en `127.0.0.1`. Hay una prueba automática que lo exige.
- **Las sesiones y las claves van cifradas** con DPAPI (atadas al usuario de
  Windows): la sesión de TikTok, la de Spotify, la de la cuenta del Chatbot y la clave
  de Euler. La clave de la API de YouTube todavía no.
- **Lo que viene del live se trata como hostil**: se inserta como texto, no como
  HTML; dentro de Minecraft y GTA V se limpian los códigos de formato.
- **Todo lo que se baja para los juegos se verifica** contra un SHA-256 antes de
  instalarse.

Qué toca en Windows y a qué servidores se conecta:
[cambios en el sistema](docs/auditoria/cambios-en-el-sistema.md) y
[conexiones de red](docs/auditoria/conexiones-de-red.md).

## Hallazgos abiertos

Están publicados, con su severidad, en el
[registro de defectos](docs/qa/defectos/privacidad-seguridad-y-licencias.md)
(códigos `SEG` y `LIC`).
