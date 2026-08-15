# API & Exports

GS Announcements 2.6.0 does not expose public Lua exports. Use the documented server event below for business-status integrations.

---

## Business Status Event

### `gs:server:businessStatusChanged`

Fires on the server after an admin changes a business status from the dashboard.

```lua
AddEventHandler("gs:server:businessStatusChanged", function(name, status)
    print(("%s is now %s"):format(name, status))

    -- Update a phone app, map blip, dispatch board, or other resource.
end)
```

| Argument | Type | Description |
|----------|------|-------------|
| `name` | string | Business name from `Config.Businesses` |
| `status` | string | Newly selected status |

This is a local server event. Register it with `AddEventHandler`; do not treat it as a client-trusted network event.

---

## Console Broadcast Command

Server scripts that need a simple operational broadcast can execute the resource's console command:

```text
gsannounce:broadcast Server maintenance begins in ten minutes
```

The command creates a basic announcement from the `Server` business and sends it to all connected players. It is intended for administrators and console automation, not as a structured public API.

---

## Internal Events

Events under `gs:server:*` and `gs:client:*` other than the status-change event above are internal NUI and synchronization channels. Their payloads are validated and permission-gated for the bundled interface and are not a stable integration contract.

Do not trigger internal publishing events from another resource. This can couple the integration to private payload shapes and approval rules that may change between releases.
