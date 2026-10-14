# API & Exports

The resource name is `gs_laflavia`. Client exports open local gameplay and still require server approval for item movement. Server exports are for trusted server resources; validate any client request before invoking financial/destructive exports.

## Client Exports

| Export | Result/behavior |
|---|---|
| `StartSession(barId, stationId, recipeId)` | Schedules a request to start preparation; no synchronous success result |
| `OpenStation(barId, stationId)` | Opens the station's recipe menu |
| `IsSessionActive()` | Boolean indicating a local active preparation |
| `CancelSession()` | Cancels the local preparation |
| `IsUsingIceMachine()` | Boolean indicating the local machine interface/scene is active |
| `IsUsingBarTablet()` | Boolean for the customer tablet or staff monitor interface |
| `GetDrunkLevel()` | Current client intoxication units, or zero when disabled |
| `SetDrunkLevel(units)` | Sets the local level, clamped to configured limits; zero sobers up |

```lua
exports['gs_laflavia']:OpenStation('laflavia_rooftop', 'left')
exports['gs_laflavia']:StartSession('laflavia_rooftop', 'left', 'mojito')
if exports['gs_laflavia']:IsSessionActive() then
    exports['gs_laflavia']:CancelSession()
end
```

Use a completion event when an integration needs to know whether a drink was awarded. Opening a client menu is not permission to bypass job, distance or ingredients.

## Server Exports

| Export | Result/behavior |
|---|---|
| `GetActiveSession(source)` | Session table or nil; includes server session/token data |
| `GetRecipe(recipeId)` | Normalized recipe or nil |
| `GetDrinkEffect(recipeId)` | `{ units, thirst, hunger, stress }` derived from the recipe |
| `IsStationBusy(barId, stationId)` | Boolean |
| `GetOpenOrders(placeId?)` | Public order summaries; defaults to `laflavia` |
| `DeleteOrder(orderId)` | Removes the order/stash without the staff cancellation/refund workflow; boolean success |
| `getSocietyMoney()` | Balance or nil before the society is ready |
| `addSocietyMoney(amount, text?)` | Credits the society; boolean success |
| `removeSocietyMoney(amount, text?)` | Debits only when sufficient balance exists; boolean success |

```lua
local recipe = exports['gs_laflavia']:GetRecipe('whiskey_rocks')
local effect = exports['gs_laflavia']:GetDrinkEffect('whiskey_rocks')
local orders = exports['gs_laflavia']:GetOpenOrders('laflavia')
local balance = exports['gs_laflavia']:getSocietyMoney()
local paid = exports['gs_laflavia']:removeSocietyMoney(100, 'Approved business expense')
```

Financial amounts must be finite positive whole units, capped at one billion by the society API. Never send session tokens to clients through your own integration. Recipe/session tables returned by these internal exports should be treated as read-only.

{% hint style="warning" %}
`DeleteOrder` is an administrative removal API. It clears the tray and does not refund payment or return drinks. Use it only for a deliberate cleanup; the staff UI is the normal cancellation flow.
{% endhint %}

## Shared Lift Export

`bossFloorRule()` exists on client and server:

```lua
local rule = exports['gs_laflavia']:bossFloorRule()
-- nil, or { job = 'laflavia', grade = 3 }
```

The result follows `Job.Enabled`, `Job.BossFloor.enabled`, `Job.Name` and `Job.BossFloor.minGrade`. Apply it in your lift's authoritative access checks.

## Server Events

These are local server events for observation/integration, not client grant endpoints.

| Event | Arguments |
|---|---|
| `gs_laflavia:server:drinkMade` | source, recipeId, quality, metadata |
| `gs_laflavia:server:drinkConsumed` | source, recipeId, metadata |
| `gs_laflavia:server:iceDispensed` | source, machineId |
| `gs_laflavia:server:orderPlaced` | order record |
| `gs_laflavia:server:orderReady` | orderId |
| `gs_laflavia:server:orderClosed` | orderId, reason (collected/deleted/expired/uncollected/external) |
| `gs_laflavia:server:suppliesOrdered` | supply order record |

```lua
AddEventHandler('gs_laflavia:server:drinkMade', function(source, recipeId, quality, metadata)
    print(('Bartender %s made %s at quality %s'):format(source, recipeId, quality))
end)
```

## Config Hooks

```lua
Orders.OnPaid = function(order)
    print(('Paid drink order #%s: %s'):format(order.id, order.total))
end
Supplies.OnPaid = function(order)
    print(('Paid supply order #%s: %s'):format(order.id, order.mode))
end
Config.UsableDrinks.onDrink = function(source, recipe, metadata)
    print(('Player %s consumed %s'):format(source, recipe.id))
end
```

The payment hooks execute server-side and do not replace the original billing. Add sensitive integrations in server-only code rather than putting tokens in shared config. The built-in callback transport and sync messages are internal protocol; use the documented exports/events instead of reproducing them.
