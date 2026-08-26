# API & Exports

GS Garages exposes a server API for dealerships, police/tow scripts, housing resources, and anything else that touches owned vehicles. All exports are **server-side** unless noted.

---

## Vehicle Registration

### RegisterOwnedVehicle

Register a vehicle for shops that do not write the framework's vehicle table themselves.

```lua
exports['gs-garages']:RegisterOwnedVehicle({
    identifier = 'ABC12345',        -- required; must match how the framework stores owners
    plate      = 'GS 4412',         -- required
    model      = 'sultan',          -- spawn name or hash
    props      = props,             -- ox_lib vehicle properties (optional)
    garage     = 'legion_public',   -- optional, defaults to Config.DefaultGarage
    category   = 'car',             -- optional, inferred otherwise
})
```

Returns `true` on success. `identifier` is the `citizenid` on QBCore/Qbox, the ESX identifier on ESX, and the license on standalone.

{% hint style="info" %}
Most dealerships do not need this export — anything that writes `player_vehicles` / `owned_vehicles` is already compatible. See [Integrations](integrations.md#dealerships).
{% endhint %}

---

## Vehicle Queries

| Export | Signature | Description |
|---|---|---|
| `GetPlayerVehicles` | `(identifier) -> table[]` | All vehicles owned by the identifier, as normalized rows |
| `GetVehicle` | `(plate) -> table\|nil` | One normalized row by plate |
| `IsVehicleOwned` | `(plate) -> boolean` | Whether the plate is registered to anyone |
| `GetGarages` | `() -> table[]` | The merged garage list (config + admin-created) |

A normalized row looks like:

```lua
{
  plate, model, hash, owner, props, garage, state,
  fuel, engine, body, mileage, depot, category,
  favorite, nickname, lastUsed
}
```

`state` is `0` (out), `1` (stored), or `2` (impounded).

---

## Vehicle State

| Export | Signature | Description |
|---|---|---|
| `SetVehicleGarage` | `(plate, garageId) -> boolean` | Move a vehicle's home garage |
| `SetVehicleState` | `(plate, state, garageId?) -> boolean` | Set stored/out/impound state directly |
| `ImpoundVehicle` | `(plate, price?, garageId?) -> boolean` | Send a vehicle to the depot; deletes the live entity if it is on the map |

### Impound example

```lua
-- from a police or tow script
local ok = exports['gs-garages']:ImpoundVehicle('GS 4412', 750, 'impound_lot')
if not ok then
    -- plate is not registered to anyone
end
```

`price` becomes that vehicle's depot release fee. Omit `garageId` to use `Config.DefaultImpound`.

---

## Garage and Admin

| Export | Signature | Description |
|---|---|---|
| `OpenGarage` | `(src, garageId) -> boolean` | Force the garage interface open on a player (admin/debug) |
| `RunMigration` | `(apply?) -> table` | Run the native import; dry run unless `apply` is `true` |
| `RefreshHouseGarages` | `() -> nil` | Re-scan housing data immediately; call after mutating houses/keys in a housing resource |

---

## Commands

The `gs_garages` server command (alias `gsgarage`) covers status, migrations, and imports — see [Administration](administration.md#commands).

---

## Escrow and Open Files

GS Garages ships through FiveM Asset Escrow with every integration surface left unencrypted:

| Open path | What you can do there |
|---|---|
| `config/config.lua` | Every option and all override hooks (`GetFuel`, `SetFuel`, `GiveKeys`, `NotifyOverride`, `CanAccessVehicle`, `CanAccessGarage`) |
| `config/garages.lua` | The full garage list |
| `config/categories.lua` | Category mapping and per-model overrides |
| `shared/schema.lua` | The catalogue of live-editable settings |
| `shared/util.lua` | Shared helpers used by the hooks |
| `bridge/*.lua` | The framework, fuel, keys, target, and housing adapters — fix a renamed export or add a provider without waiting on an update |
| `web/build/`, `web/images/` | The built interface and the optional vehicle image pack |

Protected code never needs editing for integration work: every supported customization goes through the config hooks, the bridge files, or the exports on this page.
