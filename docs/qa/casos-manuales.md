# Casos de prueba manuales

Lo que las pruebas automáticas no pueden comprobar: necesita un live de verdad,
OBS o TikTok LIVE Studio abiertos, o el juego corriendo. Cada caso dice qué
defecto o función cubre, cómo prepararlo, los pasos y qué tiene que pasar.

**Al ejecutar uno**, anotar la fecha, la versión de TikGO y el resultado en la tabla
de "Ejecuciones" del caso. Si falla, abrir un defecto nuevo en el registro con lo
que se vio, no corregir sobre la marcha.

| Estado | Casos |
|---|---|
| Sin ejecutar como caso formal | todos (al 2026-10-15) |

Varias de estas funciones ya pasaron por [pruebas de campo](pruebas-de-campo.md), y de
ahí salieron arreglos. Estos casos son para confirmar en un live esos arreglos y lo que
nunca se vio en vivo.

---

### CM-01 · Un live largo no se queda mudo y se recupera de un corte

- **Cubre.** CON-03, CON-04 y el reporte original ("a los 10-20 minutos deja de
  andar todo").
- **Preparación.** Metas visibles en OBS, el Temporizador corriendo y la voz
  prendida.
- **Pasos.**
  1. Conectar al live propio y dejarlo andando.
  2. Cortar el Wi-Fi 30 segundos y volver a prenderlo.
  3. Mirar Inicio y el registro.
  4. Dejarlo dos horas.
- **Resultado esperado.** Reconecta sola; el registro vuelve a llenarse; Inicio dice
  que la conexión se cortó una vez y volvió sola; las Metas y el Temporizador siguen
  donde estaban; en dos horas no vuelve a quedarse muda.

### CM-02 · La app se conecta sola al empezar el live

- **Cubre.** CON-08.
- **Pasos.** Abrir TikGO con la sesión iniciada y sin tocar "Conectar". Empezar un
  live desde el teléfono.
- **Resultado esperado.** A los pocos segundos queda conectada al live de la cuenta
  de la sesión (no al último escrito a mano), con un globo en la bandeja.

### CM-03 · Cuán seguido viene el idioma del comentario

- **Cubre.** VOZ-05.
- **Pasos.** Con "Acento del idioma del mensaje" prendido, dejar pasar un live con
  comentarios en varios idiomas.
- **Resultado esperado.** Anotar qué parte de los mensajes trae `contentLanguage`
  lleno y si los que no lo traen se leen con una voz razonable.

### CM-04 · Efectos de teclado y mouse contra un juego real

- **Cubre.** JUE-17.
- **Pasos.** Con Fortnite (o el juego del perfil) al frente, disparar desde la app
  cada efecto: simular tecla, mantener, repetir, desactivar tecla, desactivar clic e
  invertir mouse.
- **Resultado esperado.** Anotar cuáles hacen efecto dentro del juego y cuáles no.
  "Restaurar mi máquina" lo devuelve todo.

### CM-05 · Alertas con video de Acciones y Eventos dentro de OBS

- **Cubre.** SUP-04.
- **Pasos.** Una Acción con un video, la superposición en OBS como fuente de
  navegador; dispararla con un evento de prueba y después con uno del live.
- **Resultado esperado.** El video se ve completo y con sonido; también con un
  archivo de más de 2 MB.

### CM-06 · Cuánto ahorran las superposiciones optimizadas dentro de OBS

- **Cubre.** REN-12.
- **Pasos.** Con OBS abierto, medir el uso de CPU y GPU de su proceso con el visor de
  YouTube reproduciendo, el fondo animado de Juegos Interactivos y el Temporizador
  en pausa.
- **Resultado esperado.** Los números, para completar `docs/auditoria-rendimiento.md`.

### CM-07 · Un video pedido por chat se reproduce solo en OBS

- **Cubre.** SUP-06.
- **Pasos.** El visor de YouTube como fuente de navegador; pedir un video desde el
  chat del live.
- **Resultado esperado.** Arranca sin ningún clic y con audio.

### CM-08 · !privacidad y !olvidame en un live de verdad

- **Cubre.** PRI-02, PRI-09.
- **Preparación.** El Chatbot prendido; una cuenta de prueba como espectadora.
- **Pasos.** Desde la cuenta de prueba: comentar, mandar un regalo chico, escribir
  `!privacidad` y después `!olvidame`. Buscarla en Privacidad › Espectadores.
- **Resultado esperado.** El bot responde una vez a cada comando; la búsqueda no
  encuentra nada; lo que comenta después no aparece en ningún lado y sus regalos
  cuentan como Anónimo.

### CM-09 · El Chatbot escribe en el chat de TikTok

- **Cubre.** CHB-02.
- **Preparación.** El add-on "Webcast Premium" de Euler (pago).
- **Pasos.** Prender la salida al chat con la cuenta del streamer y disparar un
  comando.
- **Resultado esperado.** La respuesta aparece en el chat, como mucho seis por minuto.
  Sin el add-on, el error dice que falta el add-on.

### CM-10 · Actualizar con la app abierta la cierra en orden

- **Cubre.** ARR-06.
- **Pasos.** Con la 0.15.x instalada y abierta en la bandeja, abrir el instalador de
  la versión siguiente y avanzar.
- **Resultado esperado.** La app se cierra sola antes de copiar archivos, sin el
  cartel de "cierra TikGO para seguir". La configuración queda igual.

### CM-11 · Spotify contra la API de verdad

- **Cubre.** SUP-07.
- **Pasos.** Crear la app en el panel de Spotify, cargar el Client ID, vincular con una
  cuenta Premium y dejar la cola corriendo un rato con pedidos desde el chat.
- **Resultado esperado.** Cada canción pedida suena en su turno, pasada unos 20 s
  antes del final de la anterior.

### CM-12 · Minecraft con jugadores conectados

- **Cubre.** MIN-12.
- **Pasos.** Servidor local prendido desde el minipanel, un cliente de Minecraft
  conectado, el perfil de Minecraft con varios comandos.
- **Resultado esperado.** El primero que entra recibe OP; cada efecto pasa en el
  juego con el nombre de quien lo mandó.

### CM-13 · R.E.P.O.: cada efecto y un jugador sin el mod

- **Cubre.** REPO-07.
- **Pasos.** Partida propia con el mod; un segundo jugador sin nada instalado entra.
  Disparar cada efecto.
- **Resultado esperado.** Cada efecto hace lo que dice y el segundo jugador ve lo
  mismo, sin errores de su lado.

### CM-14 · GTA V: trampas, trolleos y desastres

- **Cubre.** GTA-16.
- **Pasos.** Modo Historia en Enhanced, disparar los 21 efectos uno por uno con el
  juego al frente.
- **Resultado esperado.** Anotar para cada uno si se ve y si las fuerzas (terremoto,
  agujero negro, tornado) y el cono del aimbot están bien calibrados. Repetir en
  Legacy y en una instalación de Steam o Rockstar.

### CM-15 · Metas de "seguidores totales" en un live

- **Cubre.** Pendiente 6 de `CLAUDE.md`.
- **Pasos.** Una meta de seguidores totales visible en OBS durante un live.
- **Resultado esperado.** Arranca en el número real de seguidores de la cuenta. Si
  arranca en cero, la sala no trae el dato y la barra cuenta solo los nuevos.

### CM-16 · Estadísticas contra el panel de TikTok

- **Cubre.** Pendiente 0 de `CLAUDE.md`.
- **Pasos.** Un live propio con la sesión iniciada; al terminar, comparar las
  Estadísticas de TikGO con las del panel de TikTok del mismo live.
- **Resultado esperado.** Anotar la diferencia en espectadores únicos, pico, likes y
  seguidores nuevos.
