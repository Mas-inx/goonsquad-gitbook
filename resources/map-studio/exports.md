# API & Exports

GS Map Studio does not expose Lua exports or public events. Its integration surface is the files it writes: four export formats and a generated standalone resource. All of them are built from the authoritative server-side map document.

The internal `gsms:` network events are validated against the caller's session and permission on every call and are not intended for third-party scripts.

---

## Export Formats

**Maps → Export** writes into `gs-map-studio/exports/`. The file name is the sanitized map name.

| Format | File | Use |
|---|---|---|
| **YMAP** | `<map>.ymap.xml` | CodeWalker-compatible `CMapData` XML. Open in CodeWalker, save as binary `.ymap`, and stream it from any map resource |
| **JSON** | `<map>.json` | The native document (objects, lights, hides, layers, groups), pretty-printed with sorted keys |
| **Lua** | `<map>.lua` | A plain table (`return { name, objects, lights, hides }`) for your own loader |
| **CSV** | `<map>.csv` | `kind,id,model,x,y,z,pitch,roll,yaw,scale_x,scale_y,scale_z,extra` rows |

Files up to 700 KB are also returned to the studio so **Copy file contents** works; larger exports are written to disk only.

### YMAP Notes

- FiveM does not load the XML form directly. Convert to binary `.ymap` with CodeWalker once.
- A ymap holds **placements only**. Stock-prop deletions and dynamic lights are not representable in it. Use Publish for the full map.
- Rotation is written as the inverse quaternion, following CodeWalker's convention.
- Object **scale** works in ymaps and only there. GTA cannot scale runtime-created objects.
- Converted stock props whose model is only known by hash export as `hash_XXXXXXXX`. Models found in the prop catalog resolve to their names.
- Streaming extents are padded 350 m and entity extents 40 m around the placed objects. LOD distance defaults to 250 unless set per object.

### Lua Export Schema

```lua
return {
    name = "My Map",
    objects = {
        { id = "o1", model = "prop_bench_01a", pos = vector3(x, y, z), rot = vector3(pitch, roll, yaw),
          scale = vector3(1.0, 1.0, 1.0),  -- only when non-default
          alpha = 200,                      -- only when below 255
          lod = 400,                        -- only when set
          tint = 2,                         -- only when set
          collision = false },              -- only when disabled
    },
    lights = {
        { id = "l1", kind = "spot", pos = vector3(x, y, z), rot = vector3(pitch, roll, yaw),
          color = { 255, 214, 170 }, range = 25.0, intensity = 1.4,
          hardness = 40.0, radius = 13.0, falloff = 1.0,
          anim = "none", animSpeed = 1.0 },
    },
    hides = {
        { id = "h1", model = 1234567890, pos = vector3(x, y, z), radius = 0.5 },
    },
}
```

`kind` is `point` or `spot`. `anim` is `none`, `pulse`, `flicker`, or `strobe`. Hide `model` is always a numeric hash.

### CSV Notes

Text cells are RFC-4180 quoted when needed, and a leading `=`, `+`, `@`, or `-` is prefixed with an apostrophe so a crafted id or model name cannot become a live spreadsheet formula. Light rows carry `rgb(r;g;b) range=… intensity=…` in the `extra` column; hide rows carry `radius=…`.

---

## Published Resources

**Maps → Publish as resource** writes a complete standalone resource into `gs-map-studio/published/<name>/`:

| File | Purpose |
|------|---------|
| `fxmanifest.lua` | Cerulean manifest, Lua 5.4, client scripts only |
| `map_data.lua` | The map as a `MapData` table (objects, lights, hides) |
| `client.lua` | The streamer: spawns objects near the player, applies model hides, draws lights |
| `README.txt` | Install steps and a summary of what the map contains |

Copy the folder to any server's `resources/` directory and `ensure <name>`. The generated client uses only base natives, so it runs on ESX, QBCore, Qbox, and standalone servers without changes.

### Runtime Behaviour

- Objects stream in within 300 m of the player and despawn 60 m beyond that. They are client-local and non-networked, so they consume no OneSync entity budget.
- Objects inside MLO interiors are assigned to the interior room automatically.
- Model hides are re-applied on resource start and removed on resource stop.
- Lights are drawn within 250 m with the same pulse, flicker, and strobe animation as the studio.
- Object scale is not applied at runtime. Use the YMAP export if you need scaling.

To edit a published map later, open the original in the studio and publish again with the same name.

---

## Loading an Exported Lua Table Yourself

If you prefer your own loader, the Lua export is a plain table:

```lua
-- fxmanifest.lua of your resource
client_scripts {
    'my_map.lua',      -- the export, renamed
    'loader.lua',
}
```

```lua
-- loader.lua
local map = require 'my_map'   -- or: local map = dofile(...) / a shared_script returning the table

CreateThread(function()
    for _, o in ipairs(map.objects) do
        local model = type(o.model) == 'number' and o.model or joaat(o.model)
        RequestModel(model)
        while not HasModelLoaded(model) do Wait(0) end
        local obj = CreateObjectNoOffset(model, o.pos.x, o.pos.y, o.pos.z, false, false, false)
        SetEntityRotation(obj, o.rot.x, o.rot.y, o.rot.z, 2, true)
        FreezeEntityPosition(obj, true)
        SetModelAsNoLongerNeeded(model)
    end
    for _, h in ipairs(map.hides) do
        CreateModelHide(h.pos.x, h.pos.y, h.pos.z, h.radius, h.model, true)
    end
end)
```

The published resource's `client.lua` is a complete reference implementation of the same idea, including streaming, interior rooms, and lights.

---

## Data Files

The live documents in `data/maps/<id>.json` share the same shape as the JSON export, with a few extra fields: `author` (license identifier), `authorName`, `created`, `updated`, `locked`, `lockedBy`, `public`, and `autoload`. Objects, lights, and hides are keyed by id rather than listed in arrays. Vectors are stored as `{ x, y, z }` arrays.

Treat `data/index.json` as resource-owned. Edit maps through the studio rather than by hand.
