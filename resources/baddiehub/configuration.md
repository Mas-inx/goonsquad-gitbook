# Configuration

## Editable Files

The manifest explicitly excludes only these **nine Lua files** from escrow:

| File | Purpose |
|---|---|
| `config.lua` | Public gameplay, branding, adapters, gates, limits and economy |
| `data/businesses.lua` | Business directory |
| `data/events.lua` | Event listings |
| `data/occupations.lua` | Career information |
| `data/activities.lua` | Activity information and waypoints |
| `shared/locale.lua` | Server notification translations |
| `shared/bridge.lua` | Framework, inventory, money, ownership, keys, fuel and properties adapters |
| `server/secrets.lua` | Private bot token and webhook settings |
| `install/items.lua` | Item registration reference |

Core Lua is submitted to Portal for encryption. Compiled/minified NUI files remain necessary runtime assets; NUI is not encrypted by [Cfx Asset Escrow](https://docs.fivem.net/docs/server-manual/asset-escrow/).

## Main Settings

| Setting | Default / purpose |
|---|---|
| `Config.Debug` | `false`; enable temporary console diagnostics |
| `Config.Framework` | `'auto'`; qbox, qb, esx, standalone |
| `Config.Inventory` | `'auto'`; ox_inventory, qb-inventory, qs-inventory, lj-inventory, esx_inventoryhud, none |
| `Config.Notify` | `'auto'`; helper, framework or NUI toasts |
| `Config.ItemImagePath` | `nil`; optional image URL template containing `%s` |
| `Config.Locale` | `'en'`; en, es, de, fr, pt-BR |
| `Config.Branding` | Hub/studio/server names, tagline and welcome behavior |
| `Config.Command` | `'baddiehub'` |
| `Config.Keybind` | Disabled; optional rebindable F7 mapping |
| `Config.Claims.scope` | `'account'`; one pack and vehicle per Rockstar license |

Use `'character'` for separate character claims. Profiles, friendships, DMs and season XP use the framework character identifier; standalone uses the available player identifier. Old character claim records are also checked when account scope is enabled. Changing scope does not migrate all historical claims into account-wide records.

## Access Gate

`Config.Gate.mode` accepts `'gender'`, `'discord'`, `'either'`, `'both'` or `'none'`. Default is gender. Gender checks use staff overrides first, then framework gender, then configured female models. A male framework verdict denies access even with a female ped. Default allowed model is `mp_f_freemode_01`. With `sticky = true`, a verified female model is remembered for later custom peds; it does not override male framework gender. `/fhgender <id> clear` clears the stored override and remembered model.

Discord modes need guild ID, role IDs and a server-only bot token. Definitive role results cache for ten minutes by default; transient API failures use a cached result briefly. Role changes therefore may not take effect immediately.

## Social Limits and Moderation

Profiles default to names of 3–20 characters, 160-character bios and eight presets. Item avatars are identifiers, not proof of item ownership. HTTPS avatars default to the configured image-host allowlist.

Friend limits default to 100, five requests per minute and ten minutes before re-requesting after a decline. DMs are friends-only, maximum 500 characters with a one-second cooldown and 100-message history. Lounge chat defaults to 300 characters, a 2.5-second cooldown and 60 history messages. `wordFilter` is empty by default; choose mask or reject and supply your words.

Transport defaults to 40 callbacks per ten seconds, with extra cooldowns for opening, search, history and profile saves. Moderators come from configured ACEs or framework groups; administrative commands require the admin ACE or their specific command ACE.

## Starter Rewards and Vehicle

The pack gives $2,500 cash and one Welcome Box. Box contents live in `Config.Items.welcomeBox.contents`. Inventory capacity and item definitions must suit your economy.

The default vehicle is a Karin Sultan, plate prefix `VR`, full fuel and garage `pillboxgarage`. `enabled` and `show` must both be true. A detected garage is required by default. Resource detection does not verify its database schema or garage identifier; adapt the open bridge for custom schemas. The client proposes a road point; the server restricts it to 15 m and creates the vehicle in the player's routing bucket.

## Season and City Directory

The ten cumulative XP thresholds are 100, 250, 450, 700, 1000, 1400, 1900, 2500, 3200 and 4000. Configure ordered thresholds and cash/item rewards in `Config.Season.levels`. Members receive 100 XP once per server calendar day; chat grants 5 XP for messages of at least eight characters, capped at 50 XP per ten-minute window. A Lounge Pass gives 150 XP.

There is no automatic season rollover. Back up data and plan any database reset before changing rewards already claimed by players. Directory images use local `assets/...` paths or HTTPS URLs; optional `vector3` coordinates enable GPS. Replace sample listings and advertised prices, discounts and schedules with your server's actual content, then restart the resource.

## Private Settings

Use `server/secrets.lua` or private `set` convars, never replicated `setr` values:

```cfg
set gs_baddiehub_bot_token "YOUR_BOT_TOKEN"
set gs_baddiehub_claims_webhook "YOUR_CLAIMS_WEBHOOK"
set gs_baddiehub_chat_webhook "YOUR_CHAT_WEBHOOK"
```

Convars override inline values. Chat mirroring also requires `Config.Chat.discordMirror.enabled = true`. Claim logs contain member name, character identifier and server ID; send them to a staff channel. Keep credentials out of shared configuration and the web interface. Blank values disable the relevant outgoing integration.
