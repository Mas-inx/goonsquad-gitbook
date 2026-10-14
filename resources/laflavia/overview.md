# GS La Flavia

GS La Flavia is an interactive bartending resource for FiveM. Make drinks on a 3D counter, take paid customer orders at tablets, fill pickup trays, restock supplies and manage the rooftop bar's staff and money.

**Version:** 1.0.0 | **Author:** Goonsquad Studios | **Resource:** `gs_laflavia`

{% hint style="info" %}
The default locations are built for the separately installed La Flavia map and its lift. The script includes its bar props and interfaces; the map and lift are not bundled in this resource.
{% endhint %}

---

## Features

### Bartending

- Two configured preparation stations with an interactive drinks menu and close-up cameras
- Twelve cocktails, mocktails, wines, spirits and soft drinks
- Drag bottles and tools, pour measured amounts, muddle, squeeze, shake, strain, stir and garnish
- Quality scores, tiers and metadata with the bartender, recipe, ingredients and alcohol content
- Volume-based bottle consumption where the inventory supports metadata; one empty glass per drink
- Animated ice dispenser with a carry-to-machine workflow for iced recipes
- Nearby players see synchronized props, pouring and carried drinks
- Procedural sounds, particles, drinking animations, thirst/stress effects and temporary intoxication

### Bar Operations

- Four customer tablets with cash/bank payment and numbered pickup trays
- Staff monitor with live orders and a supplies catalog containing 25 purchasable items
- Public order TV showing preparation and pickup status
- Supplier pickup or scooter courier delivery; inventory-capacity failures leave supplies waiting
- Shared back-bar storage, an automatically registered four-grade job and a boss-office laptop
- Hire offers, staff promotion/demotion/removal, society deposits/withdrawals and transaction history
- Job/grade rule export for the La Flavia lift's Boss Office
- Paid orders and supply orders stored in resource KVP; society account stored in SQL

## Compatibility

| Component | Support |
|---|---|
| Framework | Qbox, QBCore or ESX |
| Inventory | `ox_inventory`, `qb-inventory`, `ps-inventory`, `lj-inventory`, `qs-inventory`, or compatible framework item functions |
| Target | `ox_target`, `qb-target`, or built-in E/G prompts |
| Notifications | `ox_lib`, framework notifications or interface toasts |
| Database | `oxmysql` |
| Language | English included; Lua text is editable in `config/locale.lua` |

The Qbox combination normally uses ox_inventory. Framework fallback inventories need compatible player item functions. Dedicated stashes and inventory hooks differ by backend; see [Integrations](integrations.md). The diagnostic standalone selection has no built-in inventory or money system and is not a full supported deployment.

## Requirements

- A FiveM GTA V server with OneSync state awareness enabled
- Qbox, QBCore or ESX and a supported inventory with the included items registered
- `oxmysql`, started before the resource
- The La Flavia map and lift for the default rooftop and Boss Office setup, or correctly relocated configuration
- A writable/persistent resource KVP store and SQL database

The release is prepared for Asset Escrow. Its 27 explicit Lua configuration and integration files remain editable. Runtime scripts and custom streamed models can be processed through the Cfx portal; browser code ships as production assets. No npm installation is required.

## Quick Start

1. Follow [Installation](installation.md).
2. Set locations, access, prices and inventory behavior in [Configuration](configuration.md).
3. Learn preparation, ordering and staff workflows in [Usage](usage.md).
4. Review [Integrations](integrations.md), [API & Exports](exports.md) and [Troubleshooting](troubleshooting.md).
