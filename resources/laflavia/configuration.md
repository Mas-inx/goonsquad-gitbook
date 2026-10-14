# Configuration

## Editable Files

| File/group | Purpose |
|---|---|
| `config/config.lua` | Adapter selection, keys, interaction, pouring, quality, metadata, sessions, sync, menu, effects and sounds |
| `config/job.lua` | Job/grades, boss-office access, society settings and lift floor rule |
| `config/models.lua` | Bottle/glass/tool/garnish models, inventory items, liquid definitions and capacities |
| `config/recipes.lua` | Drink steps, quantities, glasses and reward items |
| `config/drinks.lua` | Thirst, stress, alcohol units, sober rate and intoxication stages |
| `config/bars.lua` / `config/cameras.lua` | Station positions, surface geometry, restrictions and camera presets |
| `config/icemachine.lua` | Machine locations, carry limits, cameras, dispensing and serving timing |
| `config/orders.lua` | Tablet menu/prices, payment, limits, expiry, positions and shared DUI layouts |
| `config/supplies.lua` | Catalog/prices, order limits, supplier pickup and courier delivery |
| `config/storage.lua` | Shared stash location, capacity and access |
| `config/locale.lua` | English Lua messages and custom locale tables |
| `bridge/framework/*.lua`, `bridge/inventory/*.lua`, `bridge/target.lua`, `bridge/notify.lua` | Integration adapters |
| `shared/elevator.lua` | Lift integration export |
| Three Lua files under `install/` | Customer item/job definitions |

These 27 Lua files are explicitly excluded from escrow. Core recipe normalization, validation, session handling and gameplay stay protected. SQL, icons and browser assets do not need Lua escrow exclusions.

## Adapter Selection

```lua
Config.Debug = false
Config.Locale = 'en'
Config.Framework = 'auto'
Config.Inventory = 'auto'
Config.Target = 'auto'
Config.Notify = 'auto'
```

| Setting | Accepted choices |
|---|---|
| Framework | `auto`, `qbox`, `qbcore`, `esx`, diagnostic `standalone` |
| Inventory | `auto`, `ox_inventory`, `qb-inventory`, `ps-inventory`, `lj-inventory`, `qs-inventory`, `framework` |
| Target | `auto`, `ox_target`, `qb-target`, `none` |
| Notify | `auto`, `ox_lib`, `framework`, `nui` |

Auto framework selection prefers Qbox, then QBCore, then ESX. Inventory detection prefers a supported dedicated backend before framework item functions. Restart after changing adapters.

`Locale` selects a table in `config/locale.lua`; missing tables/keys fall back to English. Preserve formatting placeholders such as `%s` and `%d`. Only English is included. Browser presentation copy and recipe/model labels also contain English text; adding a Lua locale alone does not translate the entire interface.

## Gameplay and Quality

| Setting | Default | Purpose |
|---|---|---|
| `NativeInteraction.key` / `altKey` | 38 / 47 | E / G prompts when no target is active |
| `NativeInteraction.distance` | 1.6 | Native prompt activation range |
| `Keys.grab` / `secondary` | 24 / 25 | Left/right mouse for holding and rotating |
| `Keys.confirm` / `cancel` | 38 / 194 | E / Backspace |
| `Pour.tolerance` | 0.15 | Fifteen-percent target tolerance |
| `Pour.overPourStop` | 1.8 | Stops at 180 percent of target volume |
| `Quality.minimumToServe` | 0 | No quality rejection by default |
| `Session.maxDuration` | 600 seconds | Maximum active preparation lifetime |
| `Session.interactDistance` | 4 meters | Server station range check |
| `Session.rateLimitMs` | 400 | Same-action request interval |
| `Sync.range` / `rateMs` | 50 meters / 100 ms | Nearby-player scene range and client update pace |

Quality tiers are Perfect (95+), Excellent (85+), Good (70+), Average (50+) and Poor. Pour, motion and ingredient measurements use configurable weights. Raising `minimumToServe` makes low-quality preparations fail; their available ingredients are consumed. Inventory-capacity/reward delivery failures preserve ingredients instead.

`Metadata` controls label, score/tier, bartender, ingredient list, alcohol, creation time, recipe ID and the tooltip description. Some backends cannot retain all metadata.

## Bottles and Recipes

`Config.BottleConsumption` accepts `volume` (default), `always` (whole bottle) or `never` (must be present). Volume mode uses the model's capacity and item metadata `remaining`. On a backend without writable metadata it falls back to whole-bottle consumption.

Every served drink consumes one `empty_glass` by default. Tool props without an item do not require an inventory tool. Ice is provided by the default machine/bucket setup rather than a purchasable ice item.

To add a drink, define its required models/liquids, recipe steps, reward item, tablet menu price, inventory entry and icons together. Supported step types are place, pour, add, add_ice, garnish, stir, muddle, squeeze, open_bottle, close_shaker, shake, open_shaker, strain, ice_machine and serve. An `ice_machine` step must be last before serving and can occur only once. Serve is appended if missing. Startup prints invalid-recipe diagnostics.

## Stations and Cameras

A station's `coords` are the floor position where the bartender stands. `surface.height` is the counter height above that floor; `surface.forward` offsets its center. Workspace and camera offsets are local to the station's heading. The default left/right stations are at `291.109, -916.582, 57.022` and `292.135, -913.763, 57.022`, heading 250.

Use `Bars[barId].job` for nil/public access, a job name, an array of names, `{ job = 'laflavia', grade = 1 }` or a job-to-grade map. `recipes` can be `all` or a restricted list. `Job.Enabled = false` opens the default bar operations to everyone and disables the job-dependent boss menu/society setup.

## Ice Machine

| Setting | Default |
|---|---|
| Location | `289.396, -919.971, 58.027`, heading 160 |
| Carry timeout / maximum distance | 180 seconds / 60 meters |
| Server use distance | 3 meters |
| Minimum dispensing time | 2 seconds |
| Automatic serve delay | 4,200 ms |
| Model spawn / DUI distance | 60 / 12 meters |

Use `Carry`, `Serve`, `Camera`, `Timing`, `Points` and `Dui` to tune the scene. Screen corners, texture names and click rectangles must match the prop/page. `IceMachine.Enabled = false` removes machine steps when recipes are normalized.

## Customer Orders

The default menu has 12 drinks priced from $6 to $15. `ServicePercent = 5` adds a rounded five-percent fee. Cash and bank are enabled. Limits are 10 per line, 12 lines and three open orders per character.

`RefundOnDelete = true` refunds an uncollected cancelled order to its original account. The customer must be online to receive it; otherwise deletion is refused and the paid order remains. New/unfilled orders expire after 60 minutes; refundable offline orders wait until the owner is online and payment succeeds. Ready orders disappear after 45 minutes without a refund. Partly collected orders are not refunded.

Staff must be behind the configured `counter.splitY` and near the monitor/tray. Keep the origin, heading and split together when relocating. The use distance is 2.5 meters. With ox_inventory, per-order stashes default to 10 slots/20,000 weight; increase capacity if your maximum order cannot fit. `TabletUI` and `TerminalUI` are shared render/hit-test layouts: change both screen geometry and layout coherently.

## Supplies and Storage

Pickup is free; courier delivery adds $25. Cash/bank, both fulfillment modes and 25 catalog entries are enabled. Limits are 20 per line, 15 lines and three open orders per character. `ExpireMinutes = 0` keeps uncollected supplies indefinitely; a positive expiry removes them without a refund.

Pickup defaults to Cypress Flats at `928.98, -1823.4, 29.78`, heading 255.17. Delivery goes to `291.48, -900.58, 29.6`, heading 249.5. A nil pickup vehicle means the supplier stands alone. The scooter route is a client scene; collection authority uses the configured point and buyer identity.

Storage defaults to the shelf at `289.12, -918.15, 58.65`, with 50 slots/250,000 weight and a 3-meter server check. Keep stash IDs stable once items are stored. Disable storage with default ESX inventory or provide a compatible stash adapter.

## Society Account

```lua
Job.Society = {
    enabled = true,
    importLegacyBalance = false,
    account = 'laflavia',
    startBalance = 0,
    orderIncome = 100,
    refundFromSociety = true,
    transactions = 25,
}
```

The society is separate from a banking resource. It receives 100 percent of a paid drink order by default, including service fees. Supply orders are paid by the staff member and do not automatically debit the society. Bosses deposit/withdraw their cash or bank money at the laptop. Withdrawals use a conditional SQL update to avoid overdrawing; refunds can take the society negative if its income has already been spent.

Legacy import is disabled for the new release. Enabling it copies an old `bank_accounts_new` account balance only when creating a missing society account. It does not deduct that old balance, so use it only during a deliberate migration away from the old ledger. It never replaces an existing society balance.

The boss laptop is at `303.75, -906.6, 66.23`; server range is 3 meters. Hire range is 10 meters and offers last 30 seconds. `BossFloor.minGrade = 3` restricts the lift floor. Existing framework boss grades can differ from this floor threshold.

## Drink Effects and Presentation

Effects are derived from poured liquids. Defaults use 10 ml of pure alcohol per game unit, 0.25 units sobered per minute, a maximum of 12 and stages at 1/4/7 units. Death resets intoxication; reconnecting also clears this client-only level. Liquid and recipe overrides can replace thirst/hunger/stress/units.

`Sounds` uses procedural WebAudio by default; file overrides must also be added to the manifest's files. `Effects`, `Animations`, `Menu` and `BarMat` tune visuals. `Config.UsableDrinks.onDrink`, `Orders.OnPaid` and `Supplies.OnPaid` are server hooks documented in [API & Exports](exports.md).
