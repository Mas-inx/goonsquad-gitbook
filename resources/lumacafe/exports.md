# API & Integrations

## Lift status

`GetCar` is a server export. Call it after `gs-lumacafe` starts.

```lua
local car = exports['gs-lumacafe']:GetCar('luma')
if car then
    print(car.floor, car.phase)
end
```

The export returns `nil` when the lift is unavailable or the supplied ID is unknown. The state contains these fields.

| Field | Meaning |
|---|---|
| `floor` | Current or last departed floor index. **1** is G, **2** is 1, **3** is 2 and **4** is R. |
| `phase` | `closed`, `opening`, `open`, `closing` or `moving`. |
| `target` | Cabin-selected floor index, when there is an active rider selection. |
| `to` | Destination index while the car is travelling, including journeys to answer landing calls. |

Use this export to read the lift status for server-side integrations. Player controls continue to use the supplied landing panels and cabin screen.

## Target and notification adapters

Start the optional provider before `gs-lumacafe`. Set `Config.Target` or `Config.Notify` to select it explicitly. For a custom provider, edit `lift/client/bridge.lua` and keep its zone registration, removal and notification interfaces. See [Configuration](configuration.md).
