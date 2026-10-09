# Configuration

Edit the open configuration files and restart `gs-bellabody` to apply changes. Keep a backup of customized data and language files when updating.

## Editable Files

The manifest contains these **12 explicit Lua-only escrow exceptions**:

| File | Purpose |
|---|---|
| `config.lua` | Gameplay settings, locations, economy, commands and integration selection |
| `server/config.lua` | Private webhook URLs |
| `data/customers.lua` | Five NPC appearances and names |
| `data/poses.lua` | Animation candidates, sequences and prop offsets |
| `data/recipes.lua` | Procedure-shot ingredients, output and craft times |
| `data/awards.lua` | Permanent milestones and rewards |
| `data/goals.lua` | Daily/weekly pools, targets and rewards |
| `shared/bridge.lua` | Integration detection and optional helper loading |
| `client/bridge.lua` | Notifications, progress and interaction adapters |
| `server/bridge.lua` | Player, money, inventory and usable-item adapters |
| `server/storage.lua` | SQL/JSON persistence adapter |
| `install/items.lua` | Item registration examples |

Language JSON and interface assets remain accessible. Compiled streamed assets must stay with the resource. The remaining gameplay Lua is intended for portal escrow processing.

---

## General Settings

| Setting | Default | Purpose |
|---|---|---|
| `Config.Debug` | `false` | Verbose diagnostic output |
| `Config.BrandName` | `Bella Body Clinic` | Notifications, activity logging and interface brand text |
| `Config.Locale` | `en` | Default language; players may override it |
| `Config.Currency` | `$` | Display symbol; does not change numeric balances |
| `Config.MoneyAccount` | `cash` | Procedure/reward payment account |
| `Config.SaveInterval` | `60` | Autosave interval in seconds, minimum 15 |
| `Config.FemaleOnly` | `true` | Restrict the clinic gameplay actions to allowed ped models |
| `Config.FemaleModels` | `{ 'mp_f_freemode_01' }` | Allowed player models |
| `Config.Standalone.startingCash` | `35000` | Initial internal wallet for a new profile |

Changing `BrandName` does not replace the image logos. Replace the brand images under `web/img/brand/` separately when customizing artwork. Player language overrides are stored locally and take precedence over `Config.Locale`.

## Integrations

| Setting | Values |
|---|---|
| `Config.Framework` | `auto`, `qbox`, `qb`, `esx`, `standalone` |
| `Config.Inventory` | `auto`, `ox`, `qb`, `qs`, `lj`, `esx`, `none` |
| `Config.Target` | `auto`, `ox`, `qb`, `none` |
| `Config.UseOxLib` | `auto`, `true`, `false` |
| `Config.Notify` | `auto`, `nui` |
| `Config.Progress` | `auto`, `nui` |
| `Config.Storage` | `auto`, `mysql`, `json` |

All integration selections default to `auto`. Helper loading still requires the helper resource to be started; `true` does not install it. See [Integrations](integrations.md) for detection order and adapter limitations.

## Commands

Rename entries in `Config.Commands`, or set an entry to `false` to disable its registration. See the complete [command list](usage.md#commands). Disabling a command does not remove its corresponding interface or item interaction.

## Items, Recipes and Shop

`Config.Items` maps six logical item keys to inventory names. The recipes and lookup helpers resolve these aliases. Rename the external item definitions, icon filenames and translation keys to match when changing names.

`Config.Shops.clinic` controls vendor model, location, scenario, blip and stock. Default stock contains the two equipment items and two ingredients. Shot items are crafted.

| Recipe | Ingredients | Time | Output | XP |
|---|---|---|---|---|
| `small_bbl_shot` | 1 syringe + 1 fat extract | 6 seconds | 1 | 5 |
| `big_bbl_shot` | 1 syringe + 2 fat extract | 8 seconds | 1 | 5 |

Times and XP above are per unit before rank speed boosts. Batch sizes are clamped to one through five. `Config.XP.craftShot` controls per-unit crafting XP.

`Config.ShopCheckout` defaults to cash/bank accounts, 20 distinct lines and 50 units per supply item. Equipment is limited to one of each per checkout. The server supplies prices and validates the cart.

## Procedures and Customers

| Procedure | Shot | Weight | Offer | Duration | XP |
|---|---|---|---|---|---|
| `small_bbl` | `small_bbl_shot` | 60 | $1,500–$2,500 | 14 seconds | 25 |
| `big_bbl` | `big_bbl_shot` | 40 | $3,000–$5,000 | 20 seconds | 50 |

Weights are relative probabilities. The server picks the request, offer and customer variant. A higher-tier shot can fulfill a lower-tier request: `Config.UpgradeBonus = 1.35` multiplies the offer, and bonus XP is 25% of the shot procedure's XP, rounded down. A lower-tier shot is refused without consumption.

Important `Config.Customer` defaults:

| Setting | Default |
|---|---|
| Spawn distance / table summon range | 12 m / 40 m |
| Phone call | 3 seconds |
| Offer patience / table patience | 30 seconds / 60 seconds |
| Patience after cancelling | 20 seconds |
| Arrival timeout | 35 seconds |
| Leave despawn delay | 20 seconds |
| `networked` / `keepOutfit` | `true` / `true` |

With `keepOutfit = false`, procedure `heal` clothing definitions apply after treatment. This changes NPC clothing; there is no body-mesh replacement. Keep `networked = true` for server patient checks and visibility to other players. Local patients skip server entity verification.

## Operation Table and Placement

`Config.OperationTable` selects the included `gs_stretcher` model, fold/rail clips, patient scene, surgeon scene, syringe attachment and cinematic camera. Keep the included origins, pelvis offsets and clip names together when using the supplied animations.

For a base-game table, change `model`, set `anim = false`, `scene = false` **and `surgeon = false`**, then adjust fitted-mode `lieHeight` and `flipPatient`. Fallback poses come from `data/poses.lua`.

`Config.OperationTable.surgeon.camera` controls dolly shots, field of view, depth of field, blends and letterbox. Set `camera.enabled = false` to use the gameplay camera. `surgeon.stopKey = 73` is X. The custom surgeon scene runs without a progress bar; fallback procedures use the selected progress adapter.

One table per player is allowed by default. Placer defaults are 10 m maximum distance, 120 m streaming range and movement/rotation/height speeds in `Config.Placer`. Active walk-ins block table packing and moving. Moving at the placement limit requires another table item in the inventory; that item remains in the bag after the move.

## Ranks, Goals and Awards

| Rank | Title | XP | Payout | Speed reduction | Tip chance |
|---|---|---|---|---|---|
| 1 | Intern | 0 | 1.00× | 0% | 0% |
| 2 | Resident | 250 | 1.05× | 5% | 3% |
| 3 | Junior Surgeon | 700 | 1.10× | 10% | 6% |
| 4 | Surgeon | 1,500 | 1.18× | 15% | 9% |
| 5 | Senior Surgeon | 2,800 | 1.25× | 20% | 12% |
| 6 | Attending | 4,800 | 1.33× | 25% | 15% |
| 7 | Chief Surgeon | 7,500 | 1.42× | 30% | 18% |
| 8 | Cosmetic Virtuoso | 11,000 | 1.52× | 34% | 21% |
| 9 | Elite Sculptor | 16,000 | 1.63× | 37% | 23% |
| 10 | Plastic Legend | 23,000 | 1.75× | 40% | 25% |

Keep rank XP thresholds ascending and speed reductions below 1. Tips use `Config.TipRange = { 0.10, 0.25 }` against the offered base after any upgrade. Payouts and tips are rounded down.

The server selects three of five daily goals and two of three weekly goals. Everyone receives the same selection for a UTC day/week; progress and claims are per player. Daily goals reset at UTC midnight and weekly goals at Monday UTC midnight. Goal types are `operation`, `craftshot` and `earn`; operation filters use procedure IDs.

Achievements default to the first procedure, 25 procedures, 25 Big BBLs, 100 procedures, $1,000,000 lifetime earnings and rank 10. Rewards are claimed manually. Reward cash does not add to lifetime procedure earnings. Update locale labels when changing ranks, goals or awards.

## Cooldowns and Logging

`Config.RateLimits` contains server-enforced per-player buckets. Summoning defaults to 20 seconds; leaderboard refresh is 10 seconds with a shared 60-second cache. The leaderboard displays ten players by default.

Set `Config.Webhooks.operations`, `.awards` or `.admin` in **`server/config.lua`**. Empty URLs disable logging. Keep credentials out of `config.lua` because shared scripts are sent to clients.
