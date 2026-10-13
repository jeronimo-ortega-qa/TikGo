# Defectos · Voz, regalos y Chatbot

### VOZ-01 · Una síntesis colgada dejaba la voz muda el resto del stream

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `tts/edge-tts-client.js` | Automática y mutación (`tests/cadena/en-vivo.test.js`) |

- **Síntoma.** La voz dejaba de leer y no volvía, sin error ni registro.
- **Causa.** La síntesis de Edge TTS resuelve cuando cierra su websocket. Si queda
  a medio morir, la promesa no vuelve nunca, y la cola se marca ocupada antes de
  esperarla.
- **Corrección.** `Promise.race` con un reloj de 15 s; el error llega a Inicio.
- **Prueba.** Un mensaje cuya síntesis no vuelve nunca, seguido de otro: el
  segundo llega a Microsoft igual.

### VOZ-02 · El monedero de la voz no tenía tope y ordenaba a todos en cada comentario

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (0.12.5) | `tts/wallet.js` | Automática y mutación · medición |

- **Causa.** Saldo por espectador sin límite; con "Top gifters" prendido,
  `isTopGifter()` ordenaba a todos los que regalaron con cada comentario.
- **Corrección.** Tope de 5.000 que descarta **al que menos tiene** (es un saldo);
  el ranking se ordena solo cuando entra un regalo. 2.002 µs → 0,2 µs por consulta.
- **Prueba.** 6.000 regaladores: quedan a lo sumo 5.000 y el que más regaló sigue.

### VOZ-03 · Los combos multiplicaban el total regalado

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `tts/tts-manager.js`, `shared/combo-final.js` | Automática y mutación |

- **Síntoma.** Un combo de 20 rosas sumaba 210 monedas en el monedero de la voz.
- **Causa.** Cada tick trae el `repeatCount` acumulado; sumar todos los ticks
  suma 1 + 2 + … + 20.
- **Corrección.** Se ignoran los ticks con `isCombo && !repeatEnd`, y el total es
  `diamondCount × repeatCount`. Hay dos defensas: el finalizador de combos de
  `handlers.js` y la guarda de la propia voz. Las dos tienen prueba.

### VOZ-04 · Recortar un mensaje largo dejaba medio teléfono sin tapar

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido · en esta auditoría, sin compilar | `tts/tts-manager.js` | Automática y mutación |

- **Síntoma.** Con un límite de caracteres corto, a Microsoft le llegaba
  "mi número 31155": la mitad de un teléfono.
- **Causa.** El mensaje se recortaba **antes** de tapar los datos, y un número
  cortado por debajo de 7 dígitos ya no se reconocía como teléfono.
- **Corrección.** Primero se tapa y después se recorta.
- **Encontrado.** 2026-10-12, al escribir la prueba de cadena de la privacidad.

### VOZ-05 · El idioma del comentario puede venir vacío

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Mitigado · falta medir en vivo | `tts/idioma.js` | Automática · caso manual CM-03 |

- **Qué pasa.** `contentLanguage` viene en el mensaje de chat pero a veces vacío.
- **Mitigación.** Capas: lo que dice TikTok, el alfabeto y un puñado de palabras;
  si nada alcanza, sale con la voz elegida. Falta medir en un live cuán seguido
  viene lleno.

### REG-01 · El tick final de un combo a veces no llega

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido | `shared/combo-final.js` y los respaldos por módulo | Automática |

- **Síntoma.** Una Money Gun de 500 monedas quedó en los récords y sin contar en
  Puntos, Metas y el tarro.
- **Causa.** TikTok no siempre manda el tick con `repeatEnd`.
- **Corrección.** Todo combo se cierra solo a los 4 s del último tick, con su total,
  y avisa una sola vez.

### REG-02 · Un tick final tardío contaba el mismo combo dos veces

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `shared/combo-final.js` | Automática y mutación |

- **Causa.** El final que llega después del respaldo parecía un combo nuevo.
- **Corrección.** Se distinguen por el contador: el mismo `repeatCount` es el
  final tardío y se descarta; uno más chico es un combo nuevo.

### REG-03 · El mismo regalo tiene un id distinto en cada región

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `alerts/gift-catalog-store.js` | Automática |

- **Síntoma.** Un disparador guardado contra "Night Star" no enganchaba nunca si el
  live mandaba el id de otra región (hay 12 ids para ese regalo).
- **Corrección.** El catálogo agrupa por nombre y precio (`aliasIds`) y se compara
  con `mismoRegalo(a, b)`, nunca con `String(a) === String(b)`. El grupo hereda la
  imagen de la región que la tenga.

### REG-04 · Deduplicar por id de mensaje perdía los ticks del combo

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `tiktok/connection.js` | Automática y mutación |

- **Corrección.** Para los regalos, la clave es `msgId:repeatCount:repeatEnd`. Un
  comentario repetido se descarta y cada tick de un combo llega.

### REG-05 · TikTok reenvía el "seguir" de quien vuelve a entrar

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido | `shared/event-dedupe.js` | Automática |

- **Corrección.** Registro por módulo y por tipo, sin distinguir mayúsculas, con
  tope de 20.000 usuarios.
- **Observación de la auditoría.** El tope descarta al más viejo **en llegar**, no
  al que hace más que no participa (no es LRU, aunque `CLAUDE.md` lo agrupe con
  los LRU). Para un deduplicado da lo mismo en la práctica; queda anotado.

### REG-06 · Faltan imágenes de regalos (81 %)

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Abierto (por límite externo) | `alerts/gift-catalog-store.js` | Medición · `docs/imagenes-de-regalos.md` |

- **Estado.** 1.311 de 1.609 regalos con imagen. Estuvo en 15 % con el `imageUri`
  del catálogo paginado (espejo de Euler, 404 casi siempre). Lo que falta son
  regalos que ninguna región publica; se completan cuando alguien los envía. Las
  rutas ya descartadas están documentadas para no volver a probarlas.

### CHB-01 · {espectadores} y {seguidores} daban siempre 0 y "—"

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (0.12.5) | `chatbot/variables.js` | Automática (`tests/cadena/conexion.test.js`) |

- **Causa.** `seguro()` convertía el objeto de la sala en el texto
  `"[object Object]"` y la línea siguiente lo descartaba.
- **Prueba.** Con 1.234 espectadores en la sala, "Somos {espectadores}" da
  "Somos 1.234"; sin dato de seguidores no inventa un cero.

### CHB-02 · Escribir en el chat de TikTok va contra sus términos y necesita un add-on pago

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Por diseño (límite externo) | `chatbot/tiktok-sender.js` | Caso manual CM-09 |

- **Qué pasa.** Sin "Webcast Premium" de Euler la respuesta es `401`. La cuenta que
  publica es la de la cookie y queda expuesta. La app lo dice en la pantalla; viene
  apagado de fábrica y la superposición funciona sin eso.
