# API & Exports

## Client Exports

```lua
exports['gs-baddiehub']:OpenHub()
exports['gs-baddiehub']:CloseHub()
local open = exports['gs-baddiehub']:IsHubOpen()
```

`OpenHub` requests server authorization and does nothing while already open, the pause menu is active or another NUI has focus. `CloseHub` releases focus; `IsHubOpen` returns a boolean. Opening is asynchronous; do not expect a synchronous state return.

## Server Exports

```lua
local member = exports['gs-baddiehub']:IsMember(source)
local granted = exports['gs-baddiehub']:AddSeasonXP(source, 25)
local profile = exports['gs-baddiehub']:GetProfile(source)
```

| Export | Return / behavior |
|---|---|
| `IsMember(src)` | Boolean from the loaded session's cached membership; does not perform a fresh Discord lookup |
| `AddSeasonXP(src, amount)` | False without a member session or for invalid, nonpositive or overflowing amounts; floors valid positive XP and returns true after granting |
| `GetProfile(src)` | Nil without a session, otherwise identifier, publicId, name, avatar, bio and online |

XP amounts are server-controlled. Do not relay an arbitrary client-supplied amount through this export. The profile export's `identifier` is the real framework character identifier and `publicId` is its public member ID. Keep the real identifier on the server. `avatar` contains `type` and `value`; cached profiles can exist for nonmembers, so check membership separately.

## Inventory Lifecycle Exports

The configured welcome-box and Lounge Pass names may be registered as dynamic inventory exports. They receive inventory event/item/inventory arguments; they are not direct reward-giving APIs. Use them only through the supported inventory lifecycle described in [Installation](installation.md).

## NUI Contract

Lua sends `openHub { state }`, `closeHub`, `updateState { kind, data }` and `notify { message, type }`. The runtime posts its routes to the current parent resource. Profile, friendship, blocking, DM, chat and reward routes are gated and validated server-side. Close and waypoint are local UI operations. The browser state is presentation data, not an authoritative permission or reward source.
