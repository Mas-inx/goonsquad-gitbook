# Configuration

All settings live in `shared/config.lua` under `GSH.Config`.

## General

| Option | Default | Description |
|--------|---------|-------------|
| `Command` | `'handling'` | Chat command that toggles the editor (`/handling`) |
| `DefaultKey` | `'F7'` | Default key binding to toggle the editor (rebindable per player in FiveM key bindings) |
| `Version` | `'1.0.0'` | Resource version (do not change) |

## Storage

| Option | Default | Description |
|--------|---------|-------------|
| `StorageMode` | `'auto'` | `'auto'` = MySQL via oxmysql if started, else JSON. `'mysql'` = force oxmysql. `'json'` = force flat files |
| `JsonRoot` | `'storage'` | Folder (inside the resource) for the JSON fallback store |
| `SaveDebounceMs` | `700` | Debounce for saving live edits as the active override |

## Runtime Apply

| Option | Default | Description |
|--------|---------|-------------|
| `RuntimeApply` | `true` | Apply saved overrides at runtime (the core feature — leave on) |
| `RebuildOnSpawn` | `true` | Re-apply a model's override when the player enters a vehicle of that model |
| `LiveRebuild` | `true` | Rebuild affected physics while editing so changes are felt immediately |
| `RebuildAllFields` | `false` | Rebuild every field on each edit instead of only the changed one |
| `RebuildDebounceMs` | `600` | Debounce for live rebuilds while dragging sliders |
| `EnforceOverrides` | `true` | Periodically re-assert the override on the current vehicle |
| `EnforceIntervalMs` | `800` | Interval for the enforcement check |
| `EnableGears` | `false` | Expose gear-count editing (can be unstable on some models) |
| `ProfileScope` | `'model'` | Overrides are scoped per vehicle model |

## Permissions

| Option | Default | Description |
|--------|---------|-------------|
| `RequireAce` | `false` | When `true`, players need an ace permission to use the editor |
| `Ace.Use` | `'gsh.use'` | Ace required to open the editor (when `RequireAce = true`) |
| `Ace.Admin` | `'gsh.admin'` | Admin ace — always grants access |
| `AdminPrincipals` | `{ 'group.admin' }` | Principals treated as admin |

To lock the editor to admins only:

```lua
RequireAce = true,
```

Then grant access in `server.cfg`:

```
add_ace group.admin gsh.admin allow
add_ace identifier.license:xxxx gsh.use allow
```

## Limits

| Option | Default | Description |
|--------|---------|-------------|
| `MaxProfilesPerPlayer` | `200` | Maximum saved profiles per player |
| `MaxProfileNameLen` | `48` | Maximum profile name length |

---

## Handling Schema

`shared/fields.lua` is the **single source of truth** for the handling schema. It drives both the runtime apply layer and the UI — the schema is pushed to the NUI when the editor opens. Add a field there and it appears in the editor automatically.

## Sound Sets & Presets

- `shared/sounds.lua` — the 14 curated engine sound sets (audio bank names and labels). Add your own entries to expose more banks in the picker.
- `shared/presets.lua` — the 6 built-in presets (TRACK, DRIFT, STREET, OFFROAD, RACE, DEMO). Each is a partial handling + audio map you can tweak or extend.
