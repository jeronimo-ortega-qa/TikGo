# Defectos · Rendimiento y memoria

Medido con la configuración real copiada, nunca vacía: con la configuración vacía
los módulos salen en la primera línea y no se mide nada. Números completos de
antes y después en `docs/auditoria-rendimiento.md`.

### REN-01 · Cada regalo reescribía el catálogo de regalos entero

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S1 | Corregido (0.12.4) | `alerts/gift-catalog-store.js` | Automática y mutación · medición |

- **Síntoma.** 12-14 ms con el proceso principal bloqueado por regalo; 6.000
  regalos escribían 12 GB a disco. Mientras tanto no avanzaba ni la voz ni nada.
- **Corrección.** Guardar solo si el regalo es nuevo o cambió, con guardado
  diferido de 5 s y sin sangría. 12.941 µs → 2 µs; 5.700 escrituras → 1.
- **Prueba.** 6.000 veces el mismo regalo conocido: ninguna escritura.

### REN-02 · El catálogo combinado se rearmaba y ordenaba en cada consulta

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido (0.12.4) | `alerts/gift-catalog-store.js` | Automática · medición |

- **Corrección.** Se arma una vez y se invalida al cambiar; se busca por id.
  Un seguidor que dispara una Acción: 3,7 ms → 0,8 ms.

### REN-03 · El registro en vivo de Inicio rehacía sus 100 líneas con cada mensaje

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido (0.12.4) | `renderer` (Inicio) | Medición en la app real |

- **Causa.** `key={i}` sobre una lista que corre, sin memoizar, un `setState` por
  mensaje y cada tick de combo como una línea.
- **Corrección.** Clave estable, líneas memoizadas, dibujo cada ~250 ms y un regalo
  por combo. 10 regalos/s: 27,6 % → 5,2 % de un núcleo.

### REN-04 · Cada regalo empujaba cuatro estados completos a la interfaz

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido (0.12.5) | `shared/agrupar.js` y cinco módulos | Automática · medición |

- **Corrección.** El primer aviso sale en el acto y el resto, en uno cada 250 ms.
  300 eventos seguidos: 300 estados por módulo → 2-3.

### REN-05 · Las variables del Chatbot se calculaban todas en cada respuesta

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (0.12.5) | `chatbot/variables.js` | Medición |

- **Corrección.** Cada variable se lee solo si el texto la usa. De paso apareció
  CHB-01.

### REN-06 · Ajustes dibujaba los 2.442 regalos con su imagen

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (0.12.5) | `renderer` (Ajustes) | Medición en la app real |

- **Corrección.** Buscador, a lo sumo 150 a la vista e imágenes diferidas.
  9.882 nodos y 146 MB → 717 nodos y 52 MB.

### REN-07 · Sonidos leía el audio del disco en cada disparo

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S3 | Corregido (0.12.5) | `alerts/` | Medición |

- **Corrección.** El audio leído queda en memoria por archivo (40 audios, 5 MB cada
  uno como máximo) y se relee solo si cambió. 50 disparos: 50 lecturas → 1.

### REN-08 · Un acumulador por espectador ocupaba 73 MB en un live de 200.000

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido | `shared/likes-umbral.js` | Automática |

- **Causa.** Un `Set` con `${evento}:${usuario}` para recordar qué umbral disparó
  cada uno.
- **Corrección.** El cruce sale de comparar el total anterior con el nuevo, que no
  guarda nada, y "cada 50 likes" sale de los múltiplos cruzados. Un toque enorme
  topa en tres disparos.

### REN-09 · Contar espectadores únicos y recurrentes sin que la memoria crezca

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido (por diseño) | `estadisticas/unicos.js`, `estadisticas/vistos.js` | Automática |

- **Diseño.** HyperLogLog de 4 KB fijos (±1,6 %) para contar y filtro de Bloom de
  1 MB fijo (1 % de falsos "ya vino" hasta 800.000) para saber quién vuelve.
- **Prueba.** 200.000 espectadores: error menor al 3 % con 4.096 bytes; 100.000 en
  el filtro: ningún falso "nunca vino" y menos del 1 % de falsos "ya vino".

### REN-10 · El tope del libro de Puntos no puede borrar ahorros

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S2 | Corregido (por diseño) | `catalog/points-ledger.js` | Automática |

- **Riesgo.** Un tope LRU le borraría los puntos a quien juntó mucho y volvió al
  mes siguiente.
- **Diseño.** Descarta al que menos tiene y, entre iguales, al más viejo.

### REN-11 · El proceso de GPU se queda con 84 MB aunque no haya ventanas

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Abierto (decisión pendiente) | Electron | Medición · `docs/memoria-y-bandeja.md` |

- **Qué pasa.** Chromium mantiene el proceso de GPU toda la vida de la app. Sin
  aceleración baja a 19 MB, pero un jumpscare con video 1080p pasa de 22 % a 109 %
  de un núcleo. Queda prendida; ofrecer apagarla es una decisión abierta.

### REN-12 · El ahorro dentro de OBS no se midió

| Severidad | Estado | Módulo | Verificación |
|---|---|---|---|
| S4 | Abierto | superposiciones | Caso manual CM-06 |

- **Qué falta.** El visor de YouTube, el fondo animado y el Temporizador piden
  menos trabajo (verificado en el navegador), pero el número hay que sacarlo con
  OBS abierto.
