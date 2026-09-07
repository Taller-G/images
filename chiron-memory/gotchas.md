# gotcha

A non-obvious pitfall or trap, learned the hard way.

## El ~/.npmrc del usuario tiene un `prefix` configurado que hace que `nvm use` devuelva err…

What: El ~/.npmrc del usuario tiene un `prefix` configurado que hace que `nvm use` devuelva error aunque el cambio de versión de Node sí se aplique correctamente · Why: — · Learned: no tratar el error de `nvm use` como fallo real sin verificar `node --version` después <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-13 -->

## En el monorepo con npm workspaces hace falta que quede una única copia resuelta de @colys…

What: En el monorepo con npm workspaces hace falta que quede una única copia resuelta de @colyseus/schema (5.0.27) compartida por apps/server, apps/client y packages/shared · Why: si el bundler resuelve dos instancias del paquete, los tipos de schema se registran por separado y la sincronización de estado entre cliente y servidor deja de funcionar · Learned: tras instalar dependencias, verificar con algo como `npm ls @colyseus/schema` que solo aparece una versión resuelta antes de dar por buena la fase de dependencias <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-14 -->

## Colyseus 0.18.x y Vitest 5 exigen Node >= 22; el proyecto fija Node 22.23.2 vía .nvmrc y…

What: Colyseus 0.18.x y Vitest 5 exigen Node >= 22; el proyecto fija Node 22.23.2 vía .nvmrc y engines · Why: Colyseus 0.17 sobre Node 20 hubiera evitado el upgrade pero ya no sería la versión actual del stack; se optó por instalar Node 22 con nvm en vez de bajar de versión de Colyseus · Learned: antes de armar el plan de stack, chequear los engines de las libs clave (Colyseus, Vitest) contra el Node disponible localmente <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-2 -->

## El mapa original exportado de Tiled/SkyOffice repite la propiedad `collides: false` en mi…

What: El mapa original exportado de Tiled/SkyOffice repite la propiedad `collides: false` en miles de tiles individuales, inflando el JSON a ~822KB; quitar esas propiedades redundantes (dejar solo `collides: true` explícito) lo bajó a ~130KB sin cambiar el comportamiento · Why: impacta directamente el requisito de carga rápida del mapa · Where: apps/client/public/assets/map/oficina-taller.json <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-4 -->

## Sin tener Tiled instalado, se puede validar visualmente un Tiled JSON exportado escribien…

What: Sin tener Tiled instalado, se puede validar visualmente un Tiled JSON exportado escribiendo un script Python que lea los tilesets embebidos en base64 y dibuje capas de tiles + objetos (respetando flip flags) a un PNG con Pillow · Why: permitió detectar y corregir tiles equivocados (ej. heladera de la cocina con ids erróneos de la hoja Generic) antes de cargar el mapa en el juego, sin depender de la app Tiled · Where: scripts ad-hoc de render usados durante la Fase 2 (no versionados) <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-11 -->

## El Dockerfile del servidor debe copiar explícitamente el archivo del mapa (apps/client/pu…

What: El Dockerfile del servidor debe copiar explícitamente el archivo del mapa (apps/client/public/assets/map/) a la imagen, ya que el servidor lee el mismo JSON que usa el cliente vía la variable MAP_FILE en vez de tener su propia copia · Why: sin ese copy explícito, el build de producción del servidor arrancaría sin poder encontrar el mapa y fallaría al crear la sala · Where: apps/server/Dockerfile <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-15 -->

## Al probar el juego con Phaser en dos pestañas de Chrome, la pestaña oculta (document.hidd…

What: Al probar el juego con Phaser en dos pestañas de Chrome, la pestaña oculta (document.hidden) pausa el loop del juego (0 fps) y los KeyboardEvent inyectados por JS no generan movimiento aunque el estado del DOM parezca normal; hay que mantener la pestaña activa/enfocada (o usar teclas reales del sistema) para validar movimiento y colisiones · Why: sin saberlo, se puede concluir erróneamente que el movimiento o las colisiones no funcionan · Where: apps/client/src/game/OfficeScene.ts (prueba manual con claude-in-chrome) <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-6 -->

## Para que escribir en un input/textarea no mueva al avatar con Phaser, no basta con ignora…

What: Para que escribir en un input/textarea no mueva al avatar con Phaser, no basta con ignorar el evento; hay que poner keyboard.enabled=false, llamar disableGlobalCapture() y resetKeys() del KeyboardPlugin al enfocar el campo, y restaurarlo todo al perder el foco · Why: Phaser sigue capturando y reteniendo el estado de teclas (incluso 'sticky') globalmente aunque el foco del DOM esté en un input, generando movimiento fantasma o teclas trabadas · Where: apps/client/src/game/OfficeScene.ts (guardia de teclado ligada a foco de inputs del panel/entrada). <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-3 -->

## El helper de test `moveTo` de las pruebas de integración de sala podía resolver antes de…

What: El helper de test `moveTo` de las pruebas de integración de sala podía resolver antes de que Colyseus terminara de replicar el nuevo estado, específicamente cuando la coordenada `x` del jugador no cambiaba entre dos movimientos (el helper esperaba a que cambiara `x`, no a que llegara el snapshot). · Why: — · Where: apps/server/test/bubbles.room.test.ts. · Learned: las esperas en tests de sala real deben ser deterministas sobre un cambio que sí ocurre (o sobre el propio evento de sincronización), no sobre un campo que puede quedar igual entre dos posiciones distintas. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-10 -->

## Los tests de integración que levantan una sala Colyseus real (con @colyseus/testing) choc…

What: Los tests de integración que levantan una sala Colyseus real (con @colyseus/testing) chocan por el puerto (EADDRINUSE :::2568) si Vitest los corre en paralelo entre archivos distintos. · Why: cada archivo de test abre su propio servidor WebSocket en el mismo puerto. · Where: apps/server/vitest.config.ts. · Learned: desactivar el paralelismo entre archivos (fileParallelism/singleThread) para la suite de tests de sala. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-6 -->

## Al destruirse una burbuja (queda un solo miembro), los jugadores libres que estaban pegad…

What: Al destruirse una burbuja (queda un solo miembro), los jugadores libres que estaban pegados a ella no se agrupaban en una nueva burbuja hasta que alguien volvía a moverse. · Why: la recomputación de burbujas solo corría al mover al jugador que disparaba el evento, no a los vecinos afectados por la destrucción. · Where: apps/server/src/bubbles.ts. · Learned: al destruir una burbuja hay que reevaluar de inmediato a los jugadores libres cercanos al centro, igual que cuando una burbuja deja de estar llena debe poder absorber a quien esperaba al lado. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-7 -->

## `room.onStateChange` en @colyseus/sdk 0.18 no es un EventEmitter con `.on()`; es un objet…

What: `room.onStateChange` en @colyseus/sdk 0.18 no es un EventEmitter con `.on()`; es un objeto `{ once, remove }` que envuelve el callback (ver Room.d.ts / core/signal.d.ts), así que suscribirse como si fuera callable falla en TS con 'This expression is not callable'. · Why: — · Where: apps/client/src/ui/bubble.ts. · Learned: para reaccionar a cambios de estado en el panel de burbuja hay que usar `room.onStateChange(cb)` como llamada directa (o `.once`/`.remove` para desuscribir), no el patrón de EventEmitter genérico. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-9 -->

## Las work orders de este proyecto mencionan 'workadventure' en el contexto pero el motor r…

What: Las work orders de este proyecto mencionan 'workadventure' en el contexto pero el motor real de la oficina es 'virtual-taller-office' (Phaser 3 + Colyseus, derivado de SkyOffice). · Why: La ontología del proyecto indexó el repo workadventure además del real; el código relevante que provee Chiron apunta a rutas de SkyOffice que no coinciden con la estructura del monorepo. · Where: virtual-taller-office/ es el único repo que se modifica en runtime. · Learned: Antes de editar cualquier archivo de cliente, verificar la estructura real con ls/grep; no confiar en las rutas de contexto. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-0 -->

## BootScene originalmente llamaba this.fail() ante CUALQUIER error de carga (incluyendo un…

What: BootScene originalmente llamaba this.fail() ante CUALQUIER error de carga (incluyendo un PNG de avatar ausente), dejando la pantalla en blanco. · Why: Se cambió para que map/tilesets sigan siendo fatales (sin mapa no hay oficina) pero que avatar sheets y logo sean no-fatales (warn + continuar con avatar fallback). · Where: apps/client/src/game/BootScene.ts (manejador FILE_LOAD_ERROR). · Learned: Si se añade un asset nuevo y la oficina queda en blanco, verificar si el FILE_LOAD_ERROR lo marca como fatal. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-3 -->

## createAvatarAnims debe omitir avatares cuya textura no cargó; si crea animaciones sobre u…

What: createAvatarAnims debe omitir avatares cuya textura no cargó; si crea animaciones sobre una textura ausente, el sprite queda invisible/roto sin error explícito. · Why: Se añadió un check scene.textures.exists(textureKey(id)) antes de registrar las animaciones, y resolveLoadedAvatar en Avatar.ts resuelve el id pedido al DEFAULT_AVATAR cuando la textura falta. · Where: apps/client/src/game/avatarAnims.ts; apps/client/src/game/Avatar.ts. · Learned: El fallback de avatar requiere consistencia en tres capas: carga (BootScene), registro de anims (avatarAnims) y resolución de id (Avatar). <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-4 -->

## Los assets de avatar derivan de sprites LimeZu, que prohíben redistribuir los archivos a…

What: Los assets de avatar derivan de sprites LimeZu, que prohíben redistribuir los archivos a terceros. · Why: Mientras el repo virtual-taller-office contenga esos PNG (avatares base y los derivados), debe permanecer privado. Ya documentado en docs/licencias-assets.md y en la memoria del proyecto. · Learned: Antes de hacer el repo público, sustituir o relicenciar todos los LimeZu-derived PNG. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-9 -->

## Mientras el campo de texto del chat tiene foco, `typingGuard` (apps/client/src/game/typin…

What: Mientras el campo de texto del chat tiene foco, `typingGuard` (apps/client/src/game/typingGuard.ts) apaga el teclado de Phaser a propósito para que las flechas muevan el cursor de texto y no el avatar · Why: sin esto, escribir en el chat movería al personaje; el usuario debe soltar el foco (Esc, o tras enviar) para volver a moverse · Where: apps/client/src/game/typingGuard.ts, apps/client/src/ui/chat.ts <!-- id: d7c54432-554e-41a6-b074-d57ca352d8d9-4 -->

## Al implementar que Enter envíe y suelte el foco, el mismo evento `keydown` de Enter en el…

What: Al implementar que Enter envíe y suelte el foco, el mismo evento `keydown` de Enter en el input burbujea hasta el listener global del documento (el que abre el chat con Enter) y volvería a enfocar el campo al instante; hay que marcar el evento como atendido (p.ej. `preventDefault`) en el handler del input para que el listener superior lo ignore · Why: sin este control el envío nunca soltaba realmente el foco, quedando igual que antes pese al cambio · Where: apps/client/src/ui/chat.ts <!-- id: d7c54432-554e-41a6-b074-d57ca352d8d9-6 -->

## El test `apps/server/test/OficinaTallerRoom.test.ts` ("reconectar dentro de la gracia") f…

What: El test `apps/server/test/OficinaTallerRoom.test.ts` ("reconectar dentro de la gracia") falla con `WebSocket is not defined` de forma ambiental en máquinas con Node 20, no por el chat: el repo requiere Node ≥22 y falta el global `WebSocket`; se confirmó reproduciendo la falla en un stash limpio sin los cambios de chat · Why: evita atribuir erróneamente este fallo pre-existente al trabajo de chat en futuras verificaciones · Where: apps/server/test/OficinaTallerRoom.test.ts <!-- id: d7c54432-554e-41a6-b074-d57ca352d8d9-7 -->

## Declarar una constante de módulo en officeMap.ts que referencia TILE_LAYER_DEPTH_BASE ant…

What: Declarar una constante de módulo en officeMap.ts que referencia TILE_LAYER_DEPTH_BASE antes de su declaración produce TypeScript TS2448/TS2454 ('Block-scoped variable used before its declaration'). · Why: officeMap.ts usa const a nivel de módulo en orden secuencial; TILE_LAYER_DEPTH_BASE está declarada más abajo que las constantes de las primeras importaciones. Cualquier constante derivada (p.ej. LOGO_DEPTH) debe aparecer después de TILE_LAYER_DEPTH_BASE en el archivo. · Where: apps/client/src/game/officeMap.ts. · Learned: Antes de agregar una const de módulo en officeMap.ts, verificar el orden de declaraciones con grep -n 'TILE_LAYER_DEPTH_BASE' en el archivo. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-14 -->

## Playwright toma screenshots del canvas de Phaser a device-pixel-ratio 2×, produciendo PNG…

What: Playwright toma screenshots del canvas de Phaser a device-pixel-ratio 2×, produciendo PNGs de ~1920–2×(viewport CSS) px de ancho que superan el límite de procesamiento de imágenes (~2 MB). Las capturas se reciben truncadas o con error. · Why: Phaser escala el canvas al DPR del dispositivo; con viewport 960px CSS el output es 1920px px, demasiado grande. · Where: cualquier browser_take_screenshot del juego en local. · Learned: Antes de capturar, redimensionar a ≤700px CSS (browser_resize(700, 500)) o post-procesar con sips -Z 1100 <file> --out <file-sm>. Nunca usar fullPage:true con el canvas de Phaser. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-16 -->
