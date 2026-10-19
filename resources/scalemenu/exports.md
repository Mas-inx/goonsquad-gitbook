# API & Exports

## Client

| Export | Result / behavior |
|---|---|
| `GetCurrentScaleSettings()` | Effective height/body shape, model base, factor, width, preset, units, profiles, confirmed values and menu-open state |
| `SetScale({ heightCm = 190, weight = 60, preset = 'custom' })` | Requests a change; server validates access, cost and limits |
| `ResetScale()` | Requests a free reset to normal size |
| `OpenMenu()` | Requests access to the UI |
| `CloseMenu()` | Closes and discards an unsent preview |
| `IsMenuOpen()` | Local open-state boolean |

```lua
exports['gs-scalemenu']:SetScale({ heightCm = 190, weight = 60 })
```

Client action exports return that they issued a request, not a final server payment/application acknowledgement.

## Server

| Export | Result / behavior |
|---|---|
| `GetPlayerScale(src)` | Copy of a loaded record plus identifier, or nil |
| `SetPlayerScale(src, heightCm, weight, preset)` | Trusted override for an online player; clamps valid values, bypasses self-service access/cost |
| `ResetPlayerScale(src)` | Trusted normal-size override |
| `OpenMenu(src)` | Checks access and sends the open/denied response |
| `CheckAccess(src)` | Boolean and optional locale reason key |
| `GetScaleByIdentifier(identifier)` | Copy of a stored/cached offline record, or nil |

```lua
local ok = exports['gs-scalemenu']:SetPlayerScale(playerSource, 190, 60, 'custom')
```

Server changes queue persistence; success is not a guarantee that disk/database has finished writing. Watch operator storage warnings.

## Local Events and State

Client local events: `gs_scalemenu:RenderScaleMenu` / `gs_scalemenu:openMenu` open the menu; `gs_scalemenu:closeMenu` closes it.

The replicated player state key is `gs_scalemenu`, containing `{ h = heightCm, w = weight }`, or nil when reset. A body-only change may omit `h`. Server sync events mirror the same target values for client reconciliation. Network events are internal request/response transport; use the documented exports for integrations.
