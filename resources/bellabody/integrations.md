# Integrations

## Automatic Detection

The shared bridge examines started resources at startup. Start dependencies before `gs-bellabody`; use explicit configuration when multiple inventories or targets are installed.

| Component | Detection order |
|---|---|
| Framework | `qbx_core` → `qb-core` → `es_extended` → standalone |
| Inventory | `ox_inventory` → `qb-inventory` → `qs-inventory` → `lj-inventory` → `esx_inventoryhud` → framework default → internal bag |
| Target | `ox_target` → `qb-target` → E prompts |
| Storage | Started `oxmysql` when configured `auto`/`mysql`; otherwise JSON |

Framework fallback selects the default ESX inventory for ESX or the QB adapter for Qbox/QBCore. Detection does not install missing resources. Optional helper loading falls back to built-in functionality if unavailable.

---

## Framework and Inventory Adapters

`server/bridge.lua` handles identity, player state, cash/bank, inventory operations, usable items and player lifecycle events. `client/bridge.lua` handles notifications, progress and entity interactions. `shared/bridge.lua` handles detection.

| Adapter | Implementation |
|---|---|
| Qbox | `qbx_core` player and usable-item exports; citizen ID for profiles |
| QBCore | Core object, player Functions and usable-item registration; citizen ID for profiles |
| ESX | Shared object, account/inventory APIs and usable registration; ESX identifier for profiles |
| Standalone | License-first identifier fallback, wallet and internal bag on the clinic profile |
| ox_inventory | Count/carry/add/remove exports with detailed weight/slot failures |
| qb-inventory | Direct inventory exports with player-Functions fallback |
| qs-inventory | Inventory exports with compatible player-Functions fallback |
| lj-inventory | Compatible player-Functions inventory APIs |
| ESX inventory | Player inventory methods |
| Internal bag | Counts stored in `profile.extra.items`; usable registrations and `/clinicuse` |

The QS/LJ fallbacks require a compatible framework player object. Test custom inventory versions against the open bridge. There is no dedicated PS adapter. Generic non-ox capacity checks may defer failure until item insertion; the shop rolls back failed purchases and crafting returns ingredients when output insertion fails.

Cash/bank checkout choices use `Config.ShopCheckout.accounts`. ESX `cash` maps to `money`; standalone uses one wallet and only offers cash. Procedure and goal/award payments use `Config.MoneyAccount`.

See [Installation](installation.md#usable-items) for item exports on standalone installations using ox_inventory.

## Interaction, Notifications and Progress

Targets register on each client's local shop, patient and table entities. Without a target adapter, nearby E prompts provide the same actions. If several actions are available, the built-in picker selects one.

Notifications try the optional helper, then the selected framework, then the themed interface. `Config.Notify = 'nui'` forces themed toasts. `Config.Progress = 'nui'` forces the built-in progress bar for crafting and fallback procedures. Custom synchronized surgeon procedures run their own camera, duration and stop control.

## Persistence

SQL mode automatically creates `gs_bellabody_players` and `gs_bellabody_placements`. JSON mode writes `storage/data.json`. Profiles contain XP, earnings, statistics, rotating goal state, achievement state and extra wallet/bag/refund data.

Back up the selected store before updates. Preserve item names and IDs used by existing progress and placements. Switching SQL/JSON stores does not migrate data. There is no automatic import from another resource.

## Networked Patients

Keep OneSync and `Config.Customer.networked = true` for the default behavior. On arrival the server checks the patient network ID, ownership, expected model and table distance. Treatment checks presence and nearby surgeon position again before consumption/payment. Other players can see the networked patient and synchronized scenes; table props are streamed locally from the authoritative placement list.

With `networked = false`, customers are local and their entity cannot be verified by the server. Without OneSync, model and position checks that rely on server ped access cannot enforce the same restrictions. Use the default networked setup for release deployments.

## Webhooks

Configure private URLs in `server/config.lua`:

```lua
Config.Webhooks = {
    operations = '',
    awards = '',
    admin = '',
}
```

`operations` logs completed procedures, `awards` logs rank changes and milestone unlocks, and `admin` logs selected suspicious timing/distance requests. Logs include player name, server ID and stored identifier. Empty URLs disable the channel. No webhook URL is supplied in the release.

## Appearance and Custom Tables

NPC customers use the female freemode model and native clothing/head-overlay APIs. No external appearance resource is required. Player body transformation or appearance-save integration is not provided.

The supplied patient/surgeon clips are fitted to the supplied stretcher. For another table, disable both custom scenes and the table animations, then configure fallback pose height/orientation. See [Configuration](configuration.md#operation-table-and-placement).
