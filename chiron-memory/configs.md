# config

Setup and configuration — env vars, flags, how to run the project.

## Config del servidor por env vars sin hardcodeo: PORT, MAX_CLIENTS, RECONNECT_GRACE_SECOND…

What: Config del servidor por env vars sin hardcodeo: PORT, MAX_CLIENTS, RECONNECT_GRACE_SECONDS, PING_INTERVAL_MS, PING_MAX_RETRIES; el cliente requiere VITE_SERVER_URL · Why: — · Where: apps/server/.env.example, apps/client/.env.example <!-- id: 0b8b6f22-9fc7-444f-a6c3-f29289e8ef82-9 -->

## El servidor expone un endpoint HTTP /health que responde con el nombre de la sala, la can…

What: El servidor expone un endpoint HTTP /health que responde con el nombre de la sala, la cantidad de salas activas y la cantidad de jugadores conectados, útil para monitoreo externo · Why: — · Where: apps/server (health check). <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-14 -->

## La variable de entorno AWAY_AFTER_SECONDS (default 300) controla en el servidor cuántos s…

What: La variable de entorno AWAY_AFTER_SECONDS (default 300) controla en el servidor cuántos segundos de inactividad activan el estado 'ausente' automático de un jugador · Why: — · Where: apps/server/.env / apps/server/src/rooms/OficinaTallerRoom.ts. <!-- id: cda9a66a-6633-4581-8374-8bb7e06751f0-6 -->

## Radio y tope de burbujas se configuran por env var: BUBBLE_RADIUS_PX (default = 2 × tilew…

What: Radio y tope de burbujas se configuran por env var: BUBBLE_RADIUS_PX (default = 2 × tilewidth del mapa, 64px con tiles de 32px) y BUBBLE_MAX_MEMBERS (default 6). · Why: — · Where: apps/server/.env.example, apps/server/src/config.ts. · Learned: derivar el default del tamaño de tile del mapa en vez de hardcodear px hace que '2 tiles' siga siendo 2 tiles si el mapa cambia de escala. <!-- id: ad7b81b0-f714-46dc-bfac-6327a3363b23-5 -->

## Las herramientas de generación de assets (tools/recolor-avatars.py, tools/person-avatars.…

What: Las herramientas de generación de assets (tools/recolor-avatars.py, tools/person-avatars.py) requieren Python 3 + Pillow. En macOS el pip del sistema bloquea instalaciones globales; se usa un venv local: python3 -m venv .venv-assets && .venv-assets/bin/pip install pillow. · Why: El venv es solo para desarrollo; el resultado (PNG) se versiona. · Where: virtual-taller-office/tools/. <!-- id: ce94ff8e-e247-4c82-a62f-127c7251fb4c-13 -->
