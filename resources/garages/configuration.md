# Configuration

GS Garages keeps all configuration in three data-only files. File values are the shipped defaults; nearly every runtime-safe option can also be edited live from the in-game admin panel (see [Administration](administration.md)).

| File | Controls |
|---|---|
| `config/config.lua` | Adapters, routing, fees, features, housing, preview camera, overlays, prompts, admin, rate limits, migrations, and logging |
| `config/garages.lua` | The garage list — every garage is one table entry |
| `config/categories.lua` | Vehicle categories, GTA class mapping, and per-model overrides |

{% hint style="info" %}
Options edited in game are stored in the `gs_settings` table and re-applied over the file defaults on every boot. If a file change appears to have no effect, check whether the same option has a live override — the admin panel marks overridden options with a dot and offers a per-option reset.
{% endhint %}

---

## Adapters

```lua
Config.Framework = 'auto'   -- 'qbox' | 'qbcore' | 'esx' | 'standalone'
Config.Fuel      = 'auto'   -- 'LegacyFuel' | 'ox_fuel' | 'cdn-fuel' | 'ps-fuel' | 'ND_fuel' | 'hyon_fuel' | 'Renewed-Fuel' | 'native'
Config.Keys      = 'auto'   -- 'qbx_vehiclekeys' | 'qb-vehiclekeys' | 'wasabi_carlock' | 'qs-vehiclekeys' | 'MrNewbVehicleKeys' | 'cd_garage' | 'none'
Config.Target    = 'markers'-- 'auto' | 'ox_target' | 'qb-target' | 'markers'
Config.Notify    = 'ui'     -- 'ui' | 'auto' | 'ox_lib' | 'framework'
```

`auto` selects the first supported resource that is already started. `Config.Notify = 'ui'` renders the resource's own styled toasts; `'auto'` uses the framework's notifications instead.

For a fuel, key, or notification resource that is not on the list, use the override hooks at the bottom of `config/config.lua` — see [Override Hooks](#override-hooks).

---

## Garages and Routing

| Key | Default | What it does |
|---|---|---|
| `DefaultGarage` | `'legion_public'` | Where a vehicle with a NULL/unknown `garage` value is considered parked. Freshly dealership-sold cars usually land here. |
| `AnyGarageIfUnknown` | `true` | Unknown-garage vehicles are retrievable from **any** public garage that allows their category, instead of only `DefaultGarage`. |
| `DefaultImpound` | `'impound_lot'` | Depot used when a vehicle is impounded with no destination. |
| `StoreRadius` | `15.0` | Max distance (m) from a garage that a park request is accepted at. Server-validated. |
| `StoreSearchRadius` | `8.0` | How far to look for an owned vehicle to park while on foot. |
| `SpawnPointClearance` | `2.6` | A spawn point counts as blocked if any vehicle is within this radius. |
| `WarpIntoVehicle` | `true` | Seat the player in a vehicle they just took out. |
| `EngineOnRetrieve` | `false` | Leave the engine running on retrieval. |

{% hint style="info" %}
`AnyGarageIfUnknown = true` is why a newly bought car can appear in every public lot until it is parked once. Set it to `false` if you want unknown-garage vehicles pinned to `DefaultGarage` only.
{% endhint %}

---

## Economy

```lua
Config.Fees = {
    retrieve = 0,     -- taking a vehicle out
    impound  = 500,   -- releasing from an impound lot
    transfer = 150,   -- moving a stored vehicle to another garage
}

Config.MoneyAccounts     = { 'cash', 'bank' }  -- tried in order until one covers the fee
Config.AllowSplitPayment = false               -- if false, one account must cover the whole fee
```

Every garage can override the fee table with its own `fees = { ... }` entry.

On standalone servers, `Config.Standalone` is an economy stub: with `chargeFees = false` (the default) all fees are waived. Point its `GetMoney` / `RemoveMoney` / `AddMoney` functions at your own money resource to charge for real.

---

## Impound

```lua
Config.Impound = {
    allowOwnerPay = true,       -- the owner may always buy their own vehicle back
    releaseJobs   = { police = 0, sheriff = 0, bcso = 0 },  -- job = minimum grade
    freeForJobs   = true,       -- release is free for those jobs
    ace           = 'goonsquad.admin',
    releaseTo     = 'default',  -- 'default' moves it to Config.DefaultGarage, 'depot' keeps it stored at the lot
}
```

Players with a listed job (at or above the grade) see **every** impounded vehicle at an impound lot, not just their own, and can release them. Other scripts can impound vehicles through the `ImpoundVehicle` export — see [API & Exports](exports.md).

---

## Features

```lua
Config.Features = {
    favorites    = true,   -- pin vehicles to the top of the list
    nicknames    = true,   -- rename a vehicle (server-validated, max Config.NicknameMaxLength)
    transfer     = true,   -- move a stored vehicle between garages
    giveKeys     = true,   -- hand temporary keys to a nearby player
    sharedAccess = true,   -- persist that share so they keep access
    waypoint     = true,   -- "find my car" GPS marker
    mileage      = true,   -- odometer accumulation
}
```

Shared access is governed by `Config.Shared`: how many people one vehicle can be shared with (`maxPerVehicle`, default 5) and what sharees may do (`allowRetrieve` and `allowStore` default on, `allowTransfer` off).

Mileage sampling is cheap by design — the client samples only while driving an owned vehicle and flushes one delta every 30 seconds, clamped server-side by `maxDeltaPerFlush` as an anti-cheat cap. Display unit is `Config.Mileage.unit` (`'km'` or `'mi'`).

### Vehicle Images

No image pack ships — the interface is typographic and nothing looks missing with images off. To add thumbnails, drop PNGs named after spawn codes into `web/images/` (e.g. `sultan.png`) and set:

```lua
Config.VehicleImages = { enabled = true }
```

Models without a file simply show no image.

---

## Housing

```lua
Config.Housing = {
    enabled    = true,
    provider   = 'auto',    -- force by id, 'custom', or 'none'
    label      = '%s Garage',
    categories = { 'car', 'motorcycle' },
    capacity   = 4,
    keyholders = true,      -- keyholders may use the garage too
    refresh    = 300,       -- seconds between background re-scans
    fees       = { retrieve = 0, transfer = 0 },
}
```

When a supported housing script is running, every house a player owns — or holds keys to — becomes a private garage at that house's own garage spot, with nothing to configure per house. See [Configuration → Housing Integration](integrations.md#housing-integration) for the supported scripts and the custom provider hook.

---

## Preview and Camera

The live 3D preview behind the interface is configured in `Config.Preview`:

- `mode = 'world'` (default) stages the car on a free spawn point of the garage you opened, grounded and lit by the world. `'cell'` uses a hidden showroom cell over the ocean. The preview entity is local-only — nobody else ever sees it.
- `useRoutingBucket = true` isolates the player in a private routing bucket while previewing, so other players and traffic vanish from the shot. Buckets are restored on close, disconnect, and resource stop.
- The camera rig ships five presets (`Hero`, `Low front`, `Profile`, `Rear quarter`, `Detail`) in `Config.Preview.angles`; per-vehicle framing automatically fits bikes and box trucks to the frame alike.
- Flourishes: idle turntable rotation, cinematic spin mode, shallow depth of field, headlight pulse on selection, optional engine idle and door crack.
- `freezePlayer` (on) and `hideRadar` (on) control the stage; `invinciblePlayer` is off by default because it is exploitable.

`Config.Overlay` controls the in-world DUI nameplate that stands behind the previewed car, and `Config.ParkPrompt` / `Config.OpenPrompt` control the world-anchored `E — PARK` and `E — OPEN GARAGE` prompts (distances, key, world size). All of it is editable live from the admin panel.

---

## Interaction and Commands

| Key | Default | What it does |
|---|---|---|
| `Marker` | see file | Fallback marker appearance, draw distance, and prompt key |
| `NearbyDistance` | `60.0` | Distance at which a garage's per-frame work switches off |
| `ScanDistance` | `250.0` | Coarse proximity scan reach; beyond it the thread sleeps a full second |
| `OpenCommand` | `'garage'` | Client command that opens the nearest garage in range |
| `OpenKeybind` | `''` | Optional key mapping (e.g. `'E'`); empty disables |
| `UISounds` | `{ enabled = true, volume = 0.22 }` | Procedural UI sound effects |
| `Currency` | `'$'` | Prefix shown on every price |

A single garage can force the marker fallback for itself with `interaction.type = 'marker'` regardless of `Config.Target`.

---

## Rate Limits

`Config.RateLimit` holds per-player, per-action server cooldowns in milliseconds (retrieve 1500, store 1200, transfer 1500, keys 2000, rename 1000, favorite 400, impound 2000, open 600, mileage 10000). These block spam and double-submit duplication attempts. Raise them if your players are patient; do not disable them.

---

## Logging

```lua
Config.Logs = {
    enabled = false,
    webhook = '',
    events  = { retrieve = true, store = true, transfer = true,
                impoundRelease = true, giveKeys = false, migration = true },
    flushInterval = 5000,
}
```

Events are batched (up to ten embeds per message) so a busy server does not open one HTTP request per action. The webhook URL is server-scoped — it is never sent to any client, including administrators using the live settings editor.

---

## Adding a Garage

Add one table to `config/garages.lua`. No code anywhere in the resource is per-garage.

```lua
{
    id    = 'legion_public',
    label = 'Legion Square Parking',
    type  = 'public',              -- public | house | job | gang | impound

    blip  = { sprite = 357, color = 3, scale = 0.75, shortRange = true, show = true },

    access = {                     -- omit entirely for public garages
        job = nil, minGrade = 0,   -- job garages
        gang = nil,                -- gang garages
        property = nil,            -- house garages, resolved by the housing bridge
    },

    categories = { 'car', 'motorcycle' },   -- {} or nil = anything
    capacity   = nil,                       -- nil = unlimited

    interaction = {
        type   = 'target',         -- 'target' | 'marker' (omit = follow Config.Target)
        coords = vec3(215.8, -810.0, 30.7),
        radius = 1.6,
        ped    = nil,              -- optional: { model, heading, scenario }
    },

    spawnPoints = {                -- tried in order, first free one wins
        vec4(226.5, -801.4, 30.4, 160.0),
        vec4(230.1, -805.2, 30.4, 160.0),
    },

    fees = { retrieve = 0, impound = 500, transfer = 150 },   -- overrides Config.Fees

    -- job/gang garages only: a shared fleet nobody owns
    sharedJobVehicles = {
        { model = 'police', label = 'Cruiser', grade = 0, category = 'emergency' },
    },
}
```

Garages can also be created entirely in game from the admin panel, with the interaction point and spawn bays placed by aiming in the world — see [Administration](administration.md).

---

## Categories

`config/categories.lua` maps GTA vehicle classes onto category ids (`car`, `motorcycle`, `cycle`, `utility`, `air`, `sea`, `emergency`, `military`). Three layers of control:

- `Config.ClassCategories` — GTA class index → category
- `Config.ModelCategories` — per-model overrides for vehicles GTA classes badly (e.g. the blimps)
- `Config.CategoryAliases` — spawn-name keywords that override class 0 misreports

`Config.FallbackCategory` (default `'car'`) covers unknown modded classes. Adding a new category means adding it to `Config.Categories` and adding its icon to the interface icon map.

---

## Override Hooks

The bottom of `config/config.lua` holds the documented escape hatches for resources without a shipped adapter. Each returns `nil`/`false` to fall through to the built-in behavior:

| Hook | Runs on | Purpose |
|---|---|---|
| `Config.GetFuel(vehicle)` / `Config.SetFuel(vehicle, level)` | client | Unsupported fuel resource |
| `Config.GiveKeys(src, netId, plate)` | server | Unsupported key resource — return `true` when handled |
| `Config.NotifyOverride(src, message, kind)` | both | Custom notifications |
| `Config.CanAccessVehicle(src, identifier, row)` | server | Extra ownership rules, e.g. rentals |
| `Config.CanAccessGarage(src, garage)` | server | Extra garage access rules, e.g. property scripts |

These files ship unencrypted, so every hook is editable on an escrowed copy.
