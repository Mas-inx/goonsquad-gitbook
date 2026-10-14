# Installation

## 1. Install and Start

Extract the customer package into a folder named `gs_laflavia`. Keep that exact name: DUI URLs, callbacks and exports use it. Start the map, lift, database, framework and inventory before the script:

```cfg
ensure oxmysql
ensure qbx_core             # or qb-core / es_extended
ensure ox_inventory        # or a supported inventory
ensure ox_target           # optional; qb-target or native prompts also work
ensure laflavia
ensure laflavia_elevator
ensure gs_laflavia
```

Only enable the lines for the components installed on your server. The map and lift are separate resources. The manifest declares oxmysql and OneSync state awareness as requirements; see the [Cfx manifest reference](https://docs.fivem.net/docs/scripting-reference/resource-manifest/#runtime-constraints).

## 2. Inventory Items

| Backend | Included definition | Action |
|---|---|---|
| ox_inventory / typical Qbox setup | `install/items_ox_inventory.lua` | Merge the entries into `ox_inventory/data/items.lua` |
| QBCore-compatible inventories | `install/items_qb_core.lua` | Merge into `qb-core/shared/items.lua` or the backend's matching item registry |
| Default ESX inventory | `install/esx.sql` | Import the item/job definitions into the ESX database |

Keep bottles non-stackable/unique where the backend supports that setting. Their `remaining` metadata tracks milliliters. Confirm every model `item` and recipe `reward.item` exists in the inventory.

Copy the 39 PNG icons from `install/images/` into your inventory's image directory. Examples are `ox_inventory/web/images/`, `qb-inventory/html/images/`, and `qs-inventory/html/images/`; forks can use different paths. These are item icons, separate from the recipe cards and supplier images used by the resource's interface.

The spare `ice` and `glass_of_ice` definitions/icons are supplied for customization. The default ice machine finishes a carried drink and does not dispense a standalone inventory item. Restart the inventory/server after merging definitions.

## 3. Job and Boss Access

`Job.Register = true` creates a missing job at startup. An existing job with the same name is reused, so check its grades and boss flag.

| Grade | Name | Configured pay | Boss |
|---:|---|---:|---|
| 0 | Barback | 50 | No |
| 1 | Bartender | 75 | No |
| 2 | Manager | 110 | No |
| 3 | Boss | 150 | Yes |

Use your framework's admin job assignment. On setups that provide the usual command, for example:

```text
/setjob <playerId> laflavia 3
```

This command belongs to the framework. GS La Flavia registers no commands. If runtime job creation is unavailable, merge the entry in `install/jobs.lua` into your framework job table, or use the ESX SQL definitions. The file contains separate qbox and qbcore blocks: copy only the matching laflavia entry. Qbox grades use numeric keys; QBCore grades use string keys. Do not replace the entire framework jobs file.

For Qbox, a runtime-created job is recreated when this resource starts and does not rewrite qbx_core's files. Persist the job definition in the framework if other resources need it before GS La Flavia starts. [Qbox job exports](https://docs.qbox.re/resources/qbx_core/exports/server).

## 4. Society Database

The resource creates `gs_laflavia_society` and `gs_laflavia_society_transactions`. For managed database setup, `install/society.sql` contains the same schema. Keep `Job.Society.account` stable after money is deposited.

The new release starts a new account at zero. Legacy balance import is off by default. Only enable `Job.Society.importLegacyBalance` when deliberately migrating an old account; see [Configuration](configuration.md#society-account).

## 5. Backend and Locations

Leave the four selectors in `config/config.lua` at `auto` unless another installed backend is being selected. Verify stations, counter height, tablets/trays, ice machine, storage shelf, pickup point, delivery gate and boss laptop together when relocating the bar.

Default ESX inventory cannot open the shared storage stash: set `Storage.Enabled = false` or install a supported stash backend. Without bottle metadata, consumption falls back to whole bottles.

## 6. Verify

1. Check startup for job, inventory, recipe and database errors.
2. Assign a staff job and open a station; a customer account must not enter staff-only interactions.
3. Buy supplies from the monitor, collect them at pickup/delivery and make one drink.
4. Test an iced recipe: carry it to the machine, fill it and serve it.
5. Use the resulting inventory item and confirm effects and metadata.
6. Place a customer order, supply its tray and collect from the correct tablet number.
7. Test a cancellation/refund, staff hiring and a society deposit/withdrawal.
8. Restart and confirm open orders and the society balance remain intact.

Use [Troubleshooting](troubleshooting.md) if a check fails. The portal-processed download also needs this live-server verification before customer distribution.
