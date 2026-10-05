# Installation

## Step 1: Add the Resource

Place the release folder in your server's `resources` directory. Keep its name exactly **`gs-onlyshots`** so the supplied inventory export names work.

Start your framework, inventory, optional target and database before OnlyShots. For real images, also start `screenshot-basic` first. A Qbox example is:

```cfg
ensure oxmysql
ensure ox_lib
ensure qbx_core
ensure ox_inventory
ensure ox_target
ensure screenshot-basic
ensure gs-onlyshots
```

Use the resources appropriate to your server. OnlyShots has built-in fallbacks for inventory, interaction, notifications and progress, so a standalone setup can start with just:

```cfg
ensure gs-onlyshots
```

Ensure OneSync is enabled. Framework and inventory detection run at startup; restart OnlyShots after changing the selected integration or start order.

---

## Step 2: Register the Items

`install/items.lua` contains copyable definitions and `install/images/` contains the three item icons. The file is a reference file; OnlyShots does not load it automatically. Copy the lines between the `>>> copy` and `<<< copy` markers for the inventory you use.

| Item | Default name | Purpose |
|---|---|---|
| Camera | `gs_camera` | Opens photo mode; costs $10,000 at the boutique by default |
| Photo | `gs_photo` | A single capture with creator, caption, image and value metadata |
| Collection | `gs_bundle` | A group of captures with collection and photo metadata |

{% hint style="warning" %}
Photos and collections must remain non-stackable or unique. Their metadata distinguishes each item and records its creator. Keep item names in your inventory definitions synchronized with `Config.Items`.
{% endhint %}

### ox_inventory

Add these entries inside the returned table in `ox_inventory/data/items.lua`:

```lua
['gs_camera'] = {
    label = 'OnlyShots Camera', weight = 800, stack = false,
    close = true, consume = 0,
    server = { export = 'gs-onlyshots.useCamera' },
},
['gs_photo'] = {
    label = 'Photo', weight = 10, stack = false,
    close = true, consume = 0,
    server = { export = 'gs-onlyshots.usePhoto' },
},
['gs_bundle'] = {
    label = 'Photo Collection', weight = 50, stack = false,
    close = true, consume = 0,
    server = { export = 'gs-onlyshots.useBundle' },
},
```

Copy the PNGs from `install/images/` to `ox_inventory/web/images/`. Hosted image URLs can also appear in the photo tooltip. Database `shot:<id>` references are resolved by the OnlyShots app rather than an inventory tooltip.

### qb-inventory and lj-inventory

Copy the `qb_core` definition block from `install/items.lua` into `qb-core/shared/items.lua`, inside `QBShared.Items`. Keep `unique = true`, `useable = true` and the supplied image names. Copy the PNGs to your inventory's `html/images/` folder.

The server bridge registers camera, photo and collection use through the framework. The supplied adapters use QB player item data and metadata in `info`.

### qs-inventory

Copy the unique item block into `qs-inventory/shared/items.lua` and copy icons into `qs-inventory/html/images/`. This adapter reads the inventory through its exports and preserves photo metadata in `info`.

### ESX and Internal Inventory

On ESX with `ox_inventory`, use the ox_inventory instructions. Otherwise use:

```lua
Config.Inventory = 'internal'
```

No external item definitions or icons are needed for internal mode. Camera ownership is a saved flag; photos and collections are stored by OnlyShots and viewed in the app.

The optional ESX SQL block in `install/items.lua` only registers names in an ESX items table. It does not implement a metadata adapter for the default ESX inventory. For a different inventory, adapt the open `server/inventory.lua` file as described in [Integrations](integrations.md#custom-inventories).

Restart the inventory and framework, or the full server, after editing shared item definitions. No job needs to be registered.

---

## Step 3: Choose Persistence and Images

With a started database adapter, OnlyShots creates `gs_onlypics_stats` and `gs_onlypics_items` automatically. With SQL image storage it also creates `gs_onlypics_shots`. No separate SQL import is required for these tables.

Without a database adapter, stats and internal items use `data/store.json` by default. Keep the `data/` directory and allow the server to write to it.

Choose `Config.Upload.storage` in `Config.lua`:

| Value | Setup |
|---|---|
| `'sql'` | Default. Start a database adapter for persistent images |
| `'fivemanage'` | Set `ServerConfig.Upload.fivemanage.token` in `server/sv_config.lua` |
| `'discord'` | Set `ServerConfig.Upload.discord.webhook` in `server/sv_config.lua` |
| `'custom'` | Configure a compatible HTTPS host in `ServerConfig.Upload.custom`; see [Integrations](integrations.md#custom-image-hosts) |
| `'none'` | Use photo-type artwork; no real image storage |

{% hint style="warning" %}
With `'sql'` selected but no database adapter, images are held in memory and disappear on restart. JSON persistence saves stats and internal items, not image data. Use a database or a hosted backend when images need to survive restarts.
{% endhint %}

All real-image modes need the capture resource configured in `Config.Upload.resource`, default `screenshot-basic`. Missing capture or host configuration still allows photos with placeholder artwork. Credentials belong only in the server config.

---

## Step 4: Configure Access and Economy

Open `Config.lua` and review the model restriction, boutique position and price, payout account, photo values, sale odds and cooldowns. The default accepted model is `mp_f_freemode_01`.

Standalone wallets start at zero. For a standalone test, set `Config.Shop.price = 0` to get started, or supply your own economy adapter in `server/bridge.lua`. There is no public cash-grant export.

For an additional Discord role requirement, enable `Config.Gate.discordRole` and fill in the guild ID, role ID and bot token in `server/sv_config.lua`. See [Configuration](configuration.md#server-only-settings).

---

## Step 5: Verify In Game

Restart the resource and check the console for the detected adapters, persistence mode and photo storage. The final startup line looks like:

```text
[gs-onlyshots] OnlyShots ready | items: gs_camera / gs_photo / gs_bundle | photo storage: sql
```

1. Spawn on an accepted model and visit the boutique at `vector4(-608.64, -1130.93, 22.27, 280.43)`.
2. Buy a camera through the target interaction or E prompt.
3. Open `/onlyshots` or press F7, then start the camera.
4. Capture and save a photo. Check its caption and image in **My content**.
5. Capture three photos, create a collection and call a buyer from **Market**.
6. Reconnect and verify that camera ownership, portfolio and progress persist.
7. Test player collecting with a second player aiming at the creator and pressing H.

See [Usage](usage.md) for controls and [Troubleshooting](troubleshooting.md) for errors.
