# Troubleshooting

### The studio does not open

1. Check the F8 console and the server console for errors.
2. If `Designer.RequireJob = true`, confirm the player's job and grade are in `Designer.jobs`.
3. If you disabled `OpenCommandEnabled`, the studio is only reachable through stations, passes, or exports.

### Forging fails with a "no slots" or "server full" message

1. All slots for that weapon are occupied, or the server-wide `maxTotalDesigns` ceiling is reached.
2. Run `gswd_pool_status` in the server console to see per-weapon occupancy.
3. Raise the weapon's count in `data/weapon_slots.json` and restart — see [Configuration](configuration.md#the-slot-system-dataweapon_slotsjson).
4. Deleting unused designs also frees slots immediately, with no restart needed.

### Slot changes in weapon_slots.json have no effect

1. The resource only reads the file at start — edit it while the resource is stopped, then start it.
2. New slots need their streamed files mounted: if the console reports `fxmanifest.lua` was updated, restart the resource once more.
3. Values below 1 or above 2048 are clamped; a weapon cannot be set to 0.

### The resource refuses to start after editing weapon_slots.json

1. The file failed to parse — the resource deliberately stops instead of overwriting your edits. Check for a missing comma or bracket.
2. The console error names the problem; fix the JSON and start again.
3. If the sum of all slot counts exceeds `maxTotalDesigns`, raise the ceiling or lower the counts.

### Forged weapons are missing or invisible in hand

1. Confirm the resource was restarted after the console reported an `fxmanifest.lua` update — un-mounted slot files stream nothing.
2. On `ox_inventory`, confirm the bridge module is installed in both script lists of ox's `fxmanifest.lua` — see [Installation](installation.md#ox_inventory-bridge-module).
3. Check the server console for slot or streaming errors from the boot sequence.

### The skin does not show on the weapon

1. `Config.RenderSystem` should be `'runtime'` unless you are deliberately testing hybrid mode.
2. Other players only see skins within `Runtime.renderDistance`.
3. If you switched to `'hybrid'`, baked textures only appear after a full server restart — and hybrid mode is experimental. Switch back to `'runtime'` if clients crash.

### Item preview images are blank

1. With the `discord` or `fivemanage` providers and `ox_inventory`, the image host must be added to ox's `inventory:validhosts` convar.
2. Confirm the matching credential is set in `server/credentials.lua` (`DiscordWebhookUrl` or `FiveManageApiKey`).
3. The `database` provider needs no credentials and is the simplest fallback.

### Designs are not saving

1. Ensure `oxmysql` starts **before** `gs-weapondesigner`.
2. Check the server console for MySQL errors — the tables are auto-created on boot, so a failed migration points at database permissions.

### oxmysql exports are not available

1. Add `ensure oxmysql` above `ensure gs-weapondesigner` in `server.cfg`.
2. Restart the server so the start order applies.

---

## Information to Send Support

- Server artifact version and framework (QBCore / Qbox / ESX / standalone)
- Inventory resource and whether the ox bridge module is installed
- Your `data/weapon_slots.json`
- The full server console output from resource start
- Any F8 client console errors
