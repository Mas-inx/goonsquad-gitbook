# GS BellaBody

GS BellaBody gives players a cosmetic clinic career in FiveM. Buy clinic supplies, craft procedure shots with a portable kit, deploy an animated operation table and treat walk-in NPC clients. Earn money and XP, climb ten career ranks, complete rotating goals and claim permanent achievements.

**Version:** 1.0.0 | **Author:** Goonsquad Studios | **Resource:** `gs-bellabody`

{% hint style="info" %}
The release is prepared for FiveM Asset Escrow. Configuration, customer appearances, animation presets, recipes, goals, awards, framework bridges, persistence and installation item definitions remain editable. See [Configuration](configuration.md#editable-files).
{% endhint %}

---

## Features

### Clinic Gameplay

- NPC supplies vendor with categories, search, a shopping cart and cash/bank checkout
- Six inventory items, including a portable crafting kit and operation table
- Two procedure-shot recipes with batches of one to five
- Custom stretcher with folding and side-rail animations
- Placement preview, rotation and height controls, persistent ownership and packing
- Five styled walk-in NPC customers with random procedure requests and offers
- Small and Big BBL procedures, shot matching and higher-tier upgrades
- Included patient and surgeon animation dictionaries with synchronized scenes
- Cinematic procedure camera, configurable shots, letterbox and X cancellation
- Rank-based payouts, crafting/procedure speed and tips

### Career and Integration

- Ten ranks from Intern to Plastic Legend
- Three daily and two weekly goals, selected from configurable pools
- Six permanent achievements with rewards claimed through the career hub
- Personal stats, earnings leaderboard and built-in inventory bag
- Qbox, QBCore, ESX and standalone framework adapters
- Optional inventory, targeting, notification, progress and SQL integrations
- SQL or JSON persistence and optional server-only activity webhooks
- English, Spanish, German, French and Brazilian Portuguese

## Compatibility

| Component | Support |
|---|---|
| Framework | Qbox (`qbx_core`), QBCore (`qb-core`), ESX (`es_extended`) or standalone |
| Inventory | `ox_inventory`, `qb-inventory`, `qs-inventory`, `lj-inventory`, ESX default inventory or internal bag |
| Interaction | `ox_target`, `qb-target` or built-in E prompts |
| Notifications | `ox_lib`, framework notifications or themed interface toasts |
| Progress | `ox_lib` or themed progress bar for crafting and fallback procedures |
| Storage | `oxmysql` or resource-local JSON |

The older inventory adapters depend on matching framework player APIs. There is no dedicated `ps-inventory` adapter. See [Integrations](integrations.md).

## Requirements

- A FiveM GTA V server supporting the included Lua scripts and streamed assets
- OneSync for server entity, model and position checks and the default networked patients
- Registered items and icons when using an external inventory
- A writable `storage/` folder when using JSON persistence

There is no required job registration or npm installation. The release includes the interface assets. Shop purchases, crafting, placement, summoning and procedures require the female freemode model by default; this restriction is configurable.

Procedures treat generated NPC clients. They do not modify player body meshes or save player appearance. NPC outfits remain unchanged by default; the optional completion effect changes clothing rather than body geometry.

## Quick Start

1. Follow [Installation](installation.md) and register your inventory items.
2. Adjust locations, economy and adapters in [Configuration](configuration.md).
3. Learn the clinic loop and career hub in [Usage](usage.md).
4. Review [Integrations](integrations.md), [API & Exports](exports.md) and [Troubleshooting](troubleshooting.md).
