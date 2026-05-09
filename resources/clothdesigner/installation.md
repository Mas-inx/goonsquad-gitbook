# Installation

{% embed url="https://www.youtube.com/watch?v=BOuWPZYtwQw" %}

> Prefer reading? The full step-by-step is below.

---

## Step 1: Add to Server

1. Drop the `gs-clothdesigner` folder into your server's `resources/` directory.
2. Add to `server.cfg`, after `oxmysql` and your framework:

   ```
   ensure oxmysql
   ensure qb-core      # or qbx_core / es_extended
   ensure ox_inventory # if you use ox_inventory
   ensure gs-clothdesigner
   ```

   `gs-clothdesigner` **must** start after `oxmysql` and your framework.

## Step 2: Database Setup

The resource auto-creates and migrates every table it needs at boot — no manual import required. Just make sure `oxmysql` is reachable.

If you'd rather pre-create the schema by hand, run the bundled SQL file:

```bash
mysql -u YOUR_USER -p YOUR_DB < resources/gs-clothdesigner/sql/schema.sql
```

## Step 3: Add the Inventory Item

ClothDesigner ships ready-to-paste item definitions in the `install/` folder.

### QBCore — `qb-core/shared/items.lua`

```lua
['gs_customshirt'] = {
    name = 'gs_customshirt',
    label = 'Custom T-Shirt',
    weight = 250,
    type = 'item',
    image = 'gs_customshirt.png',
    unique = true,
    useable = true,
    shouldClose = true,
    combinable = nil,
    description = 'A shirt printed in the clothing design studio.'
}
```

### Qbox / ox_inventory — `ox_inventory/data/items.lua`

```lua
['gs_customshirt'] = {
    label = 'Custom T-Shirt',
    weight = 250,
    stack = false,
    close = true,
    description = 'A shirt printed in the clothing design studio.'
}
```

> When `ox_inventory` is the active backend, ClothDesigner attaches the design's preview image to the item, so each printed shirt shows its own design as the inventory thumbnail. No PNGs need to be added to `ox_inventory/web/images/`.

### ESX — database insert

```sql
INSERT IGNORE INTO `items` (`name`, `label`, `weight`) VALUES
('gs_customshirt', 'Custom T-Shirt', 1);
```

> The item name is configurable via `Config.InventoryItemName`.

## Step 4: Configure Credentials

Open `server/credentials.lua`. This file is server-only and is where API keys and webhook URLs live.

```lua
GSCD = GSCD or {}
GSCD.Credentials = GSCD.Credentials or {}

GSCD.Credentials.GoogleApiKey = ''        -- only required if AI Mode is enabled
GSCD.Credentials.DiscordWebhookUrl = ''   -- recommended; required for AI Mode
```

| Credential | Required for |
|------------|--------------|
| `DiscordWebhookUrl` | AI image hosting and large user uploads. Without it, AI Mode fails and large image uploads may be rejected. |
| `GoogleApiKey` | AI Mode only. Get a free key at [aistudio.google.com](https://aistudio.google.com/apikey). Set `Config.AI.enabled = false` if you don't want AI Mode. |

> **Important:** `server/credentials.lua` is loaded server-side only. Treat the values like any other secret — do not commit a real key to a public git repo.

## Step 5: Configure Designer Stations & Jobs

In `shared/config.lua`:

```lua
Designer = {
    jobs = {
        clothingdesigner = 0,    -- job name -> minimum grade
        ambulance = 0,
    },
    stations = {
        {
            label = 'Clothing Designer Station',
            coords = vec3(-1194.96, -767.87, 17.32),
            marker = vec3(1.4, 1.4, 0.6),
            interactDistance = 1.7,
            drawDistance = 20.0,
        }
    },
}
```

Players whose framework job + grade match an entry in `Designer.jobs` can walk up to any station and press **E** to open the studio.

To make the studio public, add an `unemployed = 0` entry (or any other catch-all job) — or call the export from your own resource:

```lua
exports['gs-clothdesigner']:openDesigner()
```

## Step 6: Restart the Server

The first start will automatically discover every `.ydd` in `cloth_templates/` and prepare your apparel pool. You'll see a console banner like this:

```
==========================================================
[gs-clothdesigner] New packs were materialised.
Restart the server for the apparel to take effect.
(Subsequent restarts will be instant — no action needed.)
==========================================================
```

Restart **once more**. From that point on, boots are instant and no further action is needed unless you add new `.ydd` files.

---

## Adding More Cloth Templates

1. Drop your new `.ydd` files into `cloth_templates/<gender>/<category>/` following the existing naming pattern.
2. **Restart the server.**

That's it. The resource auto-detects new templates on boot, prepares the apparel for them, and you're ready to design.

> **Recommended:** just restarting the server is the easiest, most reliable way to add new templates. Two short restarts (one to materialise the apparel, one for FiveM to register it) handle everything end-to-end, the same as the very first install.
>
> *(Advanced)* If you'd rather not restart immediately, you can run `gscd_rescan` followed by `gscd_rebuild_pool` from the server console. You'll still need a server restart at the end for the new apparel to register — restarting up front is simpler.

---

## Framework-Specific Setup

### QBCore

1. Add `gs_customshirt` to `qb-core/shared/items.lua` (see Step 3).
2. Ensure `qb-core` and `oxmysql` start before `gs-clothdesigner`.
3. (Optional) If you want `ox_inventory`-style item images, also start `ox_inventory` and use the ox_inventory definition instead.

### Qbox (qbx_core + ox_inventory)

1. Add `gs_customshirt` to `ox_inventory/data/items.lua` (see Step 3).
2. Ensure `qbx_core`, `oxmysql`, and `ox_inventory` start before `gs-clothdesigner`.

### ESX

1. Insert `gs_customshirt` into the `items` table (see Step 3).
2. Ensure `es_extended` and `oxmysql` start before `gs-clothdesigner`.

---

## Permissions (Optional)

By default the admin commands (`gscd_rescan`, `gscd_rebuild_pool`, etc.) are server-console / RCON only. To expose them in-game, add ACE permissions:

```
add_ace group.admin command.gscd_rescan allow
add_ace group.admin command.gscd_rebuild_pool allow
add_ace group.admin command.gscd_fill_slots allow
add_ace group.admin command.gscd_delete_design allow
```

---

## Troubleshooting

**No silhouettes show up in the studio**
- Confirm `cloth_templates/` actually contains `.ydd` files at the expected paths (e.g. `cloth_templates/male/tops/jbib_000_u.ydd`).
- Run `gscd_rescan` from the server console.

**Designs don't apply / I see a yellow placeholder texture**
- The first ever boot needs **two** restarts — one to materialise the apparel, one for FiveM to register it.
- Check that `gs-clothdesigner` starts after `oxmysql` in `server.cfg`.

**`Serialization of the -92836328 packet failed` when uploading or AI-generating**
- This is FiveM's NUI packet size limit. Configure a `DiscordWebhookUrl` in `server/credentials.lua` and the limit goes away.

**AI Mode shows "disabled" or "not configured"**
- Set `Config.AI.enabled = true` in `shared/config.lua`.
- Configure `GSCD.Credentials.GoogleApiKey` in `server/credentials.lua`.
- Configure `GSCD.Credentials.DiscordWebhookUrl` (required to host the result).

**A silhouette card shows "Pack full"**
- The next server restart will auto-expand and clear the lock automatically.
- To trigger expansion immediately, run `gscd_rebuild_pool` and then restart the server.

**Custom textures don't reapply on rejoin**
- Restart the resource and try again — the persistence layer is created automatically on first boot.
- Confirm your framework's player-loaded event is firing normally for other systems on your server.

**`oxmysql exports are not available`**
- Move `ensure oxmysql` above `ensure gs-clothdesigner` in `server.cfg`.
