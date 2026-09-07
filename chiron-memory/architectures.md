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

## El catálogo de avatares vive en packages/shared/src/avatars.ts y exporta AVATARS, AVATAR_…

What: El catálogo de avatares vive en packages/shared/src/avatars.ts y exporta AVATARS, AVATAR_IDS, DEFAULT_AVATAR e isAvatarId. · Why: Añadir un id aquí auto-cablea el selector UI (ui/entry.ts recorre AVATARS), el preload de BootScene, la validación del servidor y los thumbnails (avatarThumb.ts), sin tocar ningún archivo más. · Where: packages/shared/src/avatars.ts; carga efectiva en apps/client/src/game/BootScene.ts. · Learned: Cada personaje nuevo o avatar del equipo solo requiere una entrada en este catálogo + el PNG en avatars/. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-1 -->

## El formato de cada hoja de sprites de avatar es 1664×48 px (una fila, 52 frames de 32×48…

What: El formato de cada hoja de sprites de avatar es 1664×48 px (una fila, 52 frames de 32×48 px). Layout LimeZu: idle R/U/L/D frames 0–23, walk R/U/L/D frames 24–47, sentado frames 48–51; 6 frames por animación. · Why: Lo impone el motor Phaser y la función createAvatarAnims; cambiar este contrato requiere tocar ambas cosas. · Where: apps/client/src/game/avatarAnims.ts; packages/shared/src/avatars.ts (AVATAR_FRAME, FRAMES_PER_ANIM). · Learned: El work order decía '4 frames por animación'; el código real exige 6 — siempre verificar contra el código, no contra el enunciado. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-2 -->

## officeMap.ts renderiza objetos Tiled que tienen gid como sprites con depth = obj.y

What: officeMap.ts renderiza objetos Tiled que tienen gid como sprites con depth = obj.y. La colisión no depende de si el objeto tiene geometría: solo colisiona si el objeto o la capa tiene la propiedad collides:true; el cuerpo físico de muebles vive en la capa MueblesColision separada. · Why: Por esto un sprite decorativo sin esa propiedad nunca bloquea, sin importar su posición. · Where: apps/client/src/game/officeMap.ts (renderObjects / objectCollides). · Learned: Para que un objeto sea puramente decorativo, basta con no marcar collides:true; no hace falta ningún flag extra. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-7 -->

## El mapa es 40×30 tiles a 32 px = 1280×960 unidades de mundo

What: El mapa es 40×30 tiles a 32 px = 1280×960 unidades de mundo. La zona Recepción ocupa x 640–1056, y 96–288; el spawn del jugador es (848, 208). · Why: Referencia para posicionar assets decorativos programáticamente (logo, carteles) sin abrir Tiled. · Where: apps/client/src/game/officeMap.ts (getZones, TILE_SIZE); mapa Tiled en apps/client/public/assets/map/. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-8 -->

## El servidor (`ChatRelay` en apps/server/src/chat.ts) calcula los destinatarios de un mens…

What: El servidor (`ChatRelay` en apps/server/src/chat.ts) calcula los destinatarios de un mensaje como los miembros de la burbuja del remitente **en el instante del envío**, vía `clients.getById`; el cliente nunca envía `bubbleId` ni pide membresía · Why: la membresía de burbujas es autoridad exclusiva del servidor (schema Bubble/Player.bubbleId), consistente con la convención ya existente de que el cliente nunca fuerza membresía · Where: apps/server/src/chat.ts, apps/server/src/OficinaTallerRoom.ts <!-- id: d7c54432-554e-41a6-b074-d57ca352d8d9-1 -->

## `ChatRelay` (apps/server/src/chat.ts) aplica rate-limit por jugador además de resolver de…

What: `ChatRelay` (apps/server/src/chat.ts) aplica rate-limit por jugador además de resolver destinatarios por membresía de burbuja, para evitar spam de mensajes · Why: — · Where: apps/server/src/chat.ts <!-- id: d7c54432-554e-41a6-b074-d57ca352d8d9-10 -->

## El globo de diálogo sobre el avatar (`Avatar.say()`) se implementa con un `Phaser.Text` t…

What: El globo de diálogo sobre el avatar (`Avatar.say()`) se implementa con un `Phaser.Text` temporal que desaparece solo tras unos segundos, desacoplado del panel de chat que sí mantiene historial local · Why: — · Where: apps/client/src/game/Avatar.ts <!-- id: d7c54432-554e-41a6-b074-d57ca352d8d9-8 -->

## La identidad del usuario persiste en localStorage con clave 'vto.identity', valor JSON.st…

What: La identidad del usuario persiste en localStorage con clave 'vto.identity', valor JSON.stringify({name, avatar}). Al cargar, ui/entry.ts valida el campo avatar vía isAvatarId y cae a DEFAULT_AVATAR si el id es desconocido. · Why: Es el mecanismo de persistencia de avatar entre recargas (criterio 7). Útil para debugging manual: localStorage.setItem('vto.identity', JSON.stringify({name:'Test', avatar:'adam'})) + recarga. Para tests Playwright multi-pestaña en modo ?debug, se puede fijar antes de navegar. · Where: apps/client/src/ui/entry.ts (STORAGE_KEY); packages/shared/src/avatars.ts (isAvatarId, DEFAULT_AVATAR). <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-15 -->
