# Integrations

GS Garages detects every integration at runtime. This page covers what is supported out of the box and the escape hatch for everything else. All integration files (`config/`, `bridge/`) ship unencrypted, so each adapter can be inspected and adjusted on an escrowed copy.

---

## Dealerships

**Most dealerships need no integration at all.** GS Garages reads the framework's own owned-vehicle table, and that table is what shops already write. A car sold thirty seconds ago shows up on the next garage open.

Edge cases are handled automatically:

- `garage` NULL or pointing at an unknown id → routed to `Config.DefaultGarage`, or retrievable from any public garage when `Config.AnyGarageIfUnknown = true`
- Malformed, double-encoded, or empty mods JSON → guarded, never throws
- Missing `state` → treated as parked

For a shop that keeps ownership somewhere non-standard, there is one escape hatch:

```lua
exports['gs-garages']:RegisterOwnedVehicle({
    identifier = 'ABC12345',        -- must match how the framework stores owners
    plate      = 'GS 4412',
    model      = 'sultan',          -- spawn name or hash
    props      = props,             -- ox_lib vehicle properties (optional)
    garage     = 'legion_public',   -- optional, defaults to Config.DefaultGarage
    category   = 'car',             -- optional, inferred otherwise
})
```

---

## Fuel

Auto-detected: **LegacyFuel · ox_fuel · cdn-fuel · ps-fuel · ND_fuel · hyon_fuel · Renewed-Fuel**, with the native GTA fuel level as the always-available fallback.

Anything else: point `Config.GetFuel` / `Config.SetFuel` at it in `config/config.lua`. One edit, no core changes.

---

## Vehicle Keys

Auto-detected: **qbx_vehiclekeys · qb-vehiclekeys · wasabi_carlock · qs-vehiclekeys · MrNewbVehicleKeys · cd_garage**.

Keys are handed over on retrieval and taken back on parking. For anything else:

```lua
Config.GiveKeys = function(src, netId, plate)
    -- hand keys your way; return true when handled
end
```

{% hint style="info" %}
Key-resource export names drift between versions. The adapter calls the documented export for each supported script and warns once (never spams) if it is rejected. If your key script updated its API, the fix is a one-line edit in `bridge/keys.lua` or the `Config.GiveKeys` hook.
{% endhint %}

---

## Targeting

Auto-detected: **ox_target · qb-target**, with a built-in `DrawMarker` + keypress fallback (`Config.Target = 'markers'`).

A single garage can force the marker fallback for itself with `interaction.type = 'marker'` regardless of what is installed. The world-anchored open prompt (`Config.OpenPrompt`) works alongside either style.

---

## Housing Integration

When a supported housing script is running, every house a player owns — or holds keys to — becomes a private garage at that house's own garage spot. Nothing is configured per house: the bridge detects the script, reads its data, and materializes the garages live. Buy a house and the garage appears; sell it and it is gone.

### Supported scripts

| Script | Detection | Access |
|---|---|---|
| ps-housing | auto | owner + `has_access` citizens |
| qbx_properties | auto | owner + keyholders |
| qs-housing (Housing Creator) | auto | owner + keyholders |
| qb-houses | auto | owner + keyholders |
| loaf_housing | auto | owner only (its key system is opaque) |
| esx_property | auto | owner + key holders |
| rtx_housing | auto | owner + keys/tenants |

Notes worth knowing:

- **ps-housing** — key changes refresh instantly (its key events are hooked); purchases land within the re-scan interval.
- **qbx_properties** — needs its optional `property_garages.sql` migration (that is where a property's garage point lives). Keep `qbx_garages` started; qbx_properties itself hard-requires its exports at boot.
- **qs-housing** — no server events are documented, so changes ride the re-scan interval.
- **qb-houses** — a house only counts once `/setgarage` has been used on it. `qb-apartments` has no garages and is intentionally not integrated.
- **esx_property** — a property only counts once an admin has set its garage position in game.
- **rtx_housing** — reads the documented exports; for instant updates, wire its open `server/other.lua` bridge to call `exports['gs-garages']:RefreshHouseGarages()`.

Change detection: supported scripts' own events are hooked where they exist, and a background re-scan (`Config.Housing.refresh`, default 300 seconds) is the safety net. Any resource can also call `exports['gs-garages']:RefreshHouseGarages()` after mutating housing data.

### Any other housing script

Two options, both first-class:

**Custom provider hook** — return every house that should have a garage:

```lua
-- config/config.lua
Config.Housing.Custom = {
    -- server-side; runs on boot, on the refresh interval, and on your events
    fetch = function()
        return {
            {
                key        = 'vinewood_12',                   -- stable unique id
                label      = 'Vinewood Villa',
                garage     = { x = 120.0, y = 500.0, z = 70.0, h = 180.0 },
                owner      = 'ABC12345',                      -- citizenid / identifier / license
                keyholders = { 'DEF67890' },                  -- optional
            },
        }
    end,
    events = { 'myhousing:server:changed' },                  -- optional instant refresh
}
```

**Static entries** — keep `type = 'house'` garages in `config/garages.lua` with `access = { property = '<house key>' }`, gated through `Config.CanAccessGarage`.

Vehicles parked at a house store `house:<key>` in their `garage` column, so they survive restarts, appear under "Find my car", and never leak into public lots.

{% hint style="warning" %}
With no housing provider and no `Config.CanAccessGarage` hook, static house garages open for anyone. This is harmless — a house garage only ever lists vehicles the player already owns — but it is not a lock, and one warning is printed on the server console.
{% endhint %}

---

## Police and Tow Scripts

Impound integration is two exports:

```lua
-- send a vehicle to the depot with a release price
exports['gs-garages']:ImpoundVehicle(plate, price, garageId)

-- inspect or move a vehicle directly
exports['gs-garages']:GetVehicle(plate)
exports['gs-garages']:SetVehicleState(plate, state, garageId)  -- 0 out, 1 stored, 2 impound
```

Release permissions live in `Config.Impound.releaseJobs`. See [API & Exports](exports.md) for the full surface.
