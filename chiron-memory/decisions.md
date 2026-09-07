# decision

A choice made and the reasoning behind it — the path taken over the alternatives.

## Todo el desarrollo de la fundación tiempo-real va exclusivamente en el repo Virtual-Talle…

What: Todo el desarrollo de la fundación tiempo-real va exclusivamente en el repo Virtual-Taller-Office (remoto Taller-G/Virtual-Taller-Office); los repos context/ (fork de SkyOffice) y workadventure/ son solo inspiración y no deben modificarse ni recibir commits · Why: instrucción explícita del usuario para no trabajar dentro de los repos de referencia <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-1 -->

## El cliente en tiempo real usa @colyseus/sdk (0.18) en vez de colyseus.js, que quedó conge…

What: El cliente en tiempo real usa @colyseus/sdk (0.18) en vez de colyseus.js, que quedó congelado en la línea 0.16 (legado) · Why: @colyseus/sdk es el paquete oficial vigente, trae reconexión automática integrada, eventos onDrop/onReconnect y Callbacks.get(room) en vez de onAdd directo sobre el estado · Where: apps/client servicio de red (Network) <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-3 -->

## El estado compartido (Player, OfficeState) se define con @colyseus/schema 5 usando schema…

What: El estado compartido (Player, OfficeState) se define con @colyseus/schema 5 usando schema()/t.* en vez de decoradores de clase · Why: evita el conflicto de experimentalDecorators con Vite/esbuild y permite compartir la definición de esquema entre cliente y servidor sin duplicar código · Where: packages/shared <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-4 -->

## Se usó Phaser 3.90.0 (última versión de la línea 3) en vez de Phaser 4.2.1

What: Se usó Phaser 3.90.0 (última versión de la línea 3) en vez de Phaser 4.2.1 · Why: el work order pedía explícitamente Phaser 3 aunque la 4 sea la más reciente publicada <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-8 -->

## El script Python usado para convertir el map.json de SkyOffice al layout de Taller (separ…

What: El script Python usado para convertir el map.json de SkyOffice al layout de Taller (separar capas, renombrar zonas, retocar muebles) se escribió como uso único en el scratchpad temporal y deliberadamente no se versionó en el repo · Why: el JSON resultante (apps/client/public/assets/map/oficina-taller.json) es la fuente de verdad y debe editarse directamente en Tiled de ahí en adelante, no regenerarse por script · Where: apps/client/public/assets/map/oficina-taller.json <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-10 -->

## Se agregó un movimiento mínimo de jugador con flechas/WASD y cuerpo Arcade en OfficeScene…

What: Se agregó un movimiento mínimo de jugador con flechas/WASD y cuerpo Arcade en OfficeScene.ts, a pesar de que la tarea de movimiento de la que ésta depende no estaba implementada en la rama · Why: sin movimiento no había forma de verificar en vivo que las colisiones del mapa (paredes, muebles) funcionan; se documentó como implementación provisoria a reconciliar cuando llegue la tarea de movimiento real · Where: apps/client/src/game/OfficeScene.ts <!-- id: c054e01f-3584-45a3-83a8-5fadd7ad60f5-8 -->

## Se generaron 4 avatares adicionales recoloreando (rotación de tono) los 4 sprites LimeZu…

What: Se generaron 4 avatares adicionales recoloreando (rotación de tono) los 4 sprites LimeZu de SkyOffice (adam, ash, lucy, nancy) para completar el catálogo de 8 requerido, en vez de usar los sprites Pipoya 32x32 de WorkAdventure · Why: la licencia LimeZu permite editar los sprites (documentado en docs/licencias-assets.md); los sprites Pipoya desentonan visualmente con el mapa existente · Where: tools/recolor-avatars.py genera bruno/dana/iris/tomas.png a partir de adam/ash/lucy/nancy.png; catálogo final en packages/shared/src/avatars.ts. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-0 -->

## El estado 'ausente' manual es pegajoso: una vez fijado con el botón, solo se quita manual…

What: El estado 'ausente' manual es pegajoso: una vez fijado con el botón, solo se quita manualmente, nunca por reanudar el movimiento; el ausente automático (por AWAY_AFTER_SECONDS de inactividad) sí se limpia con cualquier movimiento · Why: evita que mover el mouse/teclado por accidente saque a alguien de 'ausente' cuando lo fijó a propósito (ej. reunión) · Where: apps/server/src/rooms/OficinaTallerRoom.ts (campos away/awayManual en el Player). <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-1 -->

## Las burbujas de conversación se crean en cuanto dos jugadores quedan dentro del radio con…

What: Las burbujas de conversación se crean en cuanto dos jugadores quedan dentro del radio configurado, sin esperar a que se detengan (WorkAdventure sí espera); la salida se mide contra la distancia al baricentro actual de la burbuja, no contra el radio de creación. · Why: da histéresis natural (se sueltan al doble de distancia de la de entrada, sin parpadeo) y cumple el requisito de reaccionar en <300ms sin depender de que el jugador frene. · Where: apps/server/src/bubbles.ts. · Learned: la semántica se adaptó de WorkAdventure (back/src/Model/Group.ts, GameRoom.updateUserGroup) sin copiar el código. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-1 -->

## Los avatares de las tres personas del equipo se generan recoloreando los sprites base de…

What: Los avatares de las tres personas del equipo se generan recoloreando los sprites base de LimeZu (no con IA generativa ni arte desde cero): persona1→lucy (pelo largo claro), persona2→nancy (pelo largo oscuro + chaqueta), persona3→ash (pelo más corto). · Why: Garantiza formato exacto (1664×48, 52 frames) y coherencia visual con los 8 avatares existentes sin riesgo de dimensiones incorrectas. La herramienta es `tools/person-avatars.py`, paralela a `tools/recolor-avatars.py`. · Where: `tools/person-avatars.py`, `apps/client/public/assets/avatars/persona{1,2,3}.png` <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-3 -->
