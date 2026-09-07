# convention

A rule the codebase follows — naming, patterns, and where things live.

## Las pruebas automatizadas del servidor usan Vitest + @colyseus/testing (boot/createRoom/c…

What: Las pruebas automatizadas del servidor usan Vitest + @colyseus/testing (boot/createRoom/connectTo/waitForNextPatch) contra una instancia real de la sala, cubriendo join de dos clientes, leave consentido inmediato, drop sin aviso (<3s), reconexión dentro de la gracia y refrescos repetidos sin duplicar jugador · Why: — · Where: apps/server/test/OficinaTallerRoom.test.ts <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-10 -->

## Build de producción: el servidor se empaqueta con esbuild a un único archivo (build.mjs)…

What: Build de producción: el servidor se empaqueta con esbuild a un único archivo (build.mjs) más Dockerfile; el cliente se compila como sitio estático con vite build · Why: — · Where: apps/server build.mjs, apps/client vite build <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-11 -->

## `npm run dev` en la raíz levanta servidor y cliente juntos con concurrently, cumpliendo e…

What: `npm run dev` en la raíz levanta servidor y cliente juntos con concurrently, cumpliendo el requisito de un solo comando para desarrollo · Why: — <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-12 -->

## onLeave llama a `allowReconnection(client, RECONNECT_GRACE_SECONDS)` para sostener el asi…

What: onLeave llama a `allowReconnection(client, RECONNECT_GRACE_SECONDS)` para sostener el asiento del jugador durante la ventana de gracia configurable, de modo que si reconecta a tiempo conserva el mismo sessionId en vez de generar un jugador nuevo · Why: — · Where: apps/server OficinaTallerRoom (onLeave) <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-17 -->

## El lint/formato del monorepo usa ESLint 10 en flat config con typescript-eslint más Prett…

What: El lint/formato del monorepo usa ESLint 10 en flat config con typescript-eslint más Prettier, aplicado por igual a packages/shared, apps/server y apps/client · Why: — · Where: eslint.config.* en la raíz, scripts `lint`/`format` por workspace <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-18 -->

## Si el servidor se cae o reinicia, el cliente muestra estados Conectado/Reconectando/Desco…

What: Si el servidor se cae o reinicia, el cliente muestra estados Conectado/Reconectando/Desconectado en el HUD y reintenta joinOrCreate en bucle con backoff (hasta ~10s) en vez de fallar silenciosamente · Why: — · Where: apps/client HUD y Network <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-7 -->

## La colisión se marca con la propiedad `collides` a nivel de capa de tiles/objetos, y pued…

What: La colisión se marca con la propiedad `collides` a nivel de capa de tiles/objetos, y puede sobreescribirse por objeto individual; el spawn se define como un objeto con clase `spawn` y las zonas (recepción, escritorios, sala de reunión, cocina) como objetos con clase `zone` · Why: evita hardcodear layout o spawn en código, cumpliendo el requisito de mapa editable · Where: packages/shared/src/map.ts (findSpawnPoint, getZones, getMapBounds), docs/mapa.md <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-1 -->

## Cuando la URL del cliente lleva el query param `?debug`, config.debug queda en true y exp…

What: Cuando la URL del cliente lleva el query param `?debug`, config.debug queda en true y expone `window.__vto = { game, connection }` además de activar el overlay de colisiones (debugDraw) y forzar el modo de fps por setTimeout · Why: da un gancho reproducible para inspeccionar/testear el estado de Phaser y Colyseus desde la consola o herramientas de automatización de navegador, sin necesitar cambios de código · Where: apps/client/src/main.ts, apps/client/src/config.ts <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-13 -->

## apps/server/test/map.test.ts carga el mapa real del repo (no un mock) y valida que el spa…

What: apps/server/test/map.test.ts carga el mapa real del repo (no un mock) y valida que el spawn esté sobre un tile transitable, que existan las cuatro zonas esperadas y que las imágenes de tileset referenciadas existan en disco · Why: así cualquier edición futura del mapa en Tiled que rompa el contrato esperado por la app falla en los tests antes de llegar a producción · Where: apps/server/test/map.test.ts <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-14 -->

## La pantalla de entrada guarda el nombre y avatar elegidos en localStorage; la conexión Co…

What: La pantalla de entrada guarda el nombre y avatar elegidos en localStorage; la conexión Colyseus solo se abre cuando el usuario pulsa 'Entrar', y esas mismas opciones guardadas se reenvían automáticamente en reconexiones · Why: — · Where: apps/client (pantalla de entrada / módulo de conexión). <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-12 -->

## Además de la lista de presentes de solo lectura, el panel de presencia incluye controles…

What: Además de la lista de presentes de solo lectura, el panel de presencia incluye controles propios: un campo para renombrarse y un botón para fijar/quitar manualmente el estado 'ausente' · Why: — · Where: apps/client/src/ui/presence.ts. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-13 -->

## El panel de presencia ('quién está en la oficina') se alimenta exclusivamente de los call…

What: El panel de presencia ('quién está en la oficina') se alimenta exclusivamente de los callbacks onAdd/onRemove/listen del estado de Colyseus, sin mantener ni inferir su propia lista de jugadores · Why: garantiza que el cliente nunca invente ni retenga jugadores que el servidor ya quitó (requisito de que el servidor es la única fuente de verdad de la lista de presentes) · Where: apps/client/src/ui/presence.ts. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-5 -->

## Las variantes recoloreadas de avatares se generan con un script reproducible (tools/recol…

What: Las variantes recoloreadas de avatares se generan con un script reproducible (tools/recolor-avatars.py, basado en Pillow, solo para desarrollo) que aplica rotación de tono sobre las hojas de sprites base conservando piel y contornos · Why: permite añadir/regenerar variantes de avatar de forma determinista sin editar sprites a mano · Where: tools/recolor-avatars.py. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-7 -->

## El propio jugador envía su posición/animación al servidor con un tope de 20 mensajes por…

What: El propio jugador envía su posición/animación al servidor con un tope de 20 mensajes por segundo, y solo si algo cambió (posición, dirección o si dejó/empezó de moverse) · Why: evita saturar la red y el servidor con actualizaciones redundantes en cada frame · Where: apps/client/src/game/OfficeScene.ts (envío de MOVE). <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-8 -->

## Los avisos de "entró/salió" del panel de presencia solo se muestran después de que termin…

What: Los avisos de "entró/salió" del panel de presencia solo se muestran después de que termina la sincronización inicial del estado, nunca para los jugadores que ya estaban presentes al conectarse · Why: evita mostrar toasts falsos de "entró" para todos los que ya estaban en la oficina cuando un cliente nuevo se conecta · Where: apps/client/src/ui/presence.ts. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-9 -->

## Cuando el cliente se carga con el query param ?debug, expone en window.__vto una referenc…

What: Cuando el cliente se carga con el query param ?debug, expone en window.__vto una referencia a la instancia del juego (Phaser) y su escena, para poder inspeccionar/forzar estado de teclado, posición y animación desde scripts externos (pruebas manuales o automatizadas en navegador) · Why: — · Where: apps/client/src/main.ts. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-15 -->

## El bloque de burbuja del panel lateral tiene tres estados derivados directamente de los c…

What: El bloque de burbuja del panel lateral tiene tres estados derivados directamente de los campos del schema (sin leer eventos ad-hoc): sin burbuja cercana ('Acercate a alguien para conversar'), en conversación (lista de miembros con retrato) y burbuja llena detectada por estar dentro del radio de una burbuja cuyo tamaño ya iguala `bubbleMaxMembers` ('Esa burbuja está llena (tope N)'). · Why: — · Where: apps/client/src/ui/bubble.ts. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-12 -->

## La membresía de burbujas nunca se pide ni se fuerza desde el cliente: no existe ningún me…

What: La membresía de burbujas nunca se pide ni se fuerza desde el cliente: no existe ningún mensaje cliente→servidor para burbujas; todo el estado (schema Bubble con id/x/y/members y Player.bubbleId) se replica solo servidor→cliente vía @colyseus/schema. · Why: requisito explícito de que el cliente no puede forzar su pertenencia a una burbuja. · Where: packages/shared/src/schema.ts. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-3 -->

## Para agregar un avatar de persona al catálogo: generar el PNG 1664×48 con `tools/person-a…

What: Para agregar un avatar de persona al catálogo: generar el PNG 1664×48 con `tools/person-avatars.py`, copiarlo a `apps/client/public/assets/avatars/<id>.png`, añadir la entrada a `packages/shared/src/avatars.ts`, y actualizar el conteo en `apps/server/test/identity.test.ts`. · Why: — · Where: `tools/person-avatars.py`, `packages/shared/src/avatars.ts`, `apps/server/test/identity.test.ts` <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-4 -->

## El logo pixel art de Taller se almacena en `apps/client/public/assets/logo/taller-logo-pi…

What: El logo pixel art de Taller se almacena en `apps/client/public/assets/logo/taller-logo-pixel.png`, dimensiones 160×32 px (5×1 tiles de 32 px), fondo transparente, paleta reducida con contorno oscuro para legibilidad sobre pisos claros y oscuros. · Why: — · Where: `apps/client/public/assets/logo/taller-logo-pixel.png` <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-5 -->

## Todo archivo `.md` y `.ts` nuevo o editado debe pasar `npx prettier --write` antes de com…

What: Todo archivo `.md` y `.ts` nuevo o editado debe pasar `npx prettier --write` antes de commit; las tablas markdown en docs se reformatean automáticamente y rompen `prettier --check` si se escriben manualmente sin alineación exacta. · Why: — · Where: `docs/`, raíz del repo · Learned: Correr `npm run format:check` (o `prettier --check .`) antes de dar el trabajo por terminado. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-8 -->
