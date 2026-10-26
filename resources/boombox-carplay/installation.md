# Installation

1. Unzip. You get one folder: `gs-boombox-carplay`. Put it in your server's `resources/`.
   Do not rename it (the ox_inventory item wiring in [Inventory](inventory.md) uses the name).
2. Add to `server.cfg`:
   ```
   ensure gs-boombox-carplay
   add_ace group.admin gsbc.admin allow
   ```
   If you use ESX / QBCore / QBox / ox_inventory / oxmysql, start them **before** this line.
3. Make sure OneSync is on (`set onesync on`). It works without, but then the server has to
   believe clients about where they are (the doctor tells you).
4. Start the server. The console prints one line:
   ```
   [gsbc] INFO  started v1.0.0 | framework=standalone | items=standalone | store=file (...) | onesync=on | stations=6 | demo tracks=3
   ```
   A typo in a config file is printed right below it as `[gsbc] WARN  config.lua: ...`.

There is nothing to build and no database to set up. The folder `data/` inside the resource must
stay there and be writable: playlists and queues are saved in it.

Requirements: a current FiveM server artifact (the manifest uses `lua54`). No framework is required. With no inventory, set `Config.Boombox.item.required = false` and
`Config.Shop.enabled = false` so ordinary players can place speakers. The default shop requires `ox_inventory`.

## Inventory and First Use

1. Define the items in [Inventory](inventory.md), or disable the item requirement for standalone command placement.
2. Set permissions, destinations and screen/vehicle preferences in [Configuration](configuration.md).
3. As an administrator, place a speaker with `/boombox`, confirm its placement preview, open its library and play **Neon Drive**.
4. Test `/gsbc doctor` in game and `gsbc doctor` in the server console.
5. Verify two players, item returns, fitted screens, restart recovery and your actual audio hosts before launch.

The optional `npwd_gs_boombox` app is a separate ZIP. Install it only when using NPWD; see [Phones & QR Remotes](phones.md). Do not install the release’s marketing or verification folders as resources.

## Asset Escrow

The manifest leaves only these Lua files open:

| File | Purpose |
|---|---|
| `config/config.lua` | Commands, keys, permissions, audio, cars, shop, phones and feature settings |
| `config/stations.lua` | Radio definitions |
| `config/props.lua` | Speaker models, items and carry calibration |
| `config/server.lua` | Storage and position-check policy |
| `server/bridge.lua` | Framework identity, inventory and usable-item integration |
| `shared/phoneapps.lua` | In-game phone app adapters |
| `shared/messages.lua` | Lua notifications and refusal wording |

The other Lua files are prepared for Cfx portal escrow processing. NUI production assets are required at runtime; web source, dependencies, development tools and saved player data are excluded.
