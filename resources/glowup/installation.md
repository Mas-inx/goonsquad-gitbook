# Installation

## Step 1: Add the Resource

Place the release folder in your server's resources directory and keep its name **`gs-glowup`**. Start your selected framework, inventory and optional helpers before GlowUp.

A Qbox example:

```cfg
ensure oxmysql
ensure ox_lib
ensure qbx_core
ensure ox_inventory
ensure ox_target
ensure gs-glowup
```

Use the integrations appropriate to your server. A standalone installation with the internal wallet and bag can start with:

```cfg
ensure gs-glowup
```

Enable OneSync. Automatic integration detection runs at startup; restart GlowUp after changing adapters or start order. Keep the entire `stream/` directory: it contains the table and machine models, archetypes and animations.

---

## Step 2: Register Items

`install/items.lua` is an installation reference, not a runtime script. Copy only the item lines between the `>>> copy` and `<<< copy` markers for your inventory. Do not paste the surrounding local variable, return statement or Markdown fences into an inventory file.

| Item section | Destination |
|---|---|
| Section 1, `ox_inventory` table | `ox_inventory/data/items.lua`, inside the returned table |
| Section 2, `qb_core` table | `qb-core/shared/items.lua`, inside `QBShared.Items`; adapt to the selected inventory's item table where required |
| Section 3, `esx_sql` string | Run the SQL against the database backing your default ESX inventory |

Qbox with ox_inventory uses section 1. ESX with ox_inventory also uses section 1 instead of the default-inventory SQL. Check your ESX schema and weight units before importing the SQL example; its small material weights are rounded to zero in the supplied definitions.

There are **32 items**: two equipment items, nineteen materials and eleven cosmetics. Equipment weighs 15 kg for the workbench and 8 kg for the mixer in the gram-based definitions, and each occupies its own non-stacking slot.

| Equipment | Default item | Supplier price |
|---|---|---|
| Makeup Workbench | `makeup_workbench` | $12,000 |
| Pigment Mixer | `pigment_mixer` | $8,500 |

The other names come from `data/materials.lua` and `data/products.lua`. If you rename items, update the recipes, goal filters, inventory definitions and image filenames as well. See [Configuration](configuration.md#items-and-recipes).

### Usable Items

Qbox, QBCore and ESX receive usable registrations through their framework bridge. Equipment always registers; cosmetics register only when `Config.ProductUse.enabled = true`.

For **standalone with ox_inventory**, add a server export to each usable definition:

```lua
['makeup_workbench'] = {
    label = 'Makeup Workbench', weight = 15000, stack = false, close = true,
    server = { export = 'gs-glowup.makeup_workbench' },
},
```

Use the exact item name after `gs-glowup.` for the mixer and any enabled cosmetics. These exports are inventory lifecycle callbacks, not public item-grant functions. With `Config.Inventory = 'none'`, no external item registration is required; use the app bag or `/glowuse <item>`.

---

## Step 3: Copy Icons

Copy `install/images/*.png` into your inventory's image folder:

| Inventory | Image folder |
|---|---|
| ox_inventory | `ox_inventory/web/images/` |
| qb-inventory / lj-inventory | `<inventory>/html/images/` |
| qs-inventory | `qs-inventory/html/images/` |
| esx_inventoryhud | `esx_inventoryhud/html/img/items/` |

Keep GlowUp's `inventory/*.png` too: the interface uses these as fallback images. Restart the full server after changing framework item definitions. No job needs to be registered.

---

## Step 4: Configure Persistence

`Config.Storage = 'auto'` selects oxmysql when started, otherwise JSON. SQL mode creates these tables automatically:

- `gs_makeup_players`: profiles, XP, earnings, stats, goals, awards and extra state
- `gs_makeup_placements`: equipment ownership, position and heading
- `gs_makeup_meta`: one-time migration markers

No schema import is needed for GlowUp's own tables. JSON mode creates `storage/data.json`; keep `storage/` writable and preserve it during updates. The SQL and JSON backends are separate stores; switching between them does not migrate existing data automatically.

The optional legacy import is **off by default**. See [Integrations](integrations.md#legacy-studio-import) before enabling it.

## Step 5: First-Run Check

1. Confirm the server console prints the bridge selection, storage backend and `GlowUp studio ready`.
2. Use the default female freemode ped, or configure an allowed model.
3. Visit the Studio Supplier at `vec4(-599.4, -1146.38, 22.27, 0.11)`.
4. Buy equipment and recipe ingredients, then place the table and snap the mixer onto it.
5. Craft one product, sell it, and verify the inventory, wallet and career stats.
6. Restart the resource and confirm profile and placement persistence.

Gameplay coordinates, streamed animations and framework compatibility should be checked on your actual server before launch.
