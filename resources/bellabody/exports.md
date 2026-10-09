# API & Exports

## Public API

GS BellaBody does not expose a public career, money, XP or item-grant API. The `GS`, `Bridge`, `Storage`, `Clinic` and `Progression` tables are internal resource globals. Calling them from another resource is not a supported integration contract.

The open bridge and storage files are the intended customization points. See [Integrations](integrations.md).

---

## Inventory Lifecycle Exports

When the framework resolves to standalone, usable-item registration creates server exports using the **configured inventory item names**. Default names are:

| Export | Usable action |
|---|---|
| `gs-bellabody.operation_table` | Begin table placement |
| `gs-bellabody.crafting_kit` | Open the crafting panel |
| `gs-bellabody.small_bbl_shot` | Begin a matching/upgrade procedure near the patient |
| `gs-bellabody.big_bbl_shot` | Begin a matching/upgrade procedure near the patient |

These receive an inventory lifecycle signature `(event, itemData, inventory)`. On `usingItem`, the bridge uses `inventory.id` as the player source and invokes the corresponding item callback. They do not add items or bypass the server's recipe/procedure validation.

Register them through the inventory item definition, rather than calling them directly:

```lua
server = { export = 'gs-bellabody.crafting_kit' }
```

On Qbox, QBCore and ESX the framework performs usable registration instead. These dynamic exports are not guaranteed to exist on framework installations.

## Internal Request Protocol

The client sends `gs-bellabody:server:request(reqId, name, data)` and receives `gs-bellabody:client:response(reqId, result, errorKey, extra)`. The client request wrapper has a ten-second timeout. This router supports the bundled client and interface; it is not a public automation API.

Request groups include:

| Group | Internal requests |
|---|---|
| Profile | `player:init` |
| Shop | `shop:catalog`, `shop:checkout` |
| Crafting | `craft:list`, `craft:start`, `craft:finish`, `craft:cancel` |
| Placement | `placer:start`, `placer:confirm`, `placement:pickup`, `table:rails` |
| Walk-ins | `customer:summon`, `customer:respond`, `customer:arrived`, `customer:leave` |
| Procedures | `clinic:shots`, `operation:start`, `operation:cancel`, `operation:complete` |
| Career | `hub:data`, `hub:leaderboard`, `goals:claim`, `awards:claim` |
| Internal bag | `bag:list`, `bag:use` |

Prices, procedure offers, output, rewards and durations come from the server. Craft/procedure sessions reserve items and reject repeated or premature completion.

## Interface Callbacks

The bundled interface uses `uiReady`, `close`, `pickerSelect`, `shopCheckout`, `craft`, `place`, `placerAction`, `hubTab`, `claim` and `bagUse`. These are NUI callbacks registered by the client. They are not server exports or chat commands.

## Lifecycle Events

`gs-bellabody:server:ready`, `gs-bellabody:client:ready` and `gs-bellabody:client:cleanup` are local lifecycle events used by the bundled scripts. No documented external listener payload or reset/admin event is provided.
