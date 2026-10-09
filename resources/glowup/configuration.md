# Configuration

Edit `config.lua` for gameplay settings and `server/config.lua` for webhook URLs. Restart `gs-glowup` after editing Lua configuration or adapter files.

## Editable Files

The escrow ignore list contains only these Lua files:

| File | Purpose |
|---|---|
| `config.lua` | Integrations, commands, access, equipment, sales, economy and progression |
| `server/config.lua` | Server-only webhook URLs |
| `data/materials.lua` | Supplier materials, equipment and purchase prices |
| `data/products.lua` | Cosmetic definitions, prices, crafting/application times, animations and makeup overlays |
| `data/recipes.lua` | Ingredients and optional recipe overrides |
| `data/poses.lua` | START gesture, buyer gestures and thief search animation |
| `data/goals.lua` | Daily/weekly goal pools, filters and rewards |
| `data/awards.lua` | Permanent achievement conditions and rewards |
| `shared/bridge.lua` | Integration detection and optional helper loading |
| `client/bridge.lua` | Notifications, progress and target interactions |
| `server/bridge.lua` | Identity, money, inventory, usable items and framework lifecycle hooks |
| `server/storage.lua` | SQL/JSON persistence adapter |
| `server/import.lua` | Optional legacy-data conversion |
| `install/items.lua` | Copyable inventory definitions |

`locales/*.json`, inventory icons and interface assets remain available without Lua escrow entries. Core gameplay logic stays outside the ignore list.

---

## General Settings and Integrations

| Setting | Default | Effect |
|---|---|---|
| `Debug` | `false` | Additional client/server diagnostic prints |
| `BrandName` | `'GlowUp'` | Interface brand and webhook sender |
| `Locale` | `'en'` | Default locale; saved player preference takes priority |
| `Currency` | `'$'` | Display symbol; does not convert values |
| `Framework` | `'auto'` | `'qbox'`, `'qb'`, `'esx'` or `'standalone'` |
| `Inventory` | `'auto'` | `'ox'`, `'qb'`, `'qs'`, `'lj'`, `'esx'` or `'none'` |
| `Target` | `'auto'` | `'ox'`, `'qb'` or built-in `'none'` |
| `UseOxLib` | `'auto'` | Allow optional helper loading; `false` disables it |
| `Notify` / `Progress` | `'auto'` | Set `'nui'` to force the themed interface versions |
| `Storage` | `'auto'` | `'mysql'` or `'json'`; SQL requires oxmysql |
| `MoneyAccount` | `'cash'` | Sale/reward account; use `'bank'` if desired |
| `SaveInterval` | `60` | Dirty-profile autosave seconds, with a minimum of 15 |
| `Standalone.startingCash` | `25000` | Initial wallet for new standalone profiles |

Changing the starting wallet does not overwrite existing wallets. Shop checkout chooses cash or bank explicitly when the framework supports them. Framework and inventory adapters can be selected independently, but the combination must expose the APIs used by the bridge. See [Integrations](integrations.md).

## Access and Commands

`Config.FemaleOnly = true` restricts purchasing, placement, crafting and selling to models in `Config.FemaleModels`, initially `{ 'mp_f_freemode_01' }`. This checks the ped model rather than a job or framework gender field. Set it to `false` or add your custom model names.

`Config.SharedEquipment = false` allows only the owner to craft at a mixer. Set it to `true` for shared crafting. Packing equipment remains owner-only.

Rename or disable any `Config.Commands` entry. Set a command to `false` to omit registration. The default command list is in [Usage](usage.md#commands). There are no resource-defined key mappings or release debug commands.

---

## Supplier and Equipment

`Config.Shops.studio` controls the supplier label, ped model, `vec4` location/heading, scenario and blip. To add another shop ID, also add its `target.browse_<id>` and `blip.<id>` language keys.

`Config.Items` names the two equipment items. `Config.Workbench` and `Config.Mixer` define streamed models, interaction distances and per-owner placement limits. Their defaults are one of each. Using a spare equipment item after reaching the limit moves the oldest deployed unit instead of consuming the spare.

The supplied custom model offsets are coordinated:

- `Mixer.onTable`: machine position/heading, snap tolerance, footprint and player standing position
- `Mixer.camera`: START-button close-up, wide shot and transition time
- `Mixer.button`: interaction centre, radius, normal and glow colour
- `Workbench.anim`: unfold/fold dictionaries, clips and durations
- `Mixer.anim`: dispensing dictionary, clip and original cycle duration

Keep these aligned with the streamed props. Packing a workbench also returns its mixer. Moving the workbench carries an attached mixer along.

`Config.Placer` controls placement reach (10 m), movement/rotation speeds, height step, Shift multiplier, ghost opacity and client streaming range (120 m).

## Items and Recipes

The supplier catalogue is `Materials` plus `MaterialsEquipment` in `data/materials.lua`. Product sale prices and descriptions are in `data/products.lua`.

Recipes are keyed by the product item name. For example:

```lua
Recipes = {
    gs_lipstick = {
        ingredients = {
            pigment_rose = 1, beeswax = 1,
            shea_butter = 1, lipstick_tube = 1,
        },
    },
}
```

This illustrates one recipe; edit the existing table rather than replacing all recipes with it. Optional `output`, `time`, `xp` and `label` fields override the defaults. Without overrides, output is one, time comes from `product.craftTime`, and XP is `Config.XP.craftProduct + product.xp`. Rank speed reduces per-unit time; batch size multiplies time and ingredients.

Renaming a material requires matching ingredient keys and inventory/image definitions. Renaming a product requires matching recipe keys, goal filters and language/image entries. The interface's product grouping and equipment shortcut names also use the shipped item IDs; for broad custom item additions, adapt the interface alongside the open Lua data.

---

## Cosmetic Application

```lua
Config.ProductUse = {
    enabled = false,
    consume = true,
    applyLook = true,
}
```

Enable application to register cosmetic use callbacks and show **Use 1** in the crafting interface. Successful application consumes one item by default; cancellation leaves it intact. `applyLook` enables native head-overlay changes on female/male freemode peds.

Looks are temporary and are replaced when the appearance system reloads a saved look. Products without a `look` definition, such as the brush set and setting spray, still animate but do not apply a timed gameplay buff. The setting spray's descriptive flavour text does not implement a sixteen-hour effect.

Product `use` entries define candidate animations and sequences, hand props and optional reach/wobble behaviour. `look` entries define overlay style/opacity/RGB or fade an existing overlay. See [Integrations](integrations.md#appearance).

## Selling

| Setting | Default |
|---|---|
| Success / reject / steal | 70% / 20% / 10%, totaling 100 |
| Price variation | 0.80–1.20 × base sale price |
| Single-sale cooldown | 45 seconds |
| Minimum sale duration | 6,000 ms |
| Search duration | 5,000 ms |
| Stolen-product expiry | 300 seconds from sale creation |
| Unresolved-sale timeout | 120 seconds |
| Pop-up buyer cooldown | 20 seconds |
| Pop-up units per buyer | 1–3 of one carried product |

`Config.Selling` also controls buyer models, spawn distance, arrival/despawn timing, networked NPCs, thief movement rate and client-side downed-health threshold. Keep probabilities at a total of 100 and price variation ordered low to high.

Per-unit price is rounded down after applying variation and the current rank payout multiplier, then multiplied by units. Successful sales can receive a tip of 10–25% of that sale total when the rank tip roll succeeds.

## Progression and Rewards

`Config.Ranks` must stay ordered by ascending XP. Default thresholds are 0, 250, 700, 1,500, 2,800, 4,800, 7,500, 11,000, 16,000 and 23,000. Final-rank perks are 1.75× payouts, 40% faster crafting and a 25% tip chance.

`Config.XP` gives base crafting/sale XP plus each product's bonus, and recovery XP. `Config.Goals` selects three of five daily and two of four weekly definitions. Daily resets use UTC midnight; weekly resets use Monday at 00:00 UTC. Goal progress is per player and completed rewards must be claimed.

Achievements in `data/awards.lua` unlock automatically; rewards must be claimed once. `Config.Leaderboard` defaults to ten rows and a 60-second shared cache. Lifetime earnings track successful NPC sales and tips, excluding goal/achievement cash rewards.

`Config.RateLimits` contains per-player request cooldowns in milliseconds. Sale cooldowns use seconds in `Config.Selling`.

## Webhooks and Migration

Set the `sales`, `awards` and `admin` URLs in **`server/config.lua`**. Empty strings disable the channels. Keep credentials in that server-only file; `config.lua` is shared with clients.

`Config.Import.enabled` is `false` for fresh releases. Its resource name/table prefix select the source for a one-time legacy studio import. See [Integrations](integrations.md#legacy-studio-import) for exactly what is copied and when.
