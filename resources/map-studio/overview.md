# GS Map Studio

GS Map Studio is a complete in-game map editor for FiveM. Builders fly a noclip freecam, place and transform props with a full 3D gizmo, edit or delete GTA's own world props, add dynamic lights, and save everything to the server **live** — no restarts, no CodeWalker, no database. Finished maps export to YMAP, JSON, Lua, or CSV, or publish as a standalone resource with one click.

**Version:** 1.0.0 | **Price:** TBD

{% hint style="info" %}
GS Map Studio ships in two editions. The **open** edition has every Lua and interface file readable. The **escrowed** edition is protected by FiveM Asset Escrow; `config.lua`, the prop catalog, the framework bridge, the language files, and the built interface remain open and editable.
{% endhint %}

---

## Features

### Freecam Studio
- True noclip flight: WASD, Q/E for down/up, Shift to sprint, mouse wheel for cruising speed
- Two camera modes — FLY for looking around and EDIT for pointer work — switched with a tap of Alt
- Drop into your ped with `F` to walk the set, then lift off again
- Focus-on-selection, go-to-coordinates teleport, and a clickable coordinate chip
- Your hidden ped rides along with the camera, so your server-side position always matches where you build

### Placement and Transform
- Ghost placement with align-to-surface and rest-on-surface options, Ctrl+wheel rotation, and repeat placing
- 3-axis Move / Rotate / Scale gizmo with plane handles, world or local space, and grid, angle, and scale snapping
- Arrow-key nudging with Shift for ×5 and Alt for vertical
- Copy, paste, duplicate, drop-to-ground, align, distribute, replace model, reset rotation and scale
- Per-object opacity, LOD distance, texture tint, and collision toggle

### Bulk Tools
- Array tool: line, grid, circle, path, and stairs from the selected prop
- Scatter brush: weighted random painting for forests, grass, and rubble
- Polygon fill: click out an area and fill it with a prop on a grid or random layout
- Area select marquee that picks up placed props **and** GTA world props together
- Eyedropper copies any world prop's model into the spawner (middle mouse works anywhere)

### World Edit
- Select, move, rotate, and delete GTA's own map props at runtime
- Deletions use the engine's model-hide system, so they survive re-streaming and apply to every player in range
- Erase by click, by model within a radius, or by typed model name or hash for static geometry the raycast cannot hit
- Every deleted world prop is listed in the Maps panel with one-click restore

### Dynamic Lights
- Point and spot lights with color, range, brightness, cone controls, and optional shadow
- Pulse, flicker, and strobe animation with adjustable speed
- Attach a light to a prop so it rides along when the prop moves
- Night preview toggle to check lighting at any time of day

### Organisation
- Layers with visibility and lock, groups, and server-shared prefabs ("Sets")
- Unlimited undo and redo with a browsable, click-to-jump history page
- Prop library with categories, subcategories, search, favorites, recently used, and personal collections
- Direct spawn by model name for any streamed add-on prop

### Saving, Sync, and Output
- Server-side JSON file storage with rolling autosave and crash recovery
- Live co-editing: several builders edit the same map at once and see each other's changes
- Locked, Public, and Autoload flags per map
- Export to YMAP XML, JSON, Lua, or CSV
- Publish a complete standalone resource that streams objects, model hides, and lights on any server

### Interface
- Dark and light themes, nine accent colors, comfortable or compact density, reduced motion
- Six languages: English, Spanish, German, French, Brazilian Portuguese, Turkish
- Every keybind is rebindable from the Settings panel
- Correct rendering inside MLO interiors through automatic room assignment

---

## Framework Support

| Framework | Support | Usage |
|-----------|---------|-------|
| Qbox (`qbx_core`) | Full | Job-based access and framework notifications |
| QBCore (`qb-core`) | Full | Job-based access and framework notifications |
| ESX (`es_extended`) | Full | Job-based access and framework notifications |
| Standalone | Full | ACE or identifier-based access; chat notifications |

The framework is detected automatically. The only framework-specific behaviour is job lookup for `Config.Permissions.AllowedJobs` and out-of-studio notifications; everything else uses base natives.

---

## Requirements

| Dependency | Required | Purpose |
|------------|----------|---------|
| FiveM server artifact | Yes | A modern artifact with Lua 5.4 support |
| Qbox, QBCore, or ESX | Optional | Job-based permission checks |
| MySQL / oxmysql | No | All data is stored as JSON files inside the resource |

---

## Quick Start

See [Installation](installation.md) for setup, then use `/mapstudio` (or `/mapeditor`) to open the studio.

1. Follow [Installation](installation.md).
2. Review [Configuration](configuration.md).
3. Learn the editor in [Usage](usage.md).
4. Manage builders, maps, and storage in [Administration](administration.md).
5. Integrate exported maps through [API & Exports](exports.md).
