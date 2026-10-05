# Integrations

OnlyShots detects its adapters when it starts. Start integrations first and restart `gs-onlyshots` after replacing a framework, inventory, target or database adapter. The files listed in [Editable Files](configuration.md#editable-files) are available for customization under Asset Escrow.

## Framework and Identity

| `Config.Framework` | Detected resource | Saved identity |
|---|---|---|
| `'qbox'` | `qbx_core` | Character `citizenid` |
| `'qb'` | `qb-core` | Character `citizenid` |
| `'esx'` | `es_extended` | ESX player identifier |
| `'standalone'` | No framework | First available license, license2, Steam or Discord identifier |

`'auto'` tries these in the order shown. Qbox uses its player and usable-item exports, with QB compatibility helpers where available. Framework character names become creator display names.

For a custom framework, adjust detection in `shared/bridge.lua` and the identity, name, wallet, usable-item and access functions in `server/bridge.lua`. Client notification behavior lives in `client/bridge.lua`. Keep saved identifiers stable across connections and character switches; changing identity formats does not migrate saved progress.

`Config.MoneyAccount` controls camera purchases, NPC payments, trade payments and rewards. QB/Qbox use the named account. ESX uses its bank account for `'bank'` and cash otherwise. Standalone uses the saved internal wallet, initially zero.

---

## Inventory

`'auto'` checks `ox_inventory`, `qb-inventory`, `qs-inventory`, `lj-inventory`, then uses `'internal'`.

| Adapter | Item data | Registration |
|---|---|---|
| `ox_inventory` | `metadata` with slot-specific removal | Supplied `server.export` item definitions |
| `qb-inventory` | QB player items with `info` | Framework usable items; inventory exports with player-function fallback |
| `lj-inventory` | QB player items with `info` | Framework usable items and player item functions |
| `qs-inventory` | Inventory exports with `info` | Supplied unique definitions and framework usable items |
| `'internal'` | OnlyShots' item store | No inventory registration; app portfolio and `/camera` |

The QB and LJ adapters expect QB-compatible player data. The resource does not have a dedicated default-ESX inventory adapter or a `ps-inventory` adapter. A similar item-definition format alone does not establish compatibility.

See [Installation](installation.md#step-2-register-the-items) for definitions and image destinations.

### Custom Inventories

Adapt `server/inventory.lua` while preserving its interface:

| Function | Contract |
|---|---|
| `Inv.GetItems(src)` | Return entries shaped as `{ slot, name, count, metadata }` |
| `Inv.CanCarry(src, name)` | Check room for one new item |
| `Inv.AddItem(src, name, metadata)` | Add one item with metadata intact; return success/failure |
| `Inv.RemoveItem(src, entry)` | Remove exactly one item from the specified slot; return success/failure |
| `Inv.HasCamera(src)` | Report actual camera ownership |

Wire usable items through `Bridge.RegisterUsableItem` in `server/bridge.lua`, or through your inventory's equivalent of the item hooks in [API & Exports](exports.md#inventory-item-hooks).

Photo metadata includes `id`, original `owner`, `ownerName`, `timestamp`, `caption`, `url`, `ptype`, `pose`, `value`, `location` and `locMult`, with item `label` and `description`. Hosted photos may have `imageurl` for tooltips. Collections include `count` and a `photos` array with each photo's ID, image reference, caption, type and pose.

Trades add `collector` and `collectorName` while retaining `owner`. Preserve all fields on inventory save, load and transfer. Selling, bundling and trading use these identities and IDs; a plain item name without metadata is insufficient.

### Target, Notifications and Progress

The client bridge supports local entity interaction with `ox_target` or `qb-target`; otherwise boutique and thief-body interactions use E prompts. Set `Config.Target = 'none'` to force prompts.

Notifications prefer `ox_lib`, framework notification helpers and then OnlyShots toasts. Progress prefers available `ox_lib` helpers and otherwise uses the built-in bar. Customize `client/bridge.lua` to add your own adapters.

---

## Persistence

The open `server/db.lua` adapter prefers `oxmysql`, then `mysql-async`. Queries use positional parameters; the latter adapter converts them to named parameters internally.

| Table | Stored data |
|---|---|
| `gs_onlypics_stats` | Character name, XP/rank, counters, followers, discoveries, challenges, achievements, internal wallet and camera flag |
| `gs_onlypics_items` | Photos and collections when using the internal inventory |
| `gs_onlypics_shots` | Image data for SQL image storage |

Stats and internal-item tables are created on startup, and the image table is created when SQL image storage is selected. An existing stats table is checked for its `is_female` column. Gender flags can be backfilled from standard QB/Qbox or ESX character tables; current online models still determine runtime access.

Without a started database adapter, JSON mode uses `data/store.json` for stats and internal items. External inventory items remain in that inventory's own persistence. SQL-mode images held without a database are memory-only.

Back up both OnlyShots storage and inventory metadata. The resource does not automatically import JSON into SQL, migrate between inventories or migrate character identifiers. Keep the existing `gs_onlypics_` table names; they are the names used by the release.

---

## Image Storage

The camera requests a JPEG from `Config.Upload.resource`, the UI resizes it, and a latent event transfers it to the server. The server returns a pending image reference and commits it when the photo is saved. Capture providers must expose the `requestScreenshot(options, callback)` contract used by `screenshot-basic`; set the resource name in `Config.Upload.resource`.

### SQL

Set `Config.Upload.storage = 'sql'` and start a supported database adapter. No hosting credentials are needed. Metadata contains `shot:<id>`; the app requests and caches image data by ID. Inventory tooltip image URLs are not generated for these references.

Successful NPC sales and expired thefts delete the item image data, including the images inside a collection. Player purchases keep image references available. Without a database, this mode keeps image data only until the next restart.

### Fivemanage

```lua
Config.Upload.storage = 'fivemanage'
```

In `server/sv_config.lua`:

```lua
ServerConfig.Upload.fivemanage = {
    token = 'YOUR_API_KEY',
    url = 'https://api.fivemanage.com/api/v3/file/base64',
}
```

OnlyShots posts JSON containing a base64 data URL, filename and metadata to the v3 endpoint. The server accepts a returned HTTPS image URL from `data.url` or `url`. Credentials are never included in client configuration.

### Discord Image Hosting

```lua
Config.Upload.storage = 'discord'
```

In `server/sv_config.lua`:

```lua
ServerConfig.Upload.discord = {
    webhook = 'YOUR_WEBHOOK_URL',
}
```

The server uploads an attachment through `server/upload.js` and saves its HTTPS attachment URL. The host controls link availability; OnlyShots does not refresh an expired attachment URL. Hosted files are not deleted by the resource when sold or lost.

### Custom Image Hosts

Set `Config.Upload.storage = 'custom'`, then add a host configuration on the server:

```lua
ServerConfig.Upload.custom = {
    url = 'https://images.example.com/upload',
    token = 'YOUR_TOKEN',
    field = 'file',
}
```

The uploader sends an HTTPS multipart POST with the JPEG under the configured `field`. A non-empty token becomes `Authorization: Bearer <token>`. The host must return a successful HTTP status and JSON containing an HTTPS image URL in `url`, `link` or `data.url`.

This adapter does not expose arbitrary custom headers or extra form fields through `ServerConfig.Upload.custom`. A host needing a different authentication/response contract requires a compatible proxy or additional integration work. `uploadFile` is an internal transport helper, not a gameplay API.

### Placeholders

Set `Config.Upload.storage = 'none'` to skip real image storage. Captions, prices, sales and progression continue to work with bundled artwork.

---

## Discord Access and Activity Feed

Enable `Config.Gate.discordRole` and set `ServerConfig.Discord.guildId`, `roleId` and `botToken`. The bridge reads the player's Discord identifier and checks the configured guild member's roles. Ensure the bot can perform that lookup. The default cache is ten minutes; role changes may take that long to affect creator access.

The saved-photo feed uses separate `ServerConfig.Webhook` settings. Enable it and supply its URL. The feed includes owner, caption, type, pose and hotspot; only hosted HTTPS photos have an attached image. Selecting Discord image storage alone does not enable the activity feed, and enabling the feed does not configure image hosting.
