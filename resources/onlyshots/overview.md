# GS OnlyShots

GS OnlyShots gives players a content-creator career in FiveM. Buy a camera, take posed photos around the city, combine them into collections, and call NPC buyers to sell your work. Build followers, climb ten ranks, complete daily and weekly goals, and compete on the earnings leaderboard. Buyers can refuse an offer or steal it, starting a chase to recover the item.

**Version:** 1.0.0 | **Author:** Goonsquad Studios | **Resource:** `gs-onlyshots`

{% hint style="info" %}
The release is prepared for FiveM Asset Escrow. Configuration, language strings, ranks, poses, challenges, framework bridges, inventory integration, database integration and item definitions remain editable. See [Configuration](configuration.md#editable-files).
{% endhint %}

---

## Features

### Camera and Content

- Orbiting selfie camera and a forward-facing point-of-view mode
- Five shot presets: face, chest, full body, rear and feet
- Twelve poses, smooth zoom and height adjustment, shutter effects and captions
- Personal gallery with search, type filters, sorting, grid and list views
- Photo collections made from three to ten photos, depending on rank
- Real screenshots saved in the database or uploaded to a configured host; photo-type artwork when image capture is unavailable

### Sales and Collecting

- NPC buyers walk up to the player and pay, reject the offer or steal the item
- Rank and capture-location bonuses, tips and occasional viral payouts
- Timed thief chases with a search interaction to recover stolen work
- Player purchase offers with an accept/decline popup and preview
- Bought photos remain collectibles; the original creator retains authorship

### Progression

- Ten ranks from Rookie to Legend, with payout bonuses and larger collections
- Five premium hotspots with rank requirements and discovery XP
- Three daily and two weekly challenges selected from shared rotating pools
- Ten permanent achievements with automatic cash and XP rewards
- Lifetime statistics, followers and a top-ten earnings leaderboard

### Server Integration

- Qbox, QBCore, ESX and standalone framework adapters
- Four supported external inventories plus an internal inventory
- Optional target interaction, notification and progress adapters
- Automatic database tables or JSON persistence for stats and internal items
- Optional Discord role access and photo activity webhook
- Five languages: English, Spanish, German, French and Brazilian Portuguese

---

## Compatibility

| Component | Support |
|---|---|
| Framework | Qbox (`qbx_core`), QBCore (`qb-core`), ESX (`es_extended`) or standalone |
| Inventory | `ox_inventory`, `qb-inventory`, `qs-inventory`, `lj-inventory` or `'internal'` |
| Interaction | `ox_target`, `qb-target` or built-in E prompts |
| Notifications | `ox_lib`, framework notifications or built-in interface toasts |
| Progress | `ox_lib` or built-in interface progress bar |
| Database | `oxmysql` preferred, then `mysql-async`; JSON fallback without either |
| Image capture | `screenshot-basic`, optional for real images |
| Image storage | SQL, Fivemanage, Discord webhook, custom HTTPS host or placeholders |

ESX's default inventory does not have a dedicated photo metadata adapter. Use `ox_inventory` or the internal inventory for that setup. See [Integrations](integrations.md).

---

## Requirements

- A current FiveM GTA V server artifact supporting the resource's Lua and JavaScript scripts
- OneSync for the server's player-model, position and thief-entity checks
- A writable resource `data/` folder when using JSON persistence
- Metadata-capable item registration when using an external inventory
- `screenshot-basic` and a working storage backend if players should save real images

No job registration, npm installation or interface build is needed to run the release. Creator actions require `mp_f_freemode_01` by default; the model restriction and optional role check are configurable. Players outside the creator model restriction can still browse the app and purchase collectibles.

## Quick Start

1. Follow [Installation](installation.md) and register the items for your inventory.
2. Choose image storage and balance the economy in [Configuration](configuration.md).
3. Buy a camera and learn the app in [Usage](usage.md).
4. Connect server systems through [Integrations](integrations.md) and [API & Exports](exports.md).
5. Check [Troubleshooting](troubleshooting.md) if a feature does not work.
