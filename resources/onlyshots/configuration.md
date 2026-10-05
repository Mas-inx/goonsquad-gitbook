# Configuration

Gameplay settings live in `Config.lua`. Credentials and JSON fallback settings live in `server/sv_config.lua`, which is loaded only on the server. Restart `gs-onlyshots` after changing Lua settings or an adapter.

## Editable Files

The release manifest explicitly leaves these Lua files open under Asset Escrow:

| File | Purpose |
|---|---|
| `Config.lua` | Gameplay, commands, branding, access, economy, camera and locations |
| `server/sv_config.lua` | Upload credentials, role credentials, activity feed and JSON fallback |
| `shared/locales.lua` | Notifications, help text and item labels in five languages |
| `data/ranks.lua` | Rank thresholds, payout multipliers and collection limits |
| `data/poses.lua` | Camera pose IDs, animation dictionaries, clips and flags |
| `data/challenges.lua` | Daily and weekly pools, achievements, goals and rewards |
| `shared/bridge.lua` | Framework, inventory and interaction detection |
| `client/bridge.lua` | Notifications, progress, target interaction and client helpers |
| `server/bridge.lua` | Character identity, names, money, usable items and access checks |
| `server/inventory.lua` | Inventory reads, writes, metadata and capacity checks |
| `server/db.lua` | Database adapter, tables and JSON persistence |
| `install/items.lua` | Copyable inventory item registration examples |

The interface lives in `web/index.html`, `web/style.css`, `web/app.js` and `web/i18n.js`, with artwork in `web/img/`. The escrow allowlist contains only Lua files.

---

## General

| Option | Default | Description |
|---|---|---|
| `Config.Locale` | `'en'` | `en`, `es`, `de`, `fr` or `pt-BR` |
| `Config.Debug` | `false` | Extra F8 and server-console logging |
| `Config.Framework` | `'auto'` | Detect Qbox, QB, ESX, then standalone; overrides: `'qbox'`, `'qb'`, `'esx'`, `'standalone'` |
| `Config.Inventory` | `'auto'` | Detect ox, qb, qs, lj, then internal; overrides use resource names or `'internal'` |
| `Config.Target` | `'auto'` | Detect `ox_target`, then `qb-target`; `'none'` uses E prompts |
| `Config.Notify` | `'auto'` | Prefer `ox_lib`, then framework, then UI; overrides: `'ox_lib'`, `'framework'`, `'nui'` |
| `Config.Progress` | `'auto'` | Prefer available `ox_lib` progress helpers, otherwise UI; overrides: `'ox_lib'`, `'nui'` |
| `Config.MoneyAccount` | `'cash'` | Use `'cash'` or `'bank'`; ESX maps non-bank to its cash wallet |
| `Config.OpenKey` | `'F7'` | Default studio keybind; players can rebind it in FiveM settings |
| `Config.RateLimit.requestsPerSecond` | `8` | Per-player request-bus budget |

Forced integration values must match a working adapter. See [Integrations](integrations.md) for startup detection and custom adapters.

`Config.Brand` contains `name = 'OnlyShots'`, `by = 'by Goonsquad'`, `tagline = 'Exclusive creator studio'` and `studio = 'Goonsquad Studios'`. The app uses the brand name, signature and studio name. `tagline` is available in the config but the current interface does not read it.

`Config.Items` maps `camera`, `photo` and `bundle` to their inventory names. Defaults are `gs_camera`, `gs_photo` and `gs_bundle`.

### Commands

`Config.Commands` defaults to `sell = 'sellshots'`, `studio = 'onlyshots'`, `camera = 'camera'`, `stats = 'stats'`, `top = 'top'`, `goals = 'goals'` and `awards = 'awards'`. Set a value to `false` to disable that chat command. Studio and trade key mappings remain separate commands. See [Usage](usage.md#commands-and-keybinds).

---

## Creator Access

```lua
Config.Gate = {
    femaleOnly = true,
    models = { 'mp_f_freemode_01' },
    discordRole = false,
}
```

The model check uses the player's current ped model, rather than a job or framework gender field. Add custom accepted model names to `models`, or set `femaleOnly = false` to remove the model restriction. The role requirement is an independent check.

Creator checks apply to camera purchase, camera use, photo saves, collection creation and starting NPC sales. Browsing and buying player collectibles are available without passing the creator gate. The leaderboard follows the model restriction: with `femaleOnly = true`, it filters creator entries using stored flags and current online models.

---

## Boutique

| `Config.Shop` field | Default | Description |
|---|---|---|
| `coords` | `vector4(-608.64, -1130.93, 22.27, 280.43)` | Ped position and heading; use the standing ped's origin height |
| `ped` | `'a_f_y_hipster_02'` | Shop model, with a built-in fallback if unavailable |
| `scenario` | `'WORLD_HUMAN_STAND_MOBILE'` | Idle scenario; use `false` to skip it |
| `price` | `10000` | Camera price in the selected account |
| `distance` | `2.5` | Target and E interaction distance in metres |
| `blip` | `{ enabled = true, sprite = 184, color = 25, scale = 0.8 }` | Boutique map marker |

The server separately requires purchases within 12 metres of the configured shop. Move `coords` when replacing the boutique location.

---

## Camera and Shot Types

| `Config.Camera` field | Default | Description |
|---|---|---|
| `fov` | `{ min = 15.0, max = 115.0, default = 50.0, step = 4.0 }` | Zoom limits and scroll step; smaller FOV zooms in |
| `height` | `{ min = -0.6, max = 0.6, step = 0.025 }` | Vertical adjustment in metres |
| `lerpSpeed` | `9.0` | Camera smoothing; higher values respond faster |
| `orbitSpeed` | `6.0` | Mouse sensitivity |
| `maxPitch` | `55.0` | Vertical orbit limit in degrees |
| `captureDelay` | `3000` | Minimum milliseconds between captures, also enforced on the server |
| `maxCaption` | `60` | Server caption limit; the interface input also defaults to 60 |
| `shutterSound` | `true` | Camera sound on capture |
| `allowInVehicle` | `false` | Allow opening photo mode in vehicles |

`Config.PhotoTypes` defines `id`, ped `bone`, camera `distance`, `height`, preset `fov` and `behind` for each shot type. Keep at least one preset. The shipped camera starts at the third preset, full body. Existing shot IDs match translations and placeholder art; coordinate changes are simpler than introducing new IDs.

Poses live in `data/poses.lua`. Each entry has an `id`, animation `dict`, `anim` clip and `flag`. The natural pose has no animation. UI pose labels use `pose_<id>` in `web/i18n.js`.

---

## Image Storage

| `Config.Upload` field | Default | Description |
|---|---|---|
| `storage` | `'sql'` | `'sql'`, `'fivemanage'`, `'discord'`, `'custom'` or `'none'` |
| `resource` | `'screenshot-basic'` | Capture resource exposing `requestScreenshot` |
| `width` | `1280` | Longest image side in pixels after resizing |
| `quality` | `0.84` | JPEG quality; the current UI clamps it to 0.5–0.95 |
| `maxKB` | `450` | Server limit on the complete image data URL string, including base64 encoding |
| `cache` | `150` | Number of SQL images cached on the server |

The UI enforces a minimum resize side setting of 320 and does not enlarge a smaller image. If uploads exceed `maxKB`, lower `width` or `quality`, or raise the limit with the increased transfer and storage size in mind.

SQL images use `shot:<id>` references. They are deleted when their photo or collection is sold to an NPC or permanently lost after an escaped theft. Player trades preserve the image. Hosted images use HTTPS URLs and are not deleted by OnlyShots when an item is consumed. Changing storage affects new captures; existing metadata retains its saved reference.

The SQL mode uses memory for images if no database adapter is detected. JSON fallback does not save these images. For host setup and response formats, see [Integrations](integrations.md#image-storage).

---

## Pricing

| `Config.Pricing` field | Default | Description |
|---|---|---|
| `photo` | `{ min = 350, max = 650 }` | Random base value for a capture |
| `typeMultiplier` | Face `1.00`, chest `1.10`, body `1.15`, rear `1.10`, feet `0.90` | Value multiplier per shot type |
| `captionBonus` | `0.05` | Extra capture value for a non-empty sanitized caption |
| `bundleMultiplier` | `1.45` | Collection value multiplier |
| `bundleSizeBonus` | `0.04` | Extra bonus for each photo above the minimum collection size |
| `tradeMultiplier` | `1.25` | Player trade price multiplier after rank and hotspot bonuses |

The server computes values as follows, rounding monetary results down:

```text
photo value = random base × type multiplier × caption factor
caption factor = 1.05 with a caption, otherwise 1.00
collection value = sum(photo values) × 1.45 × (1 + 0.04 × (count - minimum))
collection hotspot multiplier = average(photo hotspot multipliers), rounded to 2 decimals
NPC price = stored item value × current creator rank multiplier × stored hotspot multiplier
successful sale total = NPC price × viral factor + tip
player trade price = stored item value × seller rank multiplier × stored hotspot multiplier × 1.25
```

The hotspot bonus is recorded where the photo is saved. Moving to another hotspot to sell does not change it. Rank uses the creator's current rank at the time of sale.

---

## NPC Sales and Thieves

| `Config.Sale` field | Default | Description |
|---|---|---|
| `odds` | `{ success = 75, reject = 15, steal = 10 }` | Outcome weights normalized by the server |
| `cooldown` | `45` | Seconds between buyer calls |
| `pendingTimeout` | `150` | Seconds before an unresolved buyer request is voided |
| `spawnDistance` | `{ min = 22.0, max = 38.0 }` | Buyer spawn search distance |
| `maxElevation` | `4.0` | Allowed height difference for the normal spawn search |
| `walkTimeout` | `30` | Seconds before a stuck buyer is moved closer |
| `cancelDistance` | `60.0` | Distance from the buyer that cancels the sale |
| `tip` | `{ chance = 20, min = 100, max = 600 }` | Percentage tip chance and amount range |
| `viral` | `{ chance = 4, multiplier = 8.0 }` | Percentage viral chance and sale-price multiplier |
| `followers` | Sale `5–25`, collection `20–60`, viral `+500`, rejection loss `5–15`, theft loss `10–30` | Follower changes; followers cannot go below zero |
| `buyerModels` | Twelve supplied models | Random buyer ped pool; keep it non-empty |

Tips and viral rolls happen on successful sales. Tips are added after the viral price multiplier. A buyer call starts the cooldown even if the outcome is rejection or cancellation.

| `Config.Thief` field | Default | Description |
|---|---|---|
| `window` | `60` | Chase duration in seconds, followed by a short loot grace period |
| `lootDistance` | `2.5` | Target/E search distance in metres |
| `lootDuration` | `4000` | Search progress duration in milliseconds |
| `health` | `200` | Buyer/thief health |
| `runSpeed` | `1.15` | Thief move-rate override |
| `serverValidate` | `true` | Require the synchronized thief to be dead and near the player when recovering |

Keep server validation enabled for normal operation. Inventory failures during recovery leave the theft record available for a retry within the recovery window.

---

## Ranks, Collections and XP

`Config.Bundle.min = 3` sets the collection minimum. The maximum comes from the creator's rank in `data/ranks.lua`; keep every rank's limit at least as large as the minimum.

| Rank | Cumulative XP | Payout multiplier | Maximum photos |
|---|---:|---:|---:|
| Rookie | 0 | 1.00 | 3 |
| Amateur | 500 | 1.08 | 3 |
| Hobbyist | 1,500 | 1.16 | 4 |
| Influencer | 3,200 | 1.25 | 4 |
| Model | 5,800 | 1.34 | 5 |
| Muse | 9,000 | 1.43 | 6 |
| Icon | 13,000 | 1.52 | 7 |
| Star | 18,000 | 1.61 | 8 |
| Superstar | 24,500 | 1.70 | 9 |
| Legend | 32,000 | 1.80 | 10 |

Rank `xp` values are cumulative and should increase in order. `multiplier` changes payouts and `bundleSize` changes collection capacity. The shipped translations contain ten `rank_<level>` labels.

`Config.XP` defaults are: photo `5`, single sale `25`, collection sale `40` plus `10` per included photo, viral `150`, tip `10`, first hotspot capture `75` and recovery `30`. Creating a collection does not itself award sale XP. Challenges and achievements define their own XP rewards.

---

## Hotspots

Each `Config.Locations` entry defines `id`, `name`, `coords`, `radius`, `multiplier`, required `rank` and `blip` sprite/colour.

| ID | Location | Rank | Radius | Multiplier |
|---|---|---:|---:|---:|
| `vinewood` | Vinewood Sign | 1 | 60 m | 1.15 |
| `pier` | Del Perro Pier | 2 | 70 m | 1.30 |
| `observatory` | Galileo Observatory | 4 | 60 m | 1.50 |
| `skyline` | Maze Bank Rooftop | 6 | 45 m | 1.75 |
| `summit` | Mount Chiliad Peak | 8 | 80 m | 2.00 |

Rank and hotspot bonuses stack. A Legend capture with a 2.00 hotspot multiplier receives a 3.60 combined rank/location factor before viral and collection bonuses.

`Config.LocationBlipsFemaleOnly = true` limits hotspot blips to accepted models and `Config.LocationBlipScale = 0.85` sets their size. Rank locks apply to bonuses; blips are not hidden individually by rank. The location check uses 3D distance and the first eligible match, so avoid overlapping hotspot spheres unless that ordering is intentional.

---

## Challenges, Achievements and Leaderboard

`Config.Challenges.dailyCount = 3` and `weeklyCount = 2` control how many entries are picked from `data/challenges.lua`. Everyone receives the same active set. Daily resets occur at 00:00 UTC; weekly resets occur Monday at 00:00 UTC. Changing the server's local timezone does not change rotation.

Challenge definitions use `id`, progress `type`, `goal`, `cash` and `xp`. Supported progress types are `photos`, `hotspot_photos`, `sales`, `bundles`, `earn`, `tips`, `followers`, `viral` and `recover`. Single sales and collection sales have separate counters. Rewards must be claimed before the active period ends.

Achievements in the same file compare lifetime `stat` values to `goal` and award `cash` and `xp` automatically. The `locations` stat counts unique photographed hotspots. Keep IDs stable so saved progress and awards retain their meaning. Adjust translation labels/descriptions alongside definition changes: Lua uses `ch_<id>` and `ach_<id>`; the UI also uses `_desc` keys in `web/i18n.js`.

`Config.Leaderboard.maxEntries = 10` sets the list size and `refreshCooldown = 30` limits forced refreshes in seconds. Rankings use lifetime NPC-sale earnings, including tips and viral payouts. Cash from challenges, achievements and player trades does not increase that earnings counter.

---

## Player Trades

`Config.Trade` defaults to `enabled = true`, `key = 'H'`, `maxDistance = 6.0`, `offerTimeout = 30` seconds and `cooldown = 10` seconds. The buyer aims at a player and sends an offer for their newest creator-owned collection, or newest photo when no collection is available.

The server recalculates the price and checks the item, money and range on acceptance. The creator's `owner` metadata is retained; collector identity is added to the transferred copy. Collectors can view purchases but cannot sell another creator's work to NPCs or use it in their own collections.

---

## Server-Only Settings

Set these in `server/sv_config.lua`:

| Setting | Default | Purpose |
|---|---|---|
| `ServerConfig.Upload.fivemanage.token` | `''` | Owner's upload API key |
| `ServerConfig.Upload.fivemanage.url` | `https://api.fivemanage.com/api/v3/file/base64` | Base64 upload endpoint |
| `ServerConfig.Upload.discord.webhook` | `''` | Photo image upload webhook |
| `ServerConfig.Webhook.enabled` | `false` | Enable the separate saved-photo activity feed |
| `ServerConfig.Webhook.url` | `''` | Activity-feed webhook URL |
| `ServerConfig.Webhook.username` | `'OnlyShots'` | Activity-feed sender name |
| `ServerConfig.Webhook.color` | `13491257` | Embed colour as a decimal integer |
| `ServerConfig.Discord.guildId` / `roleId` / `botToken` | `''` | Credentials for the optional role requirement |
| `ServerConfig.Discord.cacheMinutes` | `10` | Role-result cache duration |
| `ServerConfig.JsonFallback.enabled` | `true` | Load JSON and enable its periodic save loop without a database |
| `ServerConfig.JsonFallback.file` | `'data/store.json'` | JSON save path relative to the resource |
| `ServerConfig.JsonFallback.saveInterval` | `60` | JSON periodic save interval in seconds |

The activity feed is independent of photo hosting. It includes creator, caption, type, pose and hotspot, and attaches an image only when the photo has an HTTPS URL. SQL `shot:<id>` images are not attached to the feed.

Role access requires the player's Discord identifier and a successful guild-member lookup containing the configured role. Missing credentials or lookup failure denies gated creator actions.

The stats cache also flushes once per minute and saves on disconnect/resource stop. Disabling `JsonFallback.enabled` disables JSON loading and its dedicated timer; other stats/resource-stop flushes can still write the in-memory store, so do not use it as a read-only-storage switch.

## Updating and Backups

Preserve your edited configuration, adapters, item definitions and translations when updating. Back up `gs_onlypics_stats`, `gs_onlypics_items` and `gs_onlypics_shots`, plus your external inventory's saved item metadata. In JSON mode, back up `data/store.json`. Keep the existing table names when updating so saved progress remains accessible. Changing the persistence backend does not automatically migrate existing data.
