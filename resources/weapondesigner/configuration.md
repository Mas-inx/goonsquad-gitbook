# Configuration

Configuration is split across a small number of files:

| File | Purpose | Client-readable |
|---|---|---|
| `shared/config.lua` | Main configuration (`GSWD.Config`) | Yes |
| `shared/tebex.lua` | Designer pass packages — see [Tebex Integration](tebex.md) | Yes |
| `server/credentials.lua` | Webhook URLs and API keys | No |
| `data/weapon_slots.json` | The slot system — how many designs each weapon supports | No |

{% hint style="danger" %}
Only `server/credentials.lua` is safe for secrets. Never place webhook URLs or API keys in `shared/` files — they are downloaded by every client.
{% endhint %}

---

## General

| Option | Default | Description |
|---|---|---|
| `Debug` | `true` | Verbose console logging. Disable in production. |
| `OpenCommand` | `'gswd'` | Chat command that opens the studio (`/gswd`) |
| `OpenCommandEnabled` | `true` | Set `false` to remove the command entirely (stations/exports only) |
| `OpenKeyMapping` | `nil` | Set to a key (e.g. `'F7'`) to register a rebindable keybind for the open command |
| `AllowFileUploads` | `true` | Allow players to upload local image files onto the canvas |
| `AllowAssetUrlImports` | `true` | Allow players to import images from HTTPS URLs |

---

## Access Control

### Job Gating

```lua
Designer = {
    RequireJob = false,
    jobs = {},
}
```

| Setting | Description |
|---|---|
| `RequireJob` | When `true`, only the jobs below may use the designer. The check runs on every server action, not just when opening the UI. |
| `jobs` | Map of allowed jobs to minimum grade, e.g. `{ ['gunsmith'] = 0 }`. |

### Designer Stations

Stations are optional in-world points where players can press E to open the studio:

```lua
Designer = {
    stations = {
        {
            label = 'Weapon Studio',
            coords = vec3(18.5, -1105.1, 29.8),
            marker = vec3(1.2, 1.2, 0.5),
            interactDistance = 1.7,
            drawDistance = 20.0,
        },
    },
}
```

{% hint style="info" %}
Stations combine well with `OpenCommandEnabled = false` if you want the designer to only be reachable at physical locations.
{% endhint %}

---

## The Slot System (data/weapon_slots.json)

Every published design occupies one **slot**. A slot is a complete pre-built addon weapon — its own model, texture, attachments, and inventory item — reserved for exactly one design at a time. Because slots are real streamed weapons, custom skins work with no client mods and never conflict with each other or with vanilla weapons.

How many slots exist is controlled by one JSON file: `data/weapon_slots.json`.

```json
{
  "schemaVersion": 1,
  "maxTotalDesigns": 200,
  "defaultSlotsPerWeapon": 1,
  "weapons": {
    "assault_rifle": 3,
    "carbine_rifle": 3,
    "combat_pistol": 3,
    "pistol": 1,
    "pump_shotgun": 3,
    "sniper_rifle": 1,
    "knife": 1
  }
}
```

### Fields

| Field | Type | Description |
|---|---|---|
| `schemaVersion` | number | File format version. Leave at `1`. |
| `maxTotalDesigns` | number | Server-wide ceiling on published designs. Once this many slots are occupied, new forges are refused until a design is deleted. The sum of all per-weapon slot counts may not exceed it. |
| `defaultSlotsPerWeapon` | number | Slot count automatically assigned to newly added weapon templates. |
| `weapons` | object | One entry per weapon template: the key is the folder name under `weapon_templates/`, the value is how many designs of that weapon can be published at the same time. |

### How it behaves

- **The file manages itself.** It is created on first start, and on every start it is re-synced against `weapon_templates/`: your existing counts are preserved, newly discovered templates are added with `defaultSlotsPerWeapon`, and templates you removed are pruned.
- **Values are clamped.** Counts are whole numbers between 1 and 2048. A weapon cannot be set to 0 — remove its template folder instead if you do not want it designable.
- **Slots become available after a restart.** The resource builds the streamed files for each slot when it starts, but FiveM only mounts new streamed files on a resource restart. After changing counts, restart the resource; if the console reports that `fxmanifest.lua` was updated, restart once more.
- **Occupied slots are never deleted.** Lowering a count removes only empty slots. A slot holding a published design keeps working until the design is deleted, at which point the slot is freed or retired.
- **Invalid JSON stops the resource, safely.** If the file cannot be parsed, the resource refuses to start rather than overwrite your edits. Fix the syntax and start again.

### Editing the file

1. Stop the resource (or the server).
2. Edit the counts in `data/weapon_slots.json`. For example, to allow five simultaneous assault rifle designs:
   ```json
   "assault_rifle": 5
   ```
3. Start the resource. Watch the console — if it reports `fxmanifest.lua` was updated, restart the resource once more.
4. Confirm with the `gswd_pool_status` console command, which prints per-weapon slot occupancy.

{% hint style="warning" %}
Each slot streams a full copy of the weapon's models and attachments. Keep counts low for weapons players rarely skin and raise them only where demand exists — a server-wide total in the low hundreds is a sensible ceiling. If you configure more slots than `maxTotalDesigns` allows, the resource reports an error at boot instead of starting with a partial pool.
{% endhint %}

{% hint style="info" %}
Slot weapons appear in-game with numbered names such as `WEAPON_ASSAULTRIFLE_GSWD_00`. Players never interact with these names directly — the inventory shows the design's own label — but you will see them in `gswd_pool_status` output and in the generated item catalogue.
{% endhint %}

### Pool auto-expansion

```lua
Pool = {
    autoExpand = false,
    expansionStep = 1,
    maxCapacity = 16,
    minimumFreeSlots = 0,
}
```

| Setting | Description |
|---|---|
| `autoExpand` | When `true`, the resource grows a weapon's slot count on its own as slots fill up. Keep `false` when you manage `weapon_slots.json` by hand. |
| `expansionStep` | Slots added per expansion. |
| `maxCapacity` | Per-weapon ceiling for auto-expansion. |
| `minimumFreeSlots` | Expansion triggers when free slots for a weapon drop below this. |

{% hint style="info" %}
Auto-expanded slots still require a resource restart before they can be used, so `autoExpand` is best treated as a convenience for gradually growing servers rather than an instant capacity fix.
{% endhint %}

---

## Rendering

| Option | Default | Description |
|---|---|---|
| `RenderSystem` | `'runtime'` | `'runtime'` paints designs onto weapons live and is the safe, recommended mode. `'hybrid'` additionally bakes the design into the slot's texture file. |

{% hint style="danger" %}
Hybrid mode is **experimental** and can cause client crashes. Baked results only appear after a server restart. Use `'runtime'` unless you have a specific reason not to and have tested hybrid on your build.
{% endhint %}

The `Runtime` table tunes how skins are drawn for nearby players. The defaults are sensible for most servers:

| Option | Default | Description |
|---|---|---|
| `Runtime.renderDistance` | `100.0` | Distance within which other players' skins are drawn |
| `Runtime.prepareDistance` | `200.0` | Distance at which skin images are pre-fetched |
| `Runtime.scanInterval` | `750` | Milliseconds between scans for nearby skinned weapons |
| `Runtime.AllowAnimatedGifs` | `true` | Allow animated GIF designs |

---

## Inventory Integration

| Option | Default | Description |
|---|---|---|
| `InventoryItemName` | `'gs_customweapon'` | Ticket item used on non-ox inventories |
| `InventoryItemLabel` | `'Custom Weapon Skin'` | Display label for the ticket item |
| `OxBridge.enabled` | `true` | Enable the ox_inventory bridge integration |
| `OxBridge.useOxWeaponItems` | `true` | Grant forged designs as real ox weapon items with working attachments |

{% hint style="warning" %}
The ox integration requires the bridge module from the `install/` folder to be installed inside `ox_inventory` — see [Installation](installation.md#ox_inventory-bridge-module).
{% endhint %}

---

## Media Uploads

Design preview images (shown on inventory items and in the armory) are hosted by a configurable provider:

| Option | Default | Description |
|---|---|---|
| `AssetUploadProvider` | `'discord'` | `'database'`, `'fivemanage'`, or `'discord'` |
| `MaxUploadBytes` | 4 MiB | Maximum size for player-uploaded images |
| `MaxPreviewBytes` | 4 MiB | Maximum size for generated preview images |

- **`database`** stores images in MySQL. No credentials needed; simplest option.
- **`fivemanage`** uploads to FiveManage. Set `FiveManageApiKey` in `server/credentials.lua`.
- **`discord`** uploads to a Discord channel webhook. Set `DiscordWebhookUrl` in `server/credentials.lua`.

{% hint style="warning" %}
When using `ox_inventory` with the `discord` or `fivemanage` providers, add the image host to ox's `inventory:validhosts` convar so item preview images are allowed to load.
{% endhint %}

### Upload Queue

```lua
Queues = {
    Upload = {
        Enabled = true,
        MaxConcurrent = 2,
        MaxPending = 12,
        BusyMessage = 'The media upload queue is busy. Please try again in a moment.',
    },
}
```

The upload queue prevents many simultaneous image operations from overwhelming the server or the media provider.

| Setting | Description |
|---|---|
| `Enabled` | Set `false` to execute media jobs immediately. Keeping it enabled is recommended. |
| `MaxConcurrent` | Media jobs allowed to run at the same time. |
| `MaxPending` | Jobs allowed to wait in the queue before players see the busy message. |

---

## Armory and Approval

| Option | Default | Description |
|---|---|---|
| `Armory.Enabled` | `true` | Enable the player design library |
| `Armory.Command` | `'gswd_armory'` | Command that opens the armory |
| `Armory.RequireApproval` | `false` | When `true`, forged designs are held for staff review and grant no weapon until approved |
| `Armory.AdminCommand` | `'gswd_weapon_review'` | Staff review command |
| `Armory.AdminAce` | `'gswd.weapondesigner.admin'` | ACE permission required for review |

Grant the admin ACE in `server.cfg`:

```cfg
add_ace group.admin gswd.weapondesigner.admin allow
```

See [Usage](usage.md#staff-approval) for the review flow.

---

## Design Payload Limits

The `LayerValidation` table bounds what the server accepts from the design canvas. The defaults (64 layers, 2 MiB design data, 4096 px maximum dimension, and related limits) protect the server from oversized payloads and rarely need changing.

{% hint style="info" %}
`Config.ExportPacks` (default `false`) lets the forge screen also write a standalone resource pack to the `output/` folder. This is a development tool — leave it disabled on live servers.
{% endhint %}
