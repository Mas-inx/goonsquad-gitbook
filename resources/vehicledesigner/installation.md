# Installation

This guide covers a new GS Vehicle Designer installation. The database tables are created and migrated during startup, so an existing installation can keep its data.

---

## 1. Install the Resource

Place the `gs-vehicledesigner` folder in your server's resources directory. Do not rename the resource because its exports, events, and inventory item flow use the name `gs-vehicledesigner`.

Start it after `oxmysql`, your framework, and your inventory:

```cfg
ensure oxmysql
ensure qb-core          # or qbx_core / es_extended
ensure ox_inventory     # or your supported inventory resource
ensure gs-vehicledesigner
```

`oxmysql` is required even when your inventory does not use it.

---

## 2. Database

The resource creates and migrates its tables during startup. No manual SQL import is normally required.

For a manual or managed deployment, the complete schema is available at:

```text
gs-vehicledesigner/sql/schema.sql
```

Seven tables are created. See [API & Exports](exports.md#database-integration) for what each one holds.

---

## 3. Inventory Setup

The item name defaults to `gs_vehicle_livery`. It must be unique or non-stackable because every printed item carries its own design metadata.

Ready-to-use definitions are included in `gs-vehicledesigner/install/`:

| Backend | Install file | Destination |
|---|---|---|
| QBCore inventory | `install/qbcore.lua` | `qb-core/shared/items.lua` |
| `ox_inventory` | `install/ox_inventory.lua` | `ox_inventory/data/items.lua` |
| Jaksam Inventory | `install/jaksam_inventory.lua` | Jaksam item definitions |
| CodeM Inventory | `install/codem_inventory.lua` | `codem-inventory/config/itemlist.lua` |
| Quasar Advanced Inventory | `install/qs_inventory.lua` | `qs-inventory/shared/items.lua` |
| AK47 Inventory | `install/ak47_inventory.lua` | AK47 item definitions |
| ESX inventory | `install/esx.sql` | Run against the ESX database |

### ox_inventory and Qbox

Add the contents of `install/ox_inventory.lua` to `ox_inventory/data/items.lua`. The definition carries the client export that fits the livery:

```lua
client = {
    export = 'gs-vehicledesigner.useLiveryItem',
},
```

Keep that export. Printed items receive hosted vehicle renders and readable metadata automatically.

Qbox requires a supported external inventory. `ox_inventory` is the usual choice, but Jaksam, CodeM, Quasar, and AK47 are also detected when started.

### QBCore

Add the contents of `install/qbcore.lua` to `qb-core/shared/items.lua`, then restart `qb-core`.

### Quasar Advanced Inventory

For QB, add `install/qs_inventory.lua` to `qb-core/shared/items.lua`. For ESX, add it to `qs-inventory/shared/items.lua`.

### CodeM Inventory

Add `install/codem_inventory.lua` to `codem-inventory/config/itemlist.lua`.

### Jaksam Inventory

Add `install/jaksam_inventory.lua` to the Jaksam item definitions. The included `displayFields` show the design name, vehicle, and printed version in the tooltip.

### AK47 Inventory

Add `install/ak47_inventory.lua` to the inventory's item definitions. Keep its `server.onUse` callback, which forwards the exact item and metadata to the designer:

```lua
server = {
    onUse = function(source, item)
        TriggerEvent("gs-vehicledesigner:server:useAk47Livery", source, item)
    end,
},
```

### ESX Inventory

Run `install/esx.sql`. If you use a supported external inventory on ESX, install the matching Lua item definition instead.

### Item Image

The definitions reference `gs_vehicle_livery.png`. Place an image with that name in your inventory's image folder:

| Inventory | Image folder |
|---|---|
| `ox_inventory` | `ox_inventory/web/images/` |
| QBCore inventory | `qb-inventory/html/images/` |
| Quasar | `qs-inventory/html/images/` |
| CodeM | `codem-inventory/html/itemimage/` |
| Jaksam / AK47 | That inventory's item image folder |

{% hint style="info" %}
Supply your own `gs_vehicle_livery.png`; the repository does not include that icon. Printed items use a vehicle render when hosting succeeds and the inventory supports `metadata.imageurl`. Otherwise they keep the fallback icon, including when using the `database` provider.
{% endhint %}

{% hint style="warning" %}
If you change `Inventory.itemName` in `shared/config.lua`, update the item name in the inventory definition to match. The server logs a missing-item warning when the active backend cannot find it.
{% endhint %}

---

## 4. Add Your Vehicles

The resource ships with no cars. Drop vehicle files into `vehicle_templates/` and restart the server; every model is converted and registered automatically.

```text
gs-vehicledesigner/vehicle_templates/
```

The first boot that finds new templates converts them and prints a restart banner. Restart once more so FiveM streams the generated assets.

```text
==========================================================
[gs-vehicledesigner] New vehicle assets were materialised.
Restart the server for the vehicles to take effect.
(Subsequent restarts will be instant - no action needed.)
==========================================================
```

This is the largest part of setup and has its own page. See [Adding Vehicles](vehicles.md).

---

## 5. Media Storage

Choose a provider in `shared/config.lua`:

```lua
AssetUploadProvider = 'discord' -- 'database', 'fivemanage', or 'discord'
```

The shipped default is `discord`. Configure your own webhook before using hosted uploads. Editable assets still fall back to database storage if hosting fails. Switch it to `database` for a zero-configuration install.

### Discord

Create a webhook in the channel that will hold uploaded media, then set it in the server-only credentials file:

```lua
GSVD.Credentials.FiveManageApiKey = ''
GSVD.Credentials.DiscordWebhookUrl = 'YOUR_DISCORD_WEBHOOK_URL'
```

Only real Discord webhook URLs are accepted. Uploads run in the Node runtime and use chunked transfer for large vehicle renders.

### FiveManage

```lua
GSVD.Credentials.FiveManageApiKey = 'YOUR_API_KEY'
GSVD.Credentials.DiscordWebhookUrl = ''
```

The API endpoint is already configured in `shared/config.lua` and is FiveManage's own API URL, not a URL copied from your account dashboard:

```lua
FiveManage = {
    base64Endpoint = 'https://api.fivemanage.com/api/v3/file/base64',
    filenamePrefix = 'gsvd_asset',
    path = '',
    retentionExempt = true,
}
```

### Database

Set `AssetUploadProvider = 'database'`. No credential is required. Image payloads are stored in MySQL, which grows the database faster than a hosted provider and means printed items keep their fallback icon instead of a vehicle render.

{% hint style="danger" %}
Never place real API keys or webhook URLs in `shared/config.lua`. Clients can read shared files. Keep secrets in `server/credentials.lua`.
{% endhint %}

---

## 6. Designer Access

Access is configured in `shared/config.lua`:

```lua
Access = {
    ace = 'gs_vehicledesigner.use',
    allowEveryone = true,
    adminAce = 'gs_vehicledesigner.admin',
    designerJobs = {
        mechanic = 0,
    },
},
```

{% hint style="warning" %}
`allowEveryone` ships as `true`, so every player can open the studio out of the box. Set it to `false` before going live if the studio should be restricted to a job, an ACE, or paid passes.
{% endhint %}

With `allowEveryone = false`, a player needs one of:

- the `gs_vehicledesigner.use` ACE,
- a job and grade listed in `designerJobs`,
- admin access, or
- an active Tebex designer pass.

Grant the ACEs in `server.cfg`:

```cfg
add_ace group.admin gs_vehicledesigner.admin allow
add_ace group.mechanic gs_vehicledesigner.use allow
```

### Optional Designer Stations

By default the studio opens anywhere with `/vehicledesigner`, `/gsvd`, or **F6**. To gate it to world locations, add stations and enable the requirement:

```lua
Designer = {
    requireStation = true,
    stations = {
        {
            label = 'Vehicle Livery Studio',
            coords = vec3(-211.55, -1324.35, 30.89),
            marker = vec3(1.4, 1.4, 0.6),
            interactDistance = 2.0,
            drawDistance = 20.0,
        },
    },
},
```

Markers are drawn within `drawDistance` and pressing **E** inside `interactDistance` opens the studio.

---

## 7. Optional Approval Queue

Publishing is immediate by default. To review finishes before they go live:

```lua
Approval = {
    enabled = true,
    autoApproveAdmins = true,
},
```

Admins always bypass the queue. See [Usage](usage.md#approval-workflow) for the player and admin flow.

---

## 8. Optional Tebex Setup

Tebex integration is disabled until configured. Follow [Tebex Integration](tebex.md) to define packages, allowances, grant commands, and claim behavior.

---

## 9. Permissions

The maintenance and export commands are registered as restricted commands. Allow them for an admin group:

```cfg
add_ace group.admin command.gsvd_rescan allow
add_ace group.admin command.gsvd_rebuild_pool allow
add_ace group.admin command.gsvd_pool_status allow
add_ace group.admin command.gsvd_expand allow
add_ace group.admin command.gsvd:exportpack allow
add_ace group.admin command.gsvd:bakeslot allow
add_ace group.admin command.gsvd:exportlist allow
add_ace group.admin gs_vehicledesigner.admin allow
```

`gsvd_fill_slots` is a development command that marks every slot for a model as occupied. Do not grant it on a production server.

---

## 10. Verify the Installation

1. Confirm there are no `oxmysql` or missing-item errors during startup.
2. Confirm the console reports the vehicle pool and the live slot count:

```text
[gs-vehicledesigner] Ready with 16 live finish slots per vehicle model.
```

3. Run `gsvd_pool_status` in the server console and confirm your vehicles are listed.
4. Join and open the studio with `/vehicledesigner`.
5. Select a vehicle and confirm the 3D preview loads.
6. Paint something, then save a draft.
7. Print the livery and confirm the item arrives. With working hosted media and a compatible inventory, confirm it uses a vehicle render; otherwise check the fallback icon.
8. Spawn the matching vehicle model, stand next to it, and use the item.
9. Confirm the finish appears and that another nearby player can see it.

See [Troubleshooting](troubleshooting.md) if any step fails.
