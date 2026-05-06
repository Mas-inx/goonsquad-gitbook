# Usage

---

## Default Controls

| Key | Action |
|-----|--------|
| NUMPAD1 | Siren Slot 1 |
| NUMPAD2 | Siren Slot 2 |
| NUMPAD3 | Siren Slot 3 |
| NUMPAD4 | Siren Slot 4 |
| NUMPAD5 | Siren Slot 5 |
| NUMPAD0 | Stop all sirens |

All keybinds are configurable in `Config.Controls` and registered in FiveM's key binding settings (Esc > Settings > Key Bindings).

---

## Siren Modes

### Toggle Mode (default)
Press once to start the siren, press again to stop. The siren continues playing until toggled off or a different slot is selected.

### Hold / Momentary Mode
The siren only plays while the key is held down. Releasing the key stops the siren immediately. Useful for airhorn blasts, intersection bursts, or manual wail pulls.

---

## Adding Custom Vehicles

To add a custom police vehicle model:

```lua
Config.Vehicles[`your_car_model`] = {
    Label = "Your Police Car",
    Enabled = true,
    DisableDefaultSiren = true,
    Sirens = {
        [1] = "Wail",
        [2] = "Yelp",
        [3] = "Phaser",
        [4] = "Hi-Lo",
        [5] = { Siren = "Airhorn", Mode = "hold" },
    },
}
```

The model name in backticks (`` `your_car_model` ``) is the GTA model name (`GetHashKey` is called automatically).

---

## How Siren Audio Works

1. A player presses a siren key in a configured vehicle.
2. The client sends a request to the server.
3. The server validates permissions (job lock, driver check, cooldown).
4. If valid, the server sets a state bag on the vehicle entity with the active siren info.
5. All nearby clients detect the state bag change and create a 3D positional audio emitter.
6. The emitter updates position/orientation in real-time as the vehicle moves.
7. When the siren stops, the emitter is removed.

Occlusion (muffling through walls) is calculated client-side based on line of sight between the listener and the vehicle.

---

## Job Lock System

When `Config.Framework.JobLock.Enabled = true`:

1. Auto-detects framework (QBCore, ESX, Qbox, or manual).
2. On siren request, checks the player's job name against `JobLock.Jobs`.
3. If `AllowOffDuty = false`, also checks the player is on duty.
4. Denied requests show a notification and return a deny event.

Custom job permission logic can be added in `server/openserver.lua` via the `CanUseLeoSirenSlotServer` hook.

---

## Customization via Open Files

### `client/openclient.lua`

```lua
function GetLeoSirenControlVehicle()
    -- Return a vehicle entity to support controlling a nearby/trailer vehicle
    return false  -- false = use current vehicle
end

function CanUseLeoSirenSlot(vehicle, vehicleConfig, slotConfig, slot, sirenConfig, pressed)
    -- Client-side convenience check
    return true
end

function OnLeoSirenSlotRequested(vehicle, vehicleConfig, slotConfig, slot, sirenConfig)
    -- Called when this client requests a siren slot
end

function OnLeoSirenStateChanged(vehicle, state, vehicleConfig)
    -- Called when a streamed vehicle's siren state changes
    -- state is nil when siren stops
end

function OnLeoSirenDenied(reason)
    -- Called when a siren request is denied
end

function NotifyLeoSiren(message)
    -- Handle custom notifications
    -- Return true if handled
    return false
end
```

### `server/openserver.lua`

```lua
function CanUseLeoSirenSlotServer(sourceId, vehicle, vehicleConfig, slotConfig, slot, sirenConfig, pressed)
    -- Server-side permission check
    -- Return false + reason to deny
    return true
end
```
