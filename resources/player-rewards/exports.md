# API & Exports

---

## Server Exports

```lua
exports['gs-playerrewards']:GrantPrime(source)
```
Grants Prime to a player. Returns `boolean`.

```lua
exports['gs-playerrewards']:GetBootstrapTable(source)
```
Returns the full bootstrap table for a player (season, player stats, settings, locale).

```lua
exports['gs-playerrewards']:AddHours(source, hours)
```
Adds playtime hours to a player.

```lua
exports['gs-playerrewards']:SetHours(source, hours)
```
Sets a player's total playtime hours.

```lua
exports['gs-playerrewards']:GetPlaytimeHours(source)
```
Returns a player's total playtime hours.

---

## Tebex Integration

Create a Tebex package command to grant Prime after purchase:

```
gs-prime {identifier}
```

The command handler resolves the player and calls:

```lua
exports['gs-playerrewards']:GrantPrime(source)
```

---

## Server -> Client Events

| Event | Purpose |
|-------|---------|
| `playerrewards:bootstrap` | Full UI state on ledger open |
| `playerrewards:liveState` | Live playtime/prime updates |
| `playerrewards:claimReceipt` | Claim receipt data |
| `playerrewards:primeTebexResult` | Tebex URL or error |
| `playerrewards:purchasePrimeResult` | In-game purchase result |
| `playerrewards:catalogResponse` | Overseer catalog results |
| `playerrewards:spawnVehicle` | Spawn claimed vehicle |
| `playerrewards:open` / `close` | External open/close ledger |
| `playerrewards:notify` | Fallback notification |
| `playerrewards:vehicleGarageWaypoint` | Waypoint for claimed vehicle |

## Client -> Server Events

| Event | Purpose |
|-------|---------|
| `playerrewards:requestBootstrap` | Open ledger |
| `playerrewards:uiState` | UI open/closed state |
| `playerrewards:claim` | Claim a reward |
| `playerrewards:claimAll` | Claim all eligible rewards |
| `playerrewards:saveSeason` | Overseer save season |
| `playerrewards:saveSettings` | Overseer save settings |
| `playerrewards:requestPrimeTebex` | Request Tebex checkout |
| `playerrewards:purchasePrime` | In-game Prime purchase |
| `playerrewards:fetchCatalog` | Catalog for Overseer picker |
| `playerrewards:sessionPing` | Session keepalive |

---

## Framework Bridge

Auto-detected in order: **qbx_core** -> **qb-core** -> **es_extended** -> standalone.

### Inventory Support

| Priority | Resource |
|----------|----------|
| 1 | ox_inventory |
| 2 | qb-inventory |
| 3 | Framework native |

### Vehicle Key Integration

Supported key systems:
- `qb-vehiclekeys`
- `qbx_vehiclekeys`
- `okokGarage`

---

## Claim Flow

1. Client sends `playerrewards:claim` with reward ID.
2. Server validates: reward exists, not claimed, hours met, track access.
3. `Rewards.DeliverReward` handles delivery based on type.
4. Claim logged in `playerrewards_claims` with token.
5. Bootstrap refreshed and receipt sent to client.

**Claim failure codes:** `locked` (hours), `prime` (no Prime), `already` (already claimed), `inventory` (no space).
