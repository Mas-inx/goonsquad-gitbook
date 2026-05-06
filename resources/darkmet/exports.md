# API & Exports

---

## Server Events

### `darkmet:server:heatAlert`

Trigger a manual heat check / police alert for a player.

```lua
TriggerEvent('darkmet:server:heatAlert', source)
```

Useful for external scripts (e.g., police scanner, traffic stop integration).

---

## Police Dispatch Integration

DarkMet supports plugging into any dispatch system (`ps-dispatch`, `cd_dispatch`, `qs-dispatch`, custom MDT, etc.) without modifying core code.

Edit `server/dispatch_integration.lua` to wire in your dispatch.

### `DarkMetDispatchIntegration.PoliceAlert(payload)`

Called when DarkMet would send a police notification.

```lua
function DarkMetDispatchIntegration.PoliceAlert(payload)
    -- payload = {
    --     kind              = "heat_threshold" | "honeypot" | "traffic_scan",
    --     title             = string|nil,
    --     message           = string,
    --     coords            = { x, y, z }|nil,
    --     suspectServerId   = number|nil,
    --     suspectIdentifier = string|nil,
    -- }
    -- Return true  = handled, DarkMet skips default notify
    -- Return false = DarkMet falls back to built-in police notifies
end
```

#### Example: ps-dispatch

```lua
function DarkMetDispatchIntegration.PoliceAlert(payload)
    if GetResourceState('ps-dispatch') == 'started' then
        local c = payload.coords
        local v = c and vector3(c.x + 0.0, c.y + 0.0, c.z + 0.0) or nil
        if not v and payload.suspectServerId then
            local ped = GetPlayerPed(payload.suspectServerId)
            if ped and ped ~= 0 then v = GetEntityCoords(ped) end
        end
        if v then
            exports['ps-dispatch']:CustomAlert({
                coords = v,
                message = payload.message or 'Darknet activity',
                dispatchCode = '10-35',
                description = payload.title or 'DarkMet',
                recipientList = { 'police' },
                length = 120,
            })
            return true
        end
    end
    return false
end
```

#### Example: Custom server event

```lua
function DarkMetDispatchIntegration.PoliceAlert(payload)
    TriggerEvent('my-server:darkmetPoliceAlert', payload)
    return true
end
```

### `DarkMetDispatchIntegration.OnRaidStarted(suspectServerId, warningSeconds)`

Optional hook when a raid triggers. DarkMet already sends `darkmet:client:raidWarning` to the suspect. Use this to push extra dispatch blips or MDT entries.

```lua
function DarkMetDispatchIntegration.OnRaidStarted(suspectServerId, warningSeconds)
    -- Optional dispatch logic
end
```

---

## Client Events

### `darkmet:client:raidWarning`

Sent to the suspect client when a raid countdown starts.

```lua
RegisterNetEvent('darkmet:client:raidWarning', function(warningSeconds)
    -- warningSeconds: number of seconds before shutdown
end)
```
