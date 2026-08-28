# Installation

## Step 1: Add to Server

1. Download the `gs-weapondesigner` folder from [Tebex](https://store.goonsquadstudios.com/package/7644789) and place it in your server's `resources` directory.
2. Add to `server.cfg`:
   ```
   ensure oxmysql
   ensure gs-weapondesigner
   ```
   `oxmysql` is required and must start **before** `gs-weapondesigner`.

## Step 2: Inventory Item Setup

Copy the item definition matching your inventory from the resource's `install/` folder.

| Inventory | File | Where it goes |
|---|---|---|
| `ox_inventory` | `install/ox_inventory.lua` | Paste into `ox_inventory/data/items.lua` |
| `ox_inventory` (bridge) | `install/ox_inventory_gswd_bridge.lua` | Copy to `ox_inventory/modules/gswd_items/bridge.lua` — see below |
| QBCore / Qbox | `install/qbcore.lua` | Paste into your shared items file |
| Jaksam Inventory | `install/jaksam_inventory.lua` | Paste into the inventory's items file |
| CodeM Inventory | `install/codem_inventory.lua` | Paste into the inventory's items file |
| Quasar (`qs-inventory`) | `install/qs_inventory.lua` | Paste into the inventory's items file |
| AK47 Inventory | `install/ak47_inventory.lua` | Paste into the inventory's items file |
| ESX (database items) | `install/esx.sql` | Import into your database |

### ox_inventory bridge module

The bridge module is **required** when using `ox_inventory`. It lets GS Weapon Designer register its generated weapon and component items directly into ox's item list, so forged designs become real weapon items with working attachments.

1. Copy `install/ox_inventory_gswd_bridge.lua` to `ox_inventory/modules/gswd_items/bridge.lua`.
2. Add the module to `ox_inventory/fxmanifest.lua` in **both** script lists:
   ```lua
   server_scripts {
       -- existing entries ...
       'modules/gswd_items/bridge.lua',
   }

   client_scripts {
       -- existing entries ...
       'modules/gswd_items/bridge.lua',
   }
   ```
3. Restart `ox_inventory`.

{% hint style="warning" %}
All non-ox item definitions reference an item image named `gs_customweapon.png`. Add an image with that name to your inventory's images folder or the ticket item will show without an icon.
{% endhint %}

## Step 3: Database Setup (Optional)

The resource **auto-creates its tables on first boot**, so no manual import is needed. A reference copy of the schema ships in `sql/schema.sql` if you prefer to import it yourself.

The following tables are created:

| Table | Purpose |
|---|---|
| `gs_weapondesigner_designs` | Saved and published designs |
| `gs_weapondesigner_design_versions` | Immutable snapshot per publish, used by design history |
| `gs_weapondesigner_pool_slots` | Which weapon slot belongs to which design |
| `gs_weapondesigner_active_weapons` | Currently equipped designs, restored on reconnect |
| `gs_weapondesigner_assets` | Uploaded canvas assets |
| `gs_weapondesigner_tebex_entitlements` | Purchased designer passes and remaining allowances |
| `gs_weapondesigner_audit` | Grants, approvals, rejections, and deletions |

## Step 4: First Start

On its first start the resource scans the `weapon_templates/` folder and builds everything it needs:

- `data/manifest.json` — the discovered weapon templates and their designable parts
- `data/weapon_slots.json` — the slot configuration file (see [Configuration](configuration.md#the-slot-system-dataweapon_slotsjson))
- `data/ox_inventory_items.json` — the generated ox item catalogue
- `stream/` and `generated/metas/` — the streamed files for every weapon slot
- A managed block inside `fxmanifest.lua` between `-- [GENERATED WEAPONS START]` and `-- [GENERATED WEAPONS END]`

{% hint style="warning" %}
If the console reports that `fxmanifest.lua` was updated, restart the resource (or the server) once more so the newly generated files are mounted. Slot changes only take effect after a restart.
{% endhint %}

{% hint style="danger" %}
Never hand-edit the generated block in `fxmanifest.lua`, or anything inside `stream/`, `generated/`, or the generated files in `data/`. They are rebuilt by the resource. The only file in `data/` meant to be edited is `data/weapon_slots.json`.
{% endhint %}

## Step 5: AI Addon (Optional)

If you purchased the `gswd-ai` companion resource, install it as its own resource and start it **after** the designer:

```
ensure gs-weapondesigner
ensure gswd-ai
```

Add your AI provider key to the addon's server-side config. The key never leaves the server. AI generations are metered through the designer pass system — see [Tebex Integration](tebex.md).

---

## Rebuilding the UI (Optional)

The NUI is **already built** into `web/dist` — no build step is required. To rebuild it after making UI changes:

```bash
cd resources/gs-weapondesigner/web-source
npm install
npm run build
```

The build outputs to `web/dist` automatically. The server bundle can likewise be rebuilt with `npm run build:server` from the resource root.

---

If something does not work after installation, see [Troubleshooting](troubleshooting.md).
