# Installation

## Step 1: Add the Resource

Place the release folder in your server's resources directory and keep its name **`gs-bellabody`**. Start your chosen framework, inventory and optional helpers before the clinic.

Qbox example:

```cfg
ensure oxmysql
ensure ox_lib
ensure qbx_core
ensure ox_inventory
ensure ox_target
ensure gs-bellabody
```

Standalone installation with the internal wallet and bag:

```cfg
ensure gs-bellabody
```

Enable OneSync. Automatic adapter detection runs at startup; restart the clinic after changing integration settings or start order. Keep all five compiled assets in `stream/`: the stretcher model, archetype and animation dictionary, plus patient and surgeon animation dictionaries.

---

## Step 2: Register Items

`install/items.lua` is an installation reference and is not loaded at runtime. Copy only the item lines between the `>>> copy` and `<<< copy` markers for your selected inventory. Do not paste the surrounding local variable or return statement into an item table.

| Section | Destination |
|---|---|
| `ox_inventory` table | `ox_inventory/data/items.lua`, inside the returned table |
| `qb_core` table | `qb-core/shared/items.lua`, inside `QBShared.Items`; adapt to your selected inventory where needed |
| `esx_sql` string | Run against the database backing the default ESX inventory |

Qbox with ox_inventory and ESX with ox_inventory both use the first section. Check your ESX schema and weight units before importing the SQL example. The supplied small-item SQL weights are rounded to zero.

| Item | Default name | Supplier price | Usable |
|---|---|---|---|
| Operation Table | `operation_table` | $25,000 | Place table |
| Portable Crafting Kit | `crafting_kit` | $5,000 | Open recipes |
| Empty Syringe | `empty_syringe` | $150 | No |
| Fat Extract | `fat_extract` | $450 | No |
| Small BBL Shot | `small_bbl_shot` | Crafted | Treat waiting patient |
| Big BBL Shot | `big_bbl_shot` | Crafted | Treat waiting patient |

In gram-based item definitions the table weighs 25 kg and the kit weighs 4 kg. Check your inventory capacity before buying both together.

### Usable Items

Qbox, QBCore and ESX receive usable registrations through the framework bridge. For **standalone with ox_inventory**, add `server.export` to each usable item:

```lua
['operation_table'] = {
    label = 'Operation Table', weight = 25000, stack = false, close = true,
    server = { export = 'gs-bellabody.operation_table' },
},
```

Repeat for `crafting_kit`, `small_bbl_shot` and `big_bbl_shot`, using the exact configured item name after `gs-bellabody.`. These exports are inventory lifecycle callbacks. When `Config.Inventory = 'none'`, use the hub bag or `/clinicuse <item>` without external item registration.

---

## Step 3: Copy Icons

Copy `install/images/*.png` into your inventory's image folder:

| Inventory | Image folder |
|---|---|
| ox_inventory | `ox_inventory/web/images/` |
| qb-inventory / lj-inventory | `<inventory>/html/images/` |
| qs-inventory | `qs-inventory/html/images/` |
| esx_inventoryhud | `esx_inventoryhud/html/img/items/` |

Keep the resource's `inventory/*.png` files too: the clinic interface uses them. Restart the full server after editing framework item definitions. No job needs to be added.

## Step 4: Configure Persistence

`Config.Storage = 'auto'` selects oxmysql when started, otherwise JSON. SQL mode creates two tables automatically:

- `gs_bellabody_players`: profiles, XP, earnings, stats, goals, awards and extra state
- `gs_bellabody_placements`: operation-table ownership, coordinates and heading

No schema import is required for these tables. JSON mode creates `storage/data.json`; keep the folder writable and preserve it during updates. SQL and JSON are separate stores. Switching backends does not migrate existing data automatically.

## Step 5: First-Run Check

1. Confirm the console reports the bridge, storage backend and `Bella Body Clinic clinic ready`.
2. Use `mp_f_freemode_01` or another configured allowed player model.
3. Visit Clinic Supplies at `vec4(-607.7, -1135.02, 22.27, 294.8)`.
4. Buy a table, kit, one syringe and one fat extract; craft a Small BBL Shot.
5. Place the table with clear walking space beside it and use `/bblclient`.
6. Accept with E, treat a matching patient and verify payment, XP and item consumption.
7. Test X cancellation, reward claims, table packing and persistence after a restart.

New standalone profiles receive $35,000, enough for the $30,600 starter purchase. Existing wallets are preserved. Framework installations use the player's existing money.
