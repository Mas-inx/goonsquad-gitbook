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

   `gs-clothdesigner` **must** start after `oxmysql` and your framework. The Node-side discovery + pool allocator wait for `oxmysql` to be ready before they touch the DB.

## Step 2: Database Setup

The resource auto-creates and migrates every table it needs at boot, but it expects `oxmysql` to be reachable. You can also manually pre-create the schema:

```bash
mysql -u YOUR_USER -p YOUR_DB < resources/gs-clothdesigner/sql/schema.sql
```

This creates the following tables:

| Table | Purpose |
|-------|---------|
| `gs_clothdesigner_designs` | Saved designs (layers JSON + preview image + slot metadata) |
| `gs_clothdesigner_design_versions` | Historical preview snapshots per design |
| `gs_clothdesigner_assets` | Player-uploaded images and imported asset URLs |
| `gs_clothdesigner_silhouettes` | Discovered cloth templates (one row per `.ydd`) |
| `gs_clothdesigner_silhouette_batches` | Synthetic bridge rows linking sessions to pool packs |
| `gs_clothdesigner_silhouette_sessions` | In-progress design sessions (ephemeral, wiped on boot) |
| `gs_clothdesigner_pool_packs` | Multi-drawable apparel packs (one row per generated MP DLC pack) |
| `gs_clothdesigner_pack_drawables` | Per-silhouette drawable assignments inside a pack + texture-slot bitmap |
| `gs_clothdesigner_active_wearables` | Persists each player's equipped designs across disconnects |
| `gs_clothdesigner_audit` | Audit log for create / publish / delete actions |

> The Node bundle in `dist/server/silhouettes.js` also runs its own idempotent `CREATE TABLE IF NOT EXISTS` + `ALTER TABLE` passes when it boots, so legacy installs from older versions are migrated automatically.

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

> When `ox_inventory` is the active backend, ClothDesigner attaches the design's preview image to the item's `imageurl` metadata, so each printed shirt shows its own design as the inventory thumbnail. No PNGs need to be added to `ox_inventory/web/images/`.

### ESX — database insert

```sql
INSERT IGNORE INTO `items` (`name`, `label`, `weight`) VALUES
('gs_customshirt', 'Custom T-Shirt', 1);
```

> The item name itself is configurable via `Config.InventoryItemName`. If you change it, update the `useable` registration to match.

## Step 4: Configure Credentials

Open `server/credentials.lua`. This file is **not** in the shared config because the secrets in it must never reach the client.

```lua
GSCD = GSCD or {}
GSCD.Credentials = GSCD.Credentials or {}

GSCD.Credentials.GoogleApiKey = ''        -- required only if AI Mode is enabled
GSCD.Credentials.DiscordWebhookUrl = ''   -- required for image hosting / AI output
```

| Credential | Required for |
|------------|--------------|
| `DiscordWebhookUrl` | AI image hosting + large user uploads. Without it, AI Mode fails and large image uploads (>~12 KB base64) fall back to inline DB storage. |
| `GoogleApiKey` | AI Mode only. Get a free key at [aistudio.google.com](https://aistudio.google.com/apikey). Disable AI in `shared/config.lua` if you don't want it. |

> **Important:** `server/credentials.lua` is loaded server-side only. Treat it like any other secret — do not commit a real key to a public git repo.

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

The first start will:

1. Discover every `.ydd` in `cloth_templates/` and write `data/manifest.json`
2. Pre-allocate one drawable slot per silhouette across multi-drawable apparel packs
3. Materialise every pack to `generated/` and `stream/generated/` inside the resource
4. Append the new pack list to `fxmanifest.lua` (between the `[GENERATED APPAREL START/END]` markers)

You'll see a console banner like this on the **first** boot:

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

Drop more `.ydd` files into `cloth_templates/<gender>/<category>/` following the existing naming pattern, then run `gscd_rescan` (server console / RCON):

```
gscd_rescan
```

This re-scans the disk, upserts new silhouettes into the database, allocates fresh drawable slots, and writes any new packs to disk. Restart the server **once** for the new packs to take effect, then everything works without further restarts.

`gscd_rebuild_pool` does the same thing, but also re-materialises packs whose on-disk files are missing or out of sync.

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

## Permissions

ClothDesigner only writes inside its own resource folder (`generated/`, `stream/generated/`, `data/`, `fxmanifest.lua`). FiveM grants resources unrestricted file access to their own directory by default — no `add_filesystem_permission` lines are required.

If you want to gate the rescan / rebuild commands behind ACE permissions:

```
add_ace group.admin command.gscd_rescan allow
add_ace group.admin command.gscd_rebuild_pool allow
add_ace group.admin command.gscd_fill_slots allow
add_ace group.admin command.gscd_delete_design allow
```

By default these commands are server-console / RCON only.

---

## Troubleshooting

**Pool not provisioned / no silhouettes loaded**
- Confirm `cloth_templates/` actually contains `.ydd` files at the expected paths (e.g. `cloth_templates/male/tops/jbib_000_u.ydd`).
- Run `gscd_rescan` from the server console and watch for `[gscd][discovery]` log lines.
- If `[gscd][pool] rebuild failed` appears, check the error message — it's almost always a database connection issue.

**Designs don't apply / I see the yellow placeholder texture**
- The first ever boot needs **two** restarts — one to materialise the packs, one for FiveM to register them as MP DLC apparel.
- Check that `gs-clothdesigner` is starting after `oxmysql` in `server.cfg`.
- Use `/gscd_test_reload` (client console) on a player wearing a custom design to confirm the variation collection is recognised.

**`Serialization of the -92836328 packet failed` when uploading or AI-generating**
- This is FiveM's NUI packet size limit. Configure a `DiscordWebhookUrl` in `server/credentials.lua` — uploads switch to chunked Discord-CDN hosting and the limit goes away.

**AI Mode not enabled / disabled message in the UI**
- Set `Config.AI.enabled = true` in `shared/config.lua`.
- Configure `GSCD.Credentials.GoogleApiKey` in `server/credentials.lua`.
- Configure `GSCD.Credentials.DiscordWebhookUrl` (required to host the result).

**Pack-full lock overlay won't go away**
- A silhouette is locked when every drawable assignment for it has used all 26 texture variants. The next server restart will auto-expand by allocating a new pack with a fresh drawable slot for that silhouette.
- Run `gscd_rebuild_pool` to trigger expansion immediately (it will print how many packs were added and prompt for a final restart).

**Custom textures don't reapply on rejoin**
- Verify the `gs_clothdesigner_active_wearables` table exists. The schema migration runs automatically — restart the resource if the table is missing.
- Confirm your framework's `playerLoaded` event fires (`QBCore:Server:OnPlayerLoaded`, `qbx_core:playerLoaded`, or `esx:playerLoaded`).

**`oxmysql exports are not available`**
- The Node side runs before `oxmysql` is ready. Move `ensure oxmysql` above `ensure gs-clothdesigner` in `server.cfg`.
