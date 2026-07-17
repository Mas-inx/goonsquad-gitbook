# Installation

{% embed url="https://www.youtube.com/watch?v=BOuWPZYtwQw" %}

This guide covers a new GS Cloth Designer 1.3.0 RC installation. Existing installations can keep their database; startup migrations add new columns and tables automatically.

---

## 1. Install the Resource

Place the `gs-clothdesigner` folder in your server's resources directory. Do not rename the resource because its exports and integration events use the name `gs-clothdesigner`.

Start it after `oxmysql`, your framework, and your inventory:

```cfg
ensure oxmysql
ensure qb-core          # or qbx_core / es_extended
ensure ox_inventory     # or your supported inventory resource
ensure gs-clothdesigner
```

`oxmysql` is required even when your inventory does not use it.

---

## 2. Database

The resource creates and migrates its database tables during startup. No manual SQL import is normally required.

For a manual or managed deployment, the complete schema is available at:

```text
gs-clothdesigner/sql/schema.sql
```

The 1.3.0 migrations include Tebex entitlements, design approval fields, prop support, and `previous_appearance_json` for restoring the clothing or prop that was worn before a custom design.

---

## 3. Inventory Setup

The inventory item name defaults to `gs_customshirt`. It must be unique or non-stackable because every printed item carries design metadata.

Ready-to-use definitions are included in `gs-clothdesigner/install/`:

| Backend | Install file | Destination |
|---|---|---|
| QBCore inventory | `install/qbcore.lua` | `qb-core/shared/items.lua` |
| `ox_inventory` | `install/ox_inventory.lua` | `ox_inventory/data/items.lua` |
| Jaksam Inventory | `install/jaksam_inventory.lua` | Jaksam item definitions |
| CodeM Inventory | `install/codem_inventory.lua` | `codem-inventory/config/itemlist.lua` |
| Quasar Advanced Inventory | `install/qs_inventory.lua` | QB shared items or `qs-inventory/shared/items.lua` |
| AK47 Inventory | `install/ak47_inventory.lua` | AK47 item definitions |
| ESX inventory | `install/esx.sql` | Run against the ESX database |

### QBCore

Add the contents of `install/qbcore.lua` to `qb-core/shared/items.lua`. Make sure `gs_customshirt.png` exists in the image directory used by your inventory UI.

### ox_inventory and Qbox

Add the contents of `install/ox_inventory.lua` to `ox_inventory/data/items.lua`. Printed items receive hosted design previews and readable metadata automatically.

Qbox requires a supported external inventory. `ox_inventory` is the usual choice, but Jaksam, CodeM, Quasar, and AK47 are also detected when started.

### Jaksam Inventory

Add `install/jaksam_inventory.lua` to the Jaksam item definitions. The included `displayFields` show the design name, category, and printed version.

### CodeM Inventory

Add `install/codem_inventory.lua` to `codem-inventory/config/itemlist.lua`. The optional `install/codem_metadata.js` block can be added to `codem-inventory/config/metadata.js` to show design metadata in the tooltip.

### Quasar Advanced Inventory

For QB, add `install/qs_inventory.lua` to `qb-core/shared/items.lua`. For ESX, add it to `qs-inventory/shared/items.lua`. Place `gscd_custom_design.png` in `qs-inventory/html/images/` so the fallback item image resolves.

### AK47 Inventory

Add `install/ak47_inventory.lua` to the inventory's item definitions. Keep its `server.onUse` callback; that callback forwards the exact item and metadata to Cloth Designer. Place `gscd_custom_design.png` in the AK47 inventory image directory.

### ESX Inventory

Run `install/esx.sql`. If you use a supported external inventory on ESX, install the matching Lua item definition instead.

{% hint style="warning" %}
If you change `Config.InventoryItemName`, update the item name in both `shared/config.lua` and the inventory definition. The server prints a missing-item warning when the active backend cannot find it.
{% endhint %}

---

## 4. Media Storage

Choose a provider in `shared/config.lua`:

```lua
AssetUploadProvider = 'fivemanage' -- 'database', 'fivemanage', or 'discord'
```

### FiveManage

Put the API token in the server-only credentials file:

```lua
GSCD.Credentials.FiveManageApiKey = 'YOUR_API_KEY'
GSCD.Credentials.DiscordWebhookUrl = ''
```

The default API endpoint is already configured in `shared/config.lua`:

```lua
FiveManage = {
    base64Endpoint = 'https://api.fivemanage.com/api/v3/file/base64',
    filenamePrefix = 'gscd_asset',
    path = 'gs-clothdesigner',
    retentionExempt = false,
}
```

Normally, only the API key and optional folder path need changing. The endpoint is FiveManage's API URL, not a server-specific URL copied from your account.

### Discord

Create a webhook in the channel that will hold uploaded media, then set:

```lua
GSCD.Credentials.FiveManageApiKey = ''
GSCD.Credentials.DiscordWebhookUrl = 'YOUR_DISCORD_WEBHOOK_URL'
```

### Database

Set `AssetUploadProvider = 'database'`. No credential is required. This stores image payloads in MySQL and can grow the database faster than a hosted media provider.

{% hint style="danger" %}
Never place real API keys or webhook URLs in `shared/config.lua`. Clients can read shared files. Keep secrets in `server/credentials.lua` and do not publish that file with live values.
{% endhint %}

---

## 5. Designer Access

Edit the jobs and stations in `shared/config.lua`:

```lua
Designer = {
    jobs = {
        clothingdesigner = 0,
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

The regular station and `openDesigner()` flow validate the player's job and grade. To grant a one-time limited session from another resource, use `summonDesigner()` instead. See [API & Exports](exports.md#limited-designer-access).

---

## 6. Wardrobe and Approval

The player wardrobe is enabled by default:

```lua
Wardrobe = {
    Enabled = true,
    Command = 'gscd_wardrobe',
    AdminCommand = 'gscd_clothing_review',
    AdminAce = 'gscd.clothdesigner.admin',
    RequireApproval = false,
}
```

To require review before clothing is published, set `RequireApproval = true` and grant the configured ACE:

```cfg
add_ace group.admin gscd.clothdesigner.admin allow
```

See [Usage](usage.md#approval-workflow) for the full player and admin flow.

---

## 7. First Start

On the first start, Cloth Designer discovers the `.ydd` files in `cloth_templates/` and materializes the generated apparel pool. When the console asks for a restart, restart once more so FiveM registers the generated clothing resource.

```text
[gs-clothdesigner] New packs were materialised.
Restart the server for the apparel to take effect.
```

Subsequent starts reuse the pool unless templates changed or capacity needs to expand.

### Adding Templates Later

1. Put each `.ydd` in `cloth_templates/<gender>/<category>/`.
2. Add its companion `.ytd` when that drawable requires one.
3. Restart the server and follow any restart message printed by Cloth Designer.

The pack generator now reports the exact source YDD, companion YTD, and generated YDD when it finds an invalid or stale drawable reference. Valid templates continue to process.

---

## 8. Optional Tebex Setup

Tebex integration is disabled until configured. Follow [Tebex Integration](tebex.md) to define packages, allowances, grant commands, and claim behavior.

---

## 9. Permissions

The following examples allow an admin group to run protected maintenance commands in game:

```cfg
add_ace group.admin command.gscd_rescan allow
add_ace group.admin command.gscd_rebuild_pool allow
add_ace group.admin command.gscd_fill_slots allow
add_ace group.admin command.gscd_delete_design allow
add_ace group.admin gscd.clothdesigner.admin allow
add_ace group.admin gscd.tebex allow
```

`gscd_fill_slots` is a development command and should not be granted on a production server.

---

## 10. Verify the Installation

1. Confirm there are no `oxmysql` or missing-item errors during startup.
2. Join with a supported freemode ped and an allowed job.
3. Open the default station and load a template.
4. Upload an image below the configured limit.
5. Save and print a design.
6. Use the item twice to verify equip and unequip.
7. Reconnect and confirm equipped custom clothing is restored.

See [Troubleshooting](troubleshooting.md) if any step fails.
