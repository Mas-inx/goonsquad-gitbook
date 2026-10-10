# Installation

## Step 1: Install the Resource

Place the folder in your resources directory as **`gs-baddiehub`**. Keep the original name for item lifecycle exports and integrations. Start database, framework, inventory, garage and key resources first.

```cfg
set onesync on
ensure oxmysql
ensure qbx_core
ensure ox_inventory
ensure qbx_garages
ensure qbx_vehiclekeys
ensure gs-baddiehub
```

Replace optional resources with your setup. Standalone still requires `oxmysql`. `ox_lib` is optional. Automatic detection runs at startup; restart the hub after changing adapters or start order.

## Step 2: Register Items

`install/items.lua` is a reference, not a runtime script. Copy only the item lines between the copy markers from the appropriate local table or SQL string. Do not copy the wrapper variable or return statement.

| Section | Destination |
|---|---|
| `ox_inventory` | `ox_inventory/data/items.lua`, inside the returned table |
| `qb_core` | `qb-core/shared/items.lua`; adapt to the selected inventory |
| `esx_sql` | Your default ESX inventory database; verify columns and weight units |

| Item | Weight in gram-based definitions | Use |
|---|---|---|
| `velvet_welcome_box` | 500 g | Exchanges the box for phone ×1, water ×5, sandwich ×5 and lockpick ×1 |
| `lounge_pass` | 10 g | Members receive 150 season XP |

The additional reward item names must already exist in your inventory. Renaming the two lounge items requires matching changes in `Config.Items`, reward lists, definitions and icons. No job registration is needed.

Qbox, QBCore and ESX use the framework usable-item registration. For **standalone with ox_inventory**, add these server lifecycle exports to the definitions:

```lua
server = { export = 'gs-baddiehub.velvet_welcome_box' }
-- The Lounge Pass definition uses:
server = { export = 'gs-baddiehub.lounge_pass' }
```

The server item handler performs removal once and stops an additional automatic inventory consumption. Do not add another handler that removes the same item.

## Step 3: Copy Item Icons

Copy `install/images/*.png` to your inventory image folder and restart it.

| Inventory | Image folder |
|---|---|
| ox_inventory | `ox_inventory/web/images/` |
| qb-inventory / lj-inventory | `<inventory>/html/images/` |
| qs-inventory | `qs-inventory/html/images/` |
| esx_inventoryhud | `esx_inventoryhud/html/img/items/` |

## Step 4: Database and Permissions

The hub creates ten tables automatically: `gs_fh_profiles`, `gs_fh_friends`, `gs_fh_friend_declines`, `gs_fh_blocks`, `gs_fh_messages`, `gs_fh_chat`, `gs_fh_claims`, `gs_fh_season`, `gs_fh_meta` and `gs_fh_vehicles`. The last table records standalone vehicle ownership; it does not supply a garage. Existing schemas receive tracked migrations. Grant the database account the required create/alter/read/write permissions. Initialization failures disable the hub until repaired.

```cfg
add_ace group.admin gs_baddiehub.admin allow
add_ace group.admin gs_baddiehub.moderate allow
```

The admin ACE allows staff commands and moderation. The moderate ACE allows chat moderation only. Your existing principals must assign staff to `group.admin`.

## Step 5: First-Run Check

1. Confirm the database verification and hub-open messages with no errors.
2. Load an eligible female character and open `/baddiehub`. Test a denied character too.
3. Save a profile, send and accept a friend request, exchange DMs and test blocking with a second player.
4. Claim the starter pack, use its box and confirm actual inventory and money changes.
5. Claim a vehicle outdoors, confirm keys, ownership and retrieval through your garage after a restart.
6. Claim an unlocked season reward, use a Lounge Pass and verify progress after reconnecting.
7. Test staff permissions, themes, all five languages and optional Discord integration.

Back up all hub tables and configuration before updates. Preserve claims and season rows; clearing them allows rewards to be earned again.
