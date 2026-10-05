# API & Exports

OnlyShots provides three gameplay exports on the **server**. Call them from trusted server code with a connected player's server ID. Start your integration after `gs-onlyshots` and optionally declare the dependency in its manifest:

```lua
dependency 'gs-onlyshots'
```

Allow startup initialization to finish before querying players. Framework and persistence setup run asynchronously at resource start.

---

## GetPlayerStats

```lua
local stats = exports['gs-onlyshots']:GetPlayerStats(source)
if stats then
    print(('Rank %d | XP %d | Earned $%d'):format(
        stats.rank, stats.xp, stats.earned
    ))
end
```

Returns the player's statistics snapshot or `nil` when a player identifier cannot be resolved. The export loads the stats row if needed. Treat the returned data as read-only; editing it is not a supported way to update progress.

| Field | Type | Meaning |
|---|---|---|
| `rank` | number | Current rank level, 1–10 by default |
| `xp` | number | Cumulative XP |
| `sales` | number | Successful single-photo NPC sales |
| `bundleSales` | number | Successful collection NPC sales |
| `photos` | number | Lifetime photos saved |
| `earned` | number | Lifetime NPC earnings including tips and viral payouts |
| `followers` | number | Current followers |
| `maxFollowers` | number | Highest follower count reached |
| `viral` | number | Viral NPC sales |
| `tips` | number | Number of successful sales receiving tips |
| `rejections` | number | NPC refusals |
| `thefts` | number | NPC thefts |
| `recovered` | number | Stolen items recovered |
| `locations` | string array | IDs of distinct photographed eligible hotspots |

The snapshot does not include wallet balance, inventory items, challenge records or achievement records. `earned` excludes player trades and challenge/achievement cash rewards.

---

## AddXp

```lua
exports['gs-onlyshots']:AddXp(source, 100)
```

Adds positive XP, marks stats for persistence, updates rank if thresholds are crossed, and evaluates achievements. The amount is converted to a number and rounded down; zero, negative and non-numeric values do not grant XP. The export returns no success value.

This is a trusted server action and does not run the creator model/role gate. Validate the reason and amount in your own server code. Never forward an unrestricted client-supplied amount to it.

`AddXp` sends rank/achievement notifications when applicable, but does not directly push a complete refreshed app state. The next normal state request, such as reopening the app, reflects the changed XP.

---

## HasCamera

```lua
local hasCamera = exports['gs-onlyshots']:HasCamera(source)
```

Returns a boolean. External inventories are checked for the configured camera item; internal inventory mode checks the saved ownership flag. Camera ownership alone does not establish model/role access or open a camera session.

There is no public camera-grant, money-grant, force-sale or client app-opening export in this version. Register items through the supported adapters and use the supplied gameplay commands.

---

## Inventory Item Hooks

The server exports `useCamera`, `usePhoto` and `useBundle` are **inventory lifecycle hooks**, intended for the supplied ox_inventory definitions:

```lua
server = { export = 'gs-onlyshots.useCamera' }
server = { export = 'gs-onlyshots.usePhoto' }
server = { export = 'gs-onlyshots.useBundle' }
```

The inventory calls these with `(event, item, inventory)`. The hook acts on `'usingItem'` and uses `inventory.id` as the player source. They are not exports taking only a player ID. Camera use validates the normal creator gate and ownership; photo/collection use opens the metadata viewer.

QB, Qbox and ESX framework usable items are registered through `server/bridge.lua`. For a different inventory's hook shape, adapt the open bridge rather than calling an internal network event to bypass its item-use workflow.

---

## Internal Events and Transport

Network events retain the `gs_onlypics:server:` and `gs_onlypics:client:` prefixes used by the implementation. They handle requests, image transfers, camera sessions, sales, theft recovery, trades and state updates. They are internal protocols rather than a public integration API.

The JavaScript `uploadFile` export is an internal image-upload transport called by photo storage. Use [Custom Image Hosts](integrations.md#custom-image-hosts) to configure compatible hosting.

Direct database writes do not refresh the player's live stats cache or the leaderboard. Prefer the gameplay exports and open adapters for supported integration; do not rename tables or events to match a different prefix.
