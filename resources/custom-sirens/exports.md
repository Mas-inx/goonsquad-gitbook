# API & Exports

---

## Server Events

### `gs_leosirens:server:requestSlot(netId, slot, pressed)`

Triggered by client to start/stop a siren slot on a vehicle.

```lua
-- netId: network ID of the vehicle
-- slot: 1-5
-- pressed: true (key down), false (key up)
```

If `pressed = true` and the slot is in hold mode, the server tracks the source. When `pressed = false` for the same slot, the siren is stopped. Toggle mode switches on/off with each `pressed = true`.

### `gs_leosirens:server:requestStop(netId)`

Triggered by client to stop all active sirens on a vehicle.

---

## Client Events

### `gs_leosirens:client:denied(reason)`

Sent to a client when their siren request is denied.

```lua
RegisterNetEvent("gs_leosirens:client:denied", function(reason)
    -- reason: string explaining why the request was denied
end)
```

---

## State Bag

Siren state is synced via the state bag key `gs_leosirens:active` on the vehicle entity.

```lua
Entity(vehicle).state["gs_leosirens:active"]
-- nil or {
--     slot = number,       -- 1-5
--     siren = string,      -- siren ID
--     mode = string,       -- "toggle" or "hold"
--     source = number,     -- server ID of player who started it
--     startedAt = number,  -- GetGameTimer() timestamp
-- }
```

---

## Open File Hooks (Client)

Defined in `client/openclient.lua`:

```lua
function GetLeoSirenControlVehicle()
    -- Return vehicle entity or false (uses player's current vehicle)
    -- Useful for controlling nearby vehicles or trailers
end

function CanUseLeoSirenSlot(vehicle, vehicleConfig, slotConfig, slot, sirenConfig, pressed)
    -- Client-side permission check
    -- return false, "reason" to deny
end

function OnLeoSirenSlotRequested(vehicle, vehicleConfig, slotConfig, slot, sirenConfig)
    -- Called when siren slot is requested
end

function OnLeoSirenStateChanged(vehicle, state, vehicleConfig)
    -- Called when any streamed vehicle's siren state changes
    -- state = nil when siren stops
    -- state = table when siren starts
end

function OnLeoSirenDenied(reason)
    -- Called when request is denied
end

function NotifyLeoSiren(message)
    -- Return true if you handled the notification
    -- Useful for okokNotify, qb-core notifications, etc.
end
```

---

## Open File Hooks (Server)

Defined in `server/openserver.lua`:

```lua
function CanUseLeoSirenSlotServer(sourceId, vehicle, vehicleConfig, slotConfig, slot, sirenConfig, pressed)
    -- Server-side permission check
    -- return false, "reason" to deny
    -- return true to allow
end
```

---

## GSLeoSirens API (Server)

The `GSLeoSirens` global table provides framework access:

```lua
GSLeoSirens.GetFramework()
-- Returns: framework string ("qbcore", "esx", "qbox", "none"), framework object

GSLeoSirens.GetPlayerJob(sourceId)
-- Returns: jobName string, onDuty boolean
```

Custom server scripts can use these for external integration.
