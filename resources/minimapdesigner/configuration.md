# Configuration

## File Layout

| File | Purpose | Client-readable |
|---|---|---|
| `config/config.lua` | Command, ACE, storage, media selection, radar and postal settings | Yes |
| `config/server.lua` | Admin identifier allowlist | No |
| `server/credentials.lua` | Hosted media credentials | No |

These files stay editable under Asset Escrow. Integration files and data are also excluded:

| File or path | Purpose |
|---|---|
| `server/storage.lua` | Storage adapter and persistence |
| `server/media.lua` | Media provider selection and upload bridge |
| `client/postals.lua` | Postal exports, waypoint command and HUD readout |
| `data/**` | Saved JSON designs, active pointer and thumbnails |
| `custom_maps/**` | User-supplied map packs |

The companion's `config.lua` has its own escrow exclusion. Other core Lua scripts remain eligible for escrow protection. JavaScript and NUI files are outside the currently supported [Cfx Asset Escrow formats](https://docs.fivem.net/docs/server-manual/asset-escrow/).

---

## General Settings

The following values describe the clean release package:

```lua
Config.Command = 'minimapdesigner'
Config.AcePermission = 'minimapdesigner.edit'
Config.DefaultDesign = 'default'
Config.SyncOnJoin = true
Config.Debug = false
```

| Setting | Behaviour |
|---|---|
| `Command` | In-game command that opens the designer |
| `AcePermission` | ACE that grants editor access; `false` disables this access path |
| `DefaultDesign` | Fallback sheet name when there is no valid active pointer |
| `SyncOnJoin` | Requests the published sheet on `playerSpawned`; resource start also requests it |
| `Debug` | Additional server diagnostic output |

`data/active.json` stores the published sheet name. It takes precedence over `DefaultDesign`. Use the editor's Publish action to switch the live sheet.

---

## Access

`config/server.lua` contains `Config.Admins`, an empty array in the release. Add complete player identifiers as strings. Matching is case-insensitive, and a match on any listed identifier grants access.

ACE access and identifier access are alternatives: a player only needs one. There is no built-in job, grade, inventory or paid-pass requirement.

Restart the resource after changing access configuration so the server refreshes its identifier lookup.

---

## Storage

```lua
Config.Storage = 'mysql'
Config.StorageTable = 'gs_minimap_designs'
```

| Backend | Design data | Thumbnails |
|---|---|---|
| `mysql` | `gs_minimap_designs` table | `gs_minimap_designs_thumbs` table |
| `json` | `data/<name>.json` | `data/thumbs/<name>.txt` |

Changing `StorageTable` also changes the thumbnail table prefix. Use a simple database identifier for the table name.

JSON storage maintains `data/designs_index.json` for the catalogue. Both backends use `data/active.json` for the published pointer. The resource can fall back to a same-name JSON design when MySQL has no matching row; this is not a complete backend migration.

Back up the database tables and the entire `data/` directory. Preserve them when updating an installed server.

---

## Media Storage

```lua
Config.MediaProvider = 'database'
```

| Provider | Credential in `server/credentials.lua` | Behaviour |
|---|---|---|
| `database` | None | Stores image data inline in the design, using the selected storage backend |
| `discord` | `Credentials.DiscordWebhookUrl` | Uploads large embedded design images to a webhook |
| `fivemanage` | `Credentials.FiveManageApiKey` | Uploads large embedded design images to file storage |

Example credentials structure:

```lua
Credentials = {
    FiveManageApiKey = '',
    DiscordWebhookUrl = '',
}
```

Fill only the field required by the selected provider. Saving or publishing offloads embedded image data URLs larger than 100 KiB. Missing credentials or failed uploads leave images inline rather than removing them.

{% hint style="warning" %}
Hosted images must remain accessible to players. Backing up the design JSON preserves a hosted URL, not the image behind that URL.
{% endhint %}

---

## Radar and Postal HUD

```lua
Config.TilesOnRadar = true
Config.FlatRadar = true
Config.PostalHud = true
Config.PostalHudPos = { x = 0.175, y = 0.955 }
```

| Setting | Behaviour |
|---|---|
| `TilesOnRadar` | Shows baked tiles on the small radar as well as the pause map; `false` limits them to the pause map |
| `FlatRadar` | Keeps the radar flat while live tiles are displayed |
| `PostalHud` | Shows the postal readout when the applied design has postals enabled |
| `PostalHudPos` | Normalized screen coordinates for the readout |

The resource does not set radar component positions or masks. If another HUD already displays postals, disable `PostalHud` and use the [postal exports](exports.md#client-exports).

Postal grid bounds, numbering, label styling and map colours are saved per sheet through the editor, not in the configuration file.
