# Integrations

## Frameworks

| Backend | Behavior |
|---|---|
| Qbox | Native job/player/money exports; staff membership can include additional jobs; online and offline staff are listed |
| QBCore | Player/shared-job APIs; offline staff read from the framework players table |
| ESX | Player/job APIs and ESX SQL tables for offline staff; needs use esx_status events |

The configured job is created only when missing. Existing grades are not overwritten. An ESX boss grade is identified as `boss`; QB/Qbox use `isboss`. Keep an appropriate boss grade when adapting the definition.

The Qbox adapter accepts its current group-members map as well as a legacy member list. Its runtime job creation follows the [official Qbox exports](https://docs.qbox.re/resources/qbx_core/exports/server). Inventory/framework forks can still need edits to the open adapters.

## Inventory Behavior

| Backend | Trays and supplier orders | Shared storage | Bottle volume |
|---|---|---|---|
| ox_inventory | Real protected stashes with open/swap hooks | Registered stash | Metadata |
| qb/ps/lj inventory | Direct handover/collection | Backend stash open, with a client fallback for older versions | Player item info |
| qs-inventory | Direct handover/collection | Backend stash registration/open path | Item metadata when the export works |
| Framework item functions | Direct handover/collection | Optional recognized stash APIs | QB metadata; default ESX uses whole-bottle fallback |

Generic fallback is conditional compatibility, not a dedicated adapter for every inventory product. CodeM, TGIANN and Origen stash calls are included, but item functions must remain compatible with the active framework. Qbox requires a suitable dedicated inventory.

Only ox_inventory offers the order/supplier stash hooks. Their checks enforce character ownership, staff access, correct ordered item and pickup/monitor/tray proximity. Direct handover paths perform corresponding server permission and position checks.

If a custom backend cannot write metadata reliably, set the adapter's `supportsMetadata` capability to false. Implement count, slots, add/remove, carry checks, metadata and item labels consistently. A false metadata-write result must propagate to the caller; ingredient rollback depends on it.

## Target and Notifications

`bridge/target.lua` chooses ox_target, qb-target or native E/G prompts. Each zone supplies access/interaction callbacks, so replacing the target adapter does not require opening core session files. Notification selection is ox_lib, framework or themed toasts; both adapters remain editable.

## Map and Lift

The resource is configured for the La Flavia rooftop and Boss Office, using separate `laflavia` and `laflavia_elevator` resources. Default coordinates are not useful without that environment. For another venue, relocate stations, machine, tablets/trays, monitor/TV, counter frame, shelf, office and delivery gate together.

The lift calls the shared `bossFloorRule()` export. It returns `nil` for unrestricted access, or a job/minimum-grade table. Apply the rule on the lift server as well as its interface; hiding/locking a client button alone is not authorization.

## Banking and Persistence

The built-in society ledger needs only oxmysql. Purchases use framework cash/bank, drink-order income credits the society, and enabled refunds debit the original credited share. Staff supply purchases are personal payments unless your own integration implements a different flow.

Legacy import is opt-in and runs only for a newly created account. It copies a matching old account balance without removing it from the old ledger. Avoid running two active ledgers for the same business after migration.

Resource KVP is scoped to the resource/server installation. Preserve it together with SQL and inventory stashes during maintenance or migration; copying the Lua folder alone does not preserve open orders. Keep the resource name and configured stash/account IDs stable.

## Custom Hooks

`Orders.OnPaid(order)` and `Supplies.OnPaid(order)` run after payment/order creation and receive a copied record. They do not replace the built-in charge; charging again inside a hook bills the player twice. `Config.UsableDrinks.onDrink(source, recipe, metadata)` runs after consuming a finished drink. See [API & Exports](exports.md) for examples and events.
