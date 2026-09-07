# architecture

How the system is put together — layers, boundaries, and how data flows.

## Virtual-Taller-Office es un monorepo npm workspaces con apps/server (Colyseus), apps/clie…

What: Virtual-Taller-Office es un monorepo npm workspaces con apps/server (Colyseus), apps/client (Phaser+Vite) y packages/shared (contratos/tipos compartidos) · Why: separa despliegue (cliente estático vs servicio Node) mientras comparte esquema de estado y constantes entre ambos · Where: virtual-taller-office/apps/server, apps/client, packages/shared <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-0 -->

## OficinaTallerRoom guarda a los jugadores en un MapSchema del estado compartido usando el…

What: OficinaTallerRoom guarda a los jugadores en un MapSchema del estado compartido usando el sessionId de Colyseus como clave; al unirse, el cliente recibe su propio sessionId y el estado completo de la sala (incluido ese Map) en la primera sincronización · Why: — · Where: apps/server OficinaTallerRoom (onJoin), apps/client Network/OfficeScene <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-15 -->

## El servidor arranca con el patrón oficial de Colyseus 0.18 (`defineServer`/`defineRoom` +…

What: El servidor arranca con el patrón oficial de Colyseus 0.18 (`defineServer`/`defineRoom` + `listen()` de @colyseus/tools, siguiendo la plantilla create-colyseus-app) en vez de instanciar `colyseus.Server` a mano, y expone un endpoint `/health` · Why: — · Where: apps/server app.config.ts / index.ts <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-16 -->

## Existe una única sala persistente OficinaTallerRoom con autoDispose=false, pre-creada al…

What: Existe una única sala persistente OficinaTallerRoom con autoDispose=false, pre-creada al arrancar el servidor; el cliente siempre hace joinOrCreate y cae en esa misma instancia · Why: cumple el requisito de sala única sin lobby, salas custom ni contraseñas · Where: apps/server sala OficinaTallerRoom <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-5 -->

## El ciclo de conexión combina leave consentido inmediato (room.leave(true) en pagehide del…

What: El ciclo de conexión combina leave consentido inmediato (room.leave(true) en pagehide del cliente) con una gracia corta configurable en el servidor (onDrop, default 2s vía RECONNECT_GRACE_SECONDS) para desconexiones no consentidas antes de eliminar al jugador · Why: cerrar pestaña o refrescar no deja jugadores duplicados/fantasma, y una caída de red real igual se refleja en <3s para el resto de los jugadores · Where: apps/server OficinaTallerRoom (onLeave/onDrop), apps/client Network <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-6 -->

## El mapa de la oficina Taller es 100% data-driven vía Tiled JSON (apps/client/public/asset…

What: El mapa de la oficina Taller es 100% data-driven vía Tiled JSON (apps/client/public/assets/map/oficina-taller.json), derivado del mapa de SkyOffice, con capas Piso, Paredes, Muebles, MueblesColision, Zonas y Spawn · Why: así cualquiera del equipo puede rediseñar la oficina editando el JSON en Tiled sin tocar código · Where: apps/client/public/assets/map/oficina-taller.json, docs/mapa.md <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-0 -->

## packages/shared/src/map.ts centraliza el parseo del subconjunto de Tiled JSON usado (tipo…

What: packages/shared/src/map.ts centraliza el parseo del subconjunto de Tiled JSON usado (tipos + funciones puras) y lo comparte cliente/servidor · Why: evita duplicar lógica de lectura del mapa y ata cliente/servidor al mismo contrato de nombres de propiedades/clases en vez de a nombres de capa · Where: packages/shared/src/map.ts <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-2 -->

## El servidor carga el mapa al crear la sala desde la ruta indicada por la variable de ento…

What: El servidor carga el mapa al crear la sala desde la ruta indicada por la variable de entorno MAP_FILE (por defecto el mismo JSON que usa el cliente), toma el punto de spawn de ahí y falla con un mensaje claro si el mapa es inválido o falta el spawn · Why: elimina el hardcodeo de coordenadas de spawn en el schema del jugador, requerido por el work order · Where: apps/server/src/map.ts, apps/server/src/rooms/OficinaTallerRoom.ts <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-3 -->

## El servidor es autoritativo sobre la posición del jugador: al recibir el mensaje `move` d…

What: El servidor es autoritativo sobre la posición del jugador: al recibir el mensaje `move` del cliente, acota (clamp) x/y a los límites del mapa antes de actualizar el estado y replicarlo a los demás clientes · Why: evita que un cliente pueda enviar coordenadas fuera del mapa · Where: apps/server/src/rooms/OficinaTallerRoom.ts, packages/shared (mensaje move) <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-12 -->

## BootScene lee el Tiled JSON del mapa y encola automáticamente las imágenes de los tileset…

What: BootScene lee el Tiled JSON del mapa y encola automáticamente las imágenes de los tilesets que el propio JSON declara, en vez de listarlos a mano en el código de carga · Why: agregar o quitar un tileset en Tiled no requiere tocar el código de carga del cliente · Where: apps/client/src/game/BootScene.ts, apps/client/src/game/officeMap.ts <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-9 -->

## La profundidad (z-order) de los sprites de avatar se recalcula dinámicamente según su coo…

What: La profundidad (z-order) de los sprites de avatar se recalcula dinámicamente según su coordenada y, para que los jugadores más abajo en el mapa se dibujen por encima de los que están más arriba · Why: — · Where: apps/client/src/game/OfficeScene.ts / Avatar. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-10 -->

## El estado 'ausente' automático se evalúa en el servidor con un timer periódico (clock.set…

What: El estado 'ausente' automático se evalúa en el servidor con un timer periódico (clock.setInterval de 1 segundo) que compara el tiempo de inactividad de cada jugador contra AWAY_AFTER_SECONDS, en vez de programar un timeout individual por jugador · Why: — · Where: apps/server/src/rooms/OficinaTallerRoom.ts. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-11 -->

## El protocolo cliente-servidor separa el mensaje MOVE ({x, y, dir, moving}) de los mensaje…

What: El protocolo cliente-servidor separa el mensaje MOVE ({x, y, dir, moving}) de los mensajes SET_NAME y SET_AWAY, y las opciones de join ({name, avatar}) se validan en el paquete compartido con fallback a nombre 'Invitado-xxxx' y avatar por defecto si son inválidas · Why: mantiene el servidor como única fuente de verdad de identidad y evita que clientes maliciosos o con datos corruptos inyecten nombres/avatares fuera de catálogo · Where: packages/shared (validadores), apps/server/src/rooms/OficinaTallerRoom.ts (onJoin/onMessage), types/Messages.ts. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-2 -->

## Los avatares de otros jugadores (OtherPlayer/otros players) se interpolan con suavizado e…

What: Los avatares de otros jugadores (OtherPlayer/otros players) se interpolan con suavizado exponencial basado en tiempo transcurrido (no en frames), con un 'snap' inmediato a la posición real cuando el salto es grande o la pestaña estuvo oculta · Why: el suavizado por frame depende del framerate y produce movimiento errático a distintos FPS; sin snap, al volver de una pestaña oculta el avatar 'persigue' lentamente la posición real durante segundos · Where: apps/client/src/game/OfficeScene.ts / lógica de otros jugadores. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-4 -->

## El repo real del proyecto es el monorepo virtual-taller-office/ (apps/server, apps/client…

What: El repo real del proyecto es el monorepo virtual-taller-office/ (apps/server, apps/client, packages/shared); context/ (fork SkyOffice) y workadventure/ son solo referencia y no se modifican, aunque el work order describa rutas de esos proyectos. · Why: las rutas de un ticket pueden describir el sistema de referencia, no el código propio. · Where: virtual-taller-office/apps/{server,client}, packages/shared. · Learned: verificar siempre contra el repo real antes de mapear rutas de un work order. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-0 -->

## El indicador visual de burbuja en el cliente es un módulo dedicado (BubbleArea.ts) que di…

What: El indicador visual de burbuja en el cliente es un módulo dedicado (BubbleArea.ts) que dibuja un círculo (Phaser Graphics) por burbuja centrado en su baricentro replicado, interpolado frame a frame igual que los avatares; la burbuja propia se resalta con el color de acento y las ajenas se pintan atenuadas, y además se agrega un anillo alrededor de los avatares que son miembros de la burbuja propia. · Why: — · Where: apps/client/src/game/BubbleArea.ts, apps/client/src/game/Avatar.ts, apps/client/src/game/OfficeScene.ts. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-11 -->

## La lógica de agrupamiento por proximidad vive en un módulo puro apps/server/src/bubbles.t…

What: La lógica de agrupamiento por proximidad vive en un módulo puro apps/server/src/bubbles.ts que opera sobre el estado de Colyseus, invocado desde OficinaTallerRoom tanto en el handler de movimiento (después del clamp a los límites del mapa) como en onLeave. · Why: — · Where: apps/server/src/bubbles.ts, apps/server/src/rooms/OficinaTallerRoom.ts. · Learned: separar la lógica de dominio de la sala permite testearla unitariamente sin levantar un servidor Colyseus. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-2 -->

## OfficeState replica también bubbleRadius y bubbleMaxMembers (no solo la lista de burbujas…

What: OfficeState replica también bubbleRadius y bubbleMaxMembers (no solo la lista de burbujas) como campos de solo lectura. · Why: así el cliente dibuja el radio real y detecta 'burbuja llena' con los mismos valores que usa el servidor para decidir, en vez de hardcodear una copia que puede desincronizarse. · Where: packages/shared/src/schema.ts, apps/server/src/config.ts. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-4 -->

## El catálogo de avatares vive en `packages/shared/src/avatars.ts` (array `AVATARS`), y el…

What: El catálogo de avatares vive en `packages/shared/src/avatars.ts` (array `AVATARS`), y el cliente los auto-descubre desde ahí — no hay hardcoding de IDs en las escenas de Phaser. · Why: Centralizar el catálogo en `shared` permite que server y client compartan la misma fuente de verdad sin duplicar la lista. · Where: `packages/shared/src/avatars.ts`, `apps/client/src/game/Bootstrap.ts` <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-1 -->

## El sprite sheet de avatar sigue este layout de 52 frames (fila única, 32×48 px c/u): fram…

What: El sprite sheet de avatar sigue este layout de 52 frames (fila única, 32×48 px c/u): frames 0–23 → idle en 4 direcciones (R/U/L/D, 6 frames c/d), frames 24–47 → walk en 4 direcciones (R/U/L/D, 6 frames c/d), frames 48–51 → sentado (4 frames). · Why: — · Where: `apps/client/src/anims/CharacterAnims.ts`, `packages/shared/src/avatars.ts` <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-9 -->
