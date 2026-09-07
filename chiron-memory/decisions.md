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

## Los avatares de personas del equipo se generan recoloreando de forma independiente pelo y…

What: Los avatares de personas del equipo se generan recoloreando de forma independiente pelo y ropa de los sprites base LimeZu (lucy, nancy, ash) con tools/person-avatars.py, en lugar de generar pixel art desde cero. · Why: Garantiza formato 1664×48 idéntico al de los 8 avatares existentes, consistencia visual de paleta y escala, y no requiere software externo. Las alternativas (IA generativa, editor manual) no producen el formato exacto sin postprocesado. · Where: virtual-taller-office/tools/person-avatars.py; apps/client/public/assets/avatars/persona{1,2,3}.png. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-5 -->

## El logo de Taller se coloca como sprite decorativo de piso en la zona Recepción mediante…

What: El logo de Taller se coloca como sprite decorativo de piso en la zona Recepción mediante addReceptionLogo en officeMap.ts, con depth menor que TILE_LAYER_DEPTH_BASE (calcomanía de piso). · Why: Sin cuerpo físico, nunca bloquea el paso; al tener profundidad de piso, avatares y muebles lo cubren correctamente por z-order. La alternativa (colocarlo en el editor Tiled como tile) complicaría las licencias del tileset y el flujo de edición del mapa. · Where: apps/client/src/game/officeMap.ts (addReceptionLogo); config.logoUrl → apps/client/public/assets/logo/taller-logo-pixel.png. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-6 -->

## El chat de burbuja no vive en el schema de Colyseus; usa `room.send`/`room.onMessage` (CH…

What: El chat de burbuja no vive en el schema de Colyseus; usa `room.send`/`room.onMessage` (CHAT_SEND/CHAT_MESSAGE/CHAT_ERROR en packages/shared/src/messages.ts) y el servidor solo retransmite, sin persistencia · Why: SkyOffice original hacía broadcast global y persistía en `chatMessages` para toda la sala; se descartó eso y se conservó solo la idea del globo de diálogo sobre el avatar · Where: packages/shared/src/chat.ts, apps/server/src/chat.ts, apps/client/src/ui/chat.ts <!-- id: d7c54432-554e-41a6-b074-d57ca352d8d9-0 -->

## Al enviar un mensaje con Enter, el campo de chat suelta el foco automáticamente (permitie…

What: Al enviar un mensaje con Enter, el campo de chat suelta el foco automáticamente (permitiendo moverse de inmediato) y otro Enter lo reabre, en vez de dejar el campo abierto hasta Esc · Why: pedido explícito del usuario para un flujo de "camino unos pasos, digo algo"; si el mensaje es rechazado (vacío, muy largo, sin burbuja) el foco se mantiene para poder corregir el texto · Where: apps/client/src/ui/chat.ts (función send()) <!-- id: d7c54432-554e-41a6-b074-d57ca352d8d9-5 -->

## ** Los 3 avatares del equipo (`persona1/2/3`, en `apps/client/public/assets/avatars/`) se…

What: ** Los 3 avatares del equipo (`persona1/2/3`, en `apps/client/public/assets/avatars/`) se generaron con `tools/person-avatars.py`, que parte de los sprites base LimeZu y recolorea pelo y ropa **de forma independiente** (a diferencia de `recolor-avatars.py`, que rota un único tono sobre todo el personaje). Cada persona se mapeó al base cuya silueta de pelo mejor coincide: persona1→lucy (pelo largo claro), persona2→nancy (pelo largo negro + campera), persona3→ash (pelo más corto). Se sumaron al catálogo (`packages/shared/src/avatars.ts`, ahora 11 avatares; el test lo fija en 11). El logo se convirtió a pixel art (`assets/logo/taller-logo-pixel.png`, 160×32, transparente, con contorno oscuro para legibilidad) desde `images/logo.png`. - ** · Why: ** El work order pedía "generar con IA y retocar", pero no hay una herramienta de IA que produzca hojas de 52 frames en el layout exacto que espera la oficina, y el parecido a 32×48 se juega solo en pelo/ropa/accesorios (la cara no se distingue). Derivar de los bases garantiza formato exacto, animación completa y coherencia con los avatares existentes, reutilizando la convención del repo. - ** · Where: ** `virtual-taller-office/tools/person-avatars.py`, `apps/client/public/assets/avatars/persona*.png`, `apps/client/public/assets/logo/`, `packages/shared/src/avatars.ts`, `docs/assets-pixel-art.md`. - ** · Learned: ** Las hojas de avatar son 1664×48 (52 frames de 32×48, 6 por animación); el layout está en `AVATAR_FRAME`/`ANIM_START`. Los originales viven en el repo `images/` y no se tocan. Aplica el mismo conflicto de licencia LimeZu ya documentado (repo público) que los demás avatares/tilesets. <!-- id: spine-9eecf0bf156a2536 -->
