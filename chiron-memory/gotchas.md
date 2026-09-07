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

## Los tilesets PNG usados (LimeZu Modern Interiors / Modern Office) tienen licencia que per…

What: Los tilesets PNG usados (LimeZu Modern Interiors / Modern Office) tienen licencia que permite uso comercial/no comercial con atribución pero prohíbe expresamente redistribuir el asset pack, mientras que Taller-G/Virtual-Taller-Office es un repo público · Why: el requisito de 'licencia compatible con uso interno' choca con publicar los PNG en un repo público; queda pendiente decidir entre pasar el repo a privado, servir los assets desde un bucket privado, o reemplazarlos · Where: docs/licencias-assets.md, apps/client/public/assets/tilesets/ <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-5 -->

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

## El sprite sheet de avatar requiere **52 frames de 32×48 px** en una sola fila (1664×48 px…

What: El sprite sheet de avatar requiere **52 frames de 32×48 px** en una sola fila (1664×48 px total), con **6 frames por animación** — no 4 como describía la work order. · Why: El layout está fijado en `packages/shared/src/avatars.ts` y en `CharacterAnims.ts`; el work order decía 4 frames pero el código es la fuente de verdad. · Where: `packages/shared/src/avatars.ts`, `apps/client/src/anims/CharacterAnims.ts` · Learned: Antes de generar cualquier sprite sheet nuevo, verificar el frame count en el código, no en la especificación del ticket. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-0 -->

## El test `apps/server/test/identity.test.ts` hardcodea el número de avatares (`toHaveLengt…

What: El test `apps/server/test/identity.test.ts` hardcodea el número de avatares (`toHaveLength(8)`); se rompe al agregar nuevos avatares al catálogo. · Why: El test valida el catálogo completo para detectar entradas mal configuradas. · Where: `apps/server/test/identity.test.ts` · Learned: Actualizar este conteo junto con cada adición al catálogo de avatares. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-2 -->

## Los assets de LimeZu (sprites base, tilesets) se pueden editar pero no redistribuir; el r…

What: Los assets de LimeZu (sprites base, tilesets) se pueden editar pero no redistribuir; el repo `virtual-taller-office` debe permanecer **privado** mientras contenga estos archivos. El logo de Taller es marca propia y no tiene esta restricción. · Why: Licencia LimeZu prohíbe redistribución a terceros. · Where: `docs/licencias-assets.md` · Learned: Cualquier asset nuevo derivado de LimeZu debe marcarse con 'edición: Taller' en `licencias-assets.md` y el repo debe mantenerse privado. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-7 -->
