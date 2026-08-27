# API & Exports

The runtime apply layer is deliberately **mod-friendly**:

- A **clean base snapshot** is kept per model (captured from the first untouched instance) and only the fields you changed are written on top of it.
- Overrides apply **once** per vehicle instance (on enter/spawn or on live edit) — there is no per-frame enforcement fight. Any script that sets handling *after* the studio simply wins, so mechanic engine-swaps and similar scripts are never fought.

---

## Client Exports

Available via `exports['GS-HandlingStudio']`:

| Export | Description |
|---|---|
| `SetOverride(model, handling, audio)` | Set/replace the active override for a model and apply it to the player's current matching vehicle. `handling` is a partial `{ [field] = value }` map; vectors are `{x, y, z}` |
| `ClearOverride(model)` | Restore the model's vehicle to its clean factory base |
| `GetBase(veh)` | Returns the clean base snapshot `{ [field] = value }` for a vehicle's model |
| `GetActive(model)` | Returns the current `{ handling, audio }` override, or `nil` |
| `ApplyToVehicle(veh)` | (Re)apply the active override for that vehicle's model — call this **after** your own handling writes if you want the studio's tune to win |

## Client Events

```lua
-- push/replace an override (same as SetOverride)
TriggerEvent('gsh-handling:setOverride', model, handling, audio)

-- clear an override
TriggerEvent('gsh-handling:clearOverride', model)

-- re-apply the active override to the player's current vehicle
TriggerEvent('gsh-handling:reapply')

-- fired by the studio AFTER it applies, so you can react
AddEventHandler('gsh-handling:applied', function(veh, model) end)
```

## Server Export

```lua
-- push an override to a player (or -1 for all players)
exports['GS-HandlingStudio']:PushOverride(playerId, model, handling, audio)
```

---

## Example: Cooperating Mechanic Script

An engine-swap that raises drive force while letting the studio re-stack its user override on top:

```lua
local veh = GetVehiclePedIsIn(PlayerPedId(), false)
local model = string.lower(GetDisplayNameFromVehicleModel(GetEntityModel(veh)))

-- do your own writes...
SetVehicleHandlingFloat(veh, 'CHandlingData', 'fInitialDriveForce', 0.5)

-- ...then let the studio re-apply its user override if one exists:
exports['GS-HandlingStudio']:ApplyToVehicle(veh)
```
