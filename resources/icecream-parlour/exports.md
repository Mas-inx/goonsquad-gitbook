# API & Exports

## Server exports

```lua
local product = exports['gs-icecream-parlour']:GetProduct('softserve_twist')
local products = exports['gs-icecream-parlour']:GetProducts()
local busy = exports['gs-icecream-parlour']:IsMachineBusy('hawick_softserve')
local orders = exports['gs-icecream-parlour']:GetOpenOrders('hawick')

local balance = exports['gs-icecream-parlour']:getSocietyMoney('icecream_hawick')
local credited = exports['gs-icecream-parlour']:addSocietyMoney(100, 'Integration credit', 'icecream_hawick')
local debited = exports['gs-icecream-parlour']:removeSocietyMoney(50, 'Integration debit', 'icecream_hawick')
```

Omitting the society job selects the first configured shop job. Account setup can be asynchronous; balance may be nil until ready. Add/remove return false for invalid accounts/amounts or failed updates. Treat returned product/design tables as read-only.

`DeleteOrder(orderId)` is a trusted external removal operation. It clears the order/tray and does not perform the customer/staff refund workflow used by the monitor. Do not expose it to arbitrary clients.

## Client exports

```lua
local busy = exports['gs-icecream-parlour']:IsBusy()
local machine = exports['gs-icecream-parlour']:IsUsingMachine()
local tablet = exports['gs-icecream-parlour']:IsUsingBarTablet()
```

These describe the current client's interaction state.

## Server hooks/events

| Hook/event | Arguments |
|---|---|
| gs_icecream:server:made | source, machineId, item, count, quality |
| gs_icecream:server:consumed | source, productId, metadata |
| gs_icecream:server:orderPlaced | order copy |
| gs_icecream:server:orderReady | orderId |
| gs_icecream:server:orderClosed | orderId, reason |
| gs_icecream:server:suppliesOrdered | supply order copy |

These are local server integration notifications. The internal callback protocol is not a documented public mutation API. No public/test commands are registered.
