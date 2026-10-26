# API & Exports

## Client Exports

```lua
exports['gs-boombox-carplay']:OpenCarplay()
exports['gs-boombox-carplay']:InstallCarplay()
exports['gs-boombox-carplay']:MountScreen('wide')
```

`OpenCarplay` uses the normal vehicle/access checks. `InstallCarplay` begins the configured fitting flow; it does not bypass job, proximity, timing or item checks. `MountScreen` begins a fitting preview for an ID listed in `Config.Car.screens.models`; use the configured ID rather than its prop or inventory name.

## Server Item Exports

```lua
exports['gs-boombox-carplay']:useBoombox(source)
exports['gs-boombox-carplay']:useCarplay(source)
exports['gs-boombox-carplay']:useScreen(source)
```

These open the relevant client flow for a player. Final placement/fitting and item changes are still checked on the server. Model-specific items should use the inventory callback wiring in [Inventory](inventory.md), which supplies the item name and selects the right model.

The same exports accept the `ox_inventory` item callback signature `(event, item, inventory, slot, data)`. They act on `usingItem` and return false to prevent the inventory consuming the item prematurely; this resource handles consumption after successful placement or fitting.

## Vehicle Control Hooks

Each enabled entry in `Config.Car.controls` can specify a client `event`. The configured event receives the vehicle and replaces the built-in action for that control. Use it to connect your existing keys, engine or other vehicle systems. Your receiving handler must enforce any additional permissions required by that system.

## Integration Surface

Use `server/bridge.lua` for framework identity, jobs, inventory count/add/remove and usable-item registration. Use `shared/phoneapps.lua` for phone app registration. Internal `gsbc:*` events, browser callbacks and helper exports in `server/files.js` are implementation details and are not a general remote-control API.
