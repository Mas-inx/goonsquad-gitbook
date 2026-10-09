# GS GlowUp

GS GlowUp gives players a makeup studio career in FiveM. Buy ingredients, deploy a folding workbench and pigment mixer, craft cosmetics, and sell them to NPC buyers. Build your career through ten ranks, daily and weekly goals, six achievements and an earnings leaderboard. Buyers can refuse a purchase or steal the products, starting a chase to recover them.

**Version:** 1.0.0 | **Author:** Goonsquad Studios | **Resource:** `gs-glowup`

{% hint style="info" %}
The release is prepared for FiveM Asset Escrow. Configuration, product and recipe data, animation presets, framework bridges, persistence and migration adapters, and installation item definitions remain editable. Language JSON and interface assets remain accessible. See [Configuration](configuration.md#editable-files).
{% endhint %}

---

## Features

### Studio and Crafting

- Supplier shop with categories, search, a shopping cart and cash/bank checkout
- Nineteen materials and two deployable equipment items
- Custom folding makeup table and animated pigment machine included
- Placement preview, rotation and height controls, and mixer snapping onto the table
- Equipment ownership, persistence, moving and packing; optional shared crafting access
- Eleven cosmetic recipes with batches of one to five and ingredient availability
- Close-up START button camera, dispensing animation and rank-based crafting speed
- Optional cosmetic application with item consumption, hand-to-face animations and temporary makeup overlays

### Sales and Progression

- Single-product sales and an automatic pop-up stand serving consecutive buyers
- Server-calculated prices, rank multipliers and tips
- Refusals return products; thefts create timed recovery opportunities
- Ten ranks from Apprentice to Master Stylist
- Three daily and two weekly goals selected from rotating pools
- Six permanent achievements with rewards claimed through the app
- Personal stats, a top-ten earnings leaderboard and a standalone inventory bag

### Server Integration

- Qbox, QBCore, ESX and standalone framework adapters
- External inventory adapters or an internal bag
- Optional target, notification, progress and SQL integrations
- Automatic database schema or JSON persistence
- Optional server-only activity webhooks
- English, Spanish, German, French and Brazilian Portuguese

---

## Compatibility

| Component | Support |
|---|---|
| Framework | Qbox (`qbx_core`), QBCore (`qb-core`), ESX (`es_extended`) or standalone |
| Inventory | `ox_inventory`, `qb-inventory`, `qs-inventory`, `lj-inventory`, ESX default inventory or built-in bag |
| Interaction | `ox_target`, `qb-target` or built-in E prompts |
| Notifications | `ox_lib`, framework notifications or themed interface toasts |
| Progress | `ox_lib` or themed interface progress bar |
| Storage | `oxmysql` or resource-local JSON |
| Appearance | Temporary native head overlays on freemode peds; no appearance-save adapter supplied |

The older inventory adapters depend on the matching framework item APIs. There is no dedicated `ps-inventory` adapter. See [Integrations](integrations.md).

## Requirements

- A FiveM GTA V server supporting the included Lua scripts and streamed props
- OneSync for server-side position/model checks and networked buyer NPCs
- Registered items and icons when using an external inventory
- A writable `storage/` directory when using JSON persistence

No job registration or npm installation is needed. The release includes the built interface. Shop purchases, placement, crafting and selling require `mp_f_freemode_01` by default; this model restriction is configurable. Cosmetic application is disabled by default.

## Quick Start

1. Follow [Installation](installation.md) and register your inventory items.
2. Adjust the economy, locations and integrations in [Configuration](configuration.md).
3. Learn the studio, sales and career app in [Usage](usage.md).
4. Review [Integrations](integrations.md), [API & Exports](exports.md) and [Troubleshooting](troubleshooting.md).
