# API & Exports

GS Minimap Designer exposes server-side access and design reads, plus client-side postal lookup. Add `dependency 'gs-minimapdesigner'` to an integration resource's manifest and start it after the designer.

---

## Server Exports

### hasEditAccess

```lua
local allowed = exports['gs-minimapdesigner']:hasEditAccess(source)
```

Returns a boolean using the same ACE and configured identifier checks as the editor. Pass a connected player's server ID. This checks editor access, not ownership of an individual sheet.

### getPublishedDesign

```lua
local design = exports['gs-minimapdesigner']:getPublishedDesign()
if not design then return end

for _, zone in ipairs(design.zones or {}) do
    if not zone.hidden then
        print(zone.name, zone.type)
    end
end
```

Returns the current published design table, or `nil` if none has loaded. Treat it as read-only; modifying it is not a supported save or publish mechanism.

Startup storage initialization is asynchronous, so callers must allow for `nil` while the resource starts. Draft changes are not returned until they are published.

---

## Client Exports

### getPostal

```lua
local coords = GetEntityCoords(PlayerPedId())
local code = exports['gs-minimapdesigner']:getPostal(coords.x, coords.y)
if code then
    print(('Current postal: %s'):format(code))
end
```

Returns the postal code string for a world position, including the configured prefix and padding. Returns `nil` if postals are disabled or the position is outside the grid.

The lookup uses the design currently applied on that client.

### getPostalCenter

```lua
local center = exports['gs-minimapdesigner']:getPostalCenter('100')
if center then
    SetNewWaypoint(center.x, center.y)
end
```

Returns a `vector2` at the centre of the postal cell, or `nil` for an invalid code or disabled postal grid. Supply the complete displayed code.

For a custom HUD, set `Config.PostalHud = false` and use `getPostal` from the HUD's update loop.

---

## Design Structure

Published editor sheets use version 3. The loader also accepts legacy sheets, so integrations should handle absent fields.

| Field | Content |
|---|---|
| `version` | Design schema version |
| `blips` | Marker coordinates, sprite, colour, scale, label, short-range and hidden state |
| `zones` | Circle, rectangle or polygon definitions |
| `texts` | Labels, coordinates, font, size, colour and rotation |
| `images` | Image source, coordinates, dimensions, opacity, rotation and feathering |
| `paths` | Vertices, width, colour, dashes, arrow and halo settings |
| `patches` | Heal-patch destination, source centre, dimensions and blending |
| `postals` | Grid bounds, row/column counts, numbering and styling |
| `theme` | Base, colour bands, global grade, HUD overrides and baking flags |

### Zone Shapes

All zones have `id`, `name`, `coords`, `color`, `alpha`, and an optional `hidden` flag.

| `type` | Additional fields |
|---|---|
| `circle` | `radius` |
| `rect` | `width`, `height`, `rotation` |
| `poly` | `points` array of `{ x, y }` vertices and `outline` |

Coordinates and dimensions use world units. Rotation is expressed in degrees; zone alpha ranges from 0 to 255. Zone definitions are drawing data, not automatic territory or gameplay triggers.

---

## Persistence and Events

The MySQL design table contains `name`, JSON `data`, and `updated_at`. Its companion thumbnail table contains `name` and `thumb`. JSON mode uses `data/<name>.json`, `data/designs_index.json` and `data/thumbs/<name>.txt`.

`data/active.json` stores the published sheet pointer for both backends.

Internal `gs-minimapdesigner:*` events handle editor requests, transport and synchronization. There is no public save/publish export or dedicated documented publication hook in this version. Prefer the read exports and editor workflow; direct database writes do not broadcast a design or refresh the current in-memory publication.

The filesystem and AI chunk helpers are internal transport APIs. For file-based integration, see [Custom Maps & Streaming Exports](custom-maps.md).
