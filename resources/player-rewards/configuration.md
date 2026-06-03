# Configuration

All configuration is in `config.lua`. UI text edits go in `locale.lua`.

---

## Framework

```lua
Config.Framework = 'auto'   -- 'auto' | 'esx' | 'qb' | 'qbx'
Config.AutoDetect = true
```

| Value | Description |
|-------|-------------|
| `'auto'` | Detect from running resources (qbx_core -> qb-core -> es_extended) |
| `'esx'` | Force ESX framework |
| `'qb'` | Force QBCore framework |
| `'qbx'` | Force Qbox framework |

---

## Admin Access

```lua
Config.AdminAce = 'playerrewards.admin'
Config.OverseerRequireAce = true
Config.AdminIdentifiers = {
    'fivem:5415411',
}
```

| Option | Description |
|--------|-------------|
| `AdminAce` | ACE permission name for admin access |
| `OverseerRequireAce` | Require ACE for Overseer panel (disable for dev) |
| `AdminIdentifiers` | Identifier whitelist (alternative to ACE) |

---

## Commands

```lua
Config.Commands = {
    OpenLedger = 'playerrewards',
    GrantHours = 'granthours',
    GrantPrime = 'grantprime',
    RemovePrime = 'removeprime',
}
```

Only the command names can be changed here — logic is fixed.

---

## Keybind

```lua
Config.OpenLedgerKeybind = {
    Enabled = true,
    Key = 'F7',
}
```

Default keyboard shortcut for opening the ledger.

---

## Discord

```lua
Config.Discord = {
    BotToken = '',
    GuildId = '',
    PrimeRoleId = '',
    AutoGrantFromRole = true,
}
```

| Option | Description |
|--------|-------------|
| `BotToken` | Discord bot token. Can be set via server convar: `playerrewards:discord_bot_token` |
| `GuildId` | Discord server ID. Can be set via convar: `playerrewards:discord_guild` |
| `PrimeRoleId` | Role ID that grants Prime. Can be set via convar: `playerrewards:discord_role` |
| `AutoGrantFromRole` | Auto-grant Prime when player opens ledger and has the role |

---

## Operator Portraits

```lua
Config.OperatorPortraitMale = './img/operator.png'
Config.OperatorPortraitFemale = './img/operator-female.png'
```

Gender-based portraits shown in the ledger UI.

---

## Playtime

```lua
Config.Playtime = {
    Enabled = true,
    SaveIntervalMinutes = 5,
    MinimumSessionSeconds = 60,
    UiSyncIntervalSeconds = 15,
}
```

| Option | Description |
|--------|-------------|
| `Enabled` | Toggle playtime tracking |
| `SaveIntervalMinutes` | Minutes between session time flushes to DB |
| `MinimumSessionSeconds` | Minimum session length to count (anti-spam) |
| `UiSyncIntervalSeconds` | Seconds between live hour updates to UI |

---

## Vehicles

```lua
Config.SaveVehiclesToDatabase = true
Config.DefaultGarage = 'pillboxgarage'
```

| Option | Description |
|--------|-------------|
| `SaveVehiclesToDatabase` | Insert claimed vehicles into player_vehicles/owned_vehicles |
| `DefaultGarage` | Default garage ID for new vehicles |

---

## Hours Per Tier

```lua
Config.HoursPerTier = 5
```

Default hours per tier when creating new rewards in Overseer (tier 1 = 5h, tier 2 = 10h, etc.).

---

## Garage Waypoints

```lua
Config.GarageWaypoints = {
    pillboxgarage = {
        label = 'Pillbox Garage',
        x = 215.95,
        y = -810.76,
        z = 30.73,
    },
}
Config.GarageWaypointBannerMs = 10000
```

GPS waypoint + banner shown when a player claims a vehicle reward. Key must match the garage column in `player_vehicles.garage` / `owned_vehicles.parking`.

---

## Image Manifest

```lua
Config.ImageManifest = 'install/img_manifest.json'
```

Fallback image list when the web/img directory scan fails.

---

## Debug

```lua
Config.Debug = false
```

Enables verbose console logging via `Config.Log(msg)`.
