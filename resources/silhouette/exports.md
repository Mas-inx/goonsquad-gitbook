# API & Integrations

## Client exports

```lua
exports['gs_nailsalon']:OpenDirectory()
exports['gs_nailsalon']:CloseDirectory()
local open = exports['gs_nailsalon']:IsDirectoryOpen()
```

Open checks player state and configured access. It is an integration entry point and does not impose a receptionist-distance check. Close also cancels an opening in progress. IsDirectoryOpen reports the fully open state, not the pending opening delay.

## Server status exports

```lua
local ok = exports['gs_nailsalon']:SetServiceStatus('nails', 'busy')
local status = exports['gs_nailsalon']:GetServiceStatus('nails')
exports['gs_nailsalon']:ClearServiceStatus('nails')
```

The ID must exist in Config.Services and the status must exist in Config.Statuses. Invalid IDs/statuses are rejected. Clear restores the configured default status. Overrides are stored in the global state bag and reset on resource restart; reapply them from your business integration if needed.

No networked status-setting event or admin command is registered. These exports are intended for trusted server scripts. Setting a status changes directory flags, not door locks or job permissions.

## Client event hooks

| Local event | Arguments |
|---|---|
| gs_nailsalon:client:serviceSelected | serviceId |
| gs_nailsalon:client:guidanceStarted | serviceId, coords |
| gs_nailsalon:client:arrived | serviceId |

Configured event/serverEvent/export actions receive the service ID and configured args. Server handlers must enforce their own authorization and validate any economic or character changes. The map provides no gameplay reward/payment logic.
