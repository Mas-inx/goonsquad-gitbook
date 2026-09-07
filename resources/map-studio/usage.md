# Usage

---

## Player Guide

### Opening the Studio

| Action | Default |
|---|---|
| Toggle the studio | `/mapstudio` or `/mapeditor` (optional key via `Config.OpenKey`) |
| Bring up or dismiss the cursor | Tap **Left Alt** |
| Step back (cancel ghost, clear selection, drop cursor, prompt to exit) | **Esc** |

The studio hides your ped, freezes it, and opens straight into **FLY** mode. Leaving with unsaved changes prompts you to save, discard, or cancel. Your ped is placed on the ground under the camera when you exit.

### Camera Modes

| Mode | What it does |
|---|---|
| **FLY** (default) | The game owns the mouse. Moving the mouse turns the camera; WASD flies, **Q/E** (or Ctrl/Space) go down and up, **Shift** sprints, the **mouse wheel** changes cruising speed. The interface renders but takes no input |
| **EDIT** | The cursor is visible. Click props, drag the gizmo, use the panels and keyboard shortcuts. WASD still flies |

Tap **Alt** in either mode to switch. **CapsLock** and **Esc** also step back to FLY from EDIT. Press **F** to drop into your ped and walk the set; press **F** again to lift off from where you are standing.

{% hint style="info" %}
With the default `Config.Camera.KeepFocusInFly = false`, keyboard shortcuts such as tool numbers and `H` only fire while the cursor is up. Tap Alt first. Set the option to `true` to keep shortcuts live while flying, at the cost of possible mouse-look drift on some builds.
{% endhint %}

### Tools

The top app bar holds the tools. Keys are the defaults and are rebindable in Settings.

| Key | Tool | Notes |
|---|---|---|
| `1` | Select | Click to select, Shift to multi-select, drag the gizmo to move |
| `2` / `3` / `4` | Move / Rotate / Scale | 3-axis gizmo with plane handles. `X` toggles world/local space, `G` toggles snapping |
| `5` | Place | Ghost preview; left-click places repeatedly, Ctrl+wheel or the `,` and `.` keys rotate the ghost, right-tap cancels |
| `6` | Array | Line, grid, circle, path, or stairs. Click in the world to build there, or **Apply** to build from the selection |
| `7` | Scatter | Hold left mouse and paint. The brush uses weighted models with radius, density, align-to-ground, and random yaw |
| `P` | Polygon | Click the ground to drop points, close the shape, and **Fill area** plants the prop inside on a grid or random layout |
| `8` | Area Select | Drag a marquee. Selects placed props **and** GTA world props; Shift adds |
| `9` | World Erase | Click any prop; GTA's own props stay hidden for everyone in the map |
| `0` | Eyedropper | Click any prop to copy its model into the spawner. Middle mouse works from any tool |

Selected entities get a gold outline; the erase tool shows red.

### Shortcuts

| Key | Action |
|---|---|
| `Ctrl+Z` / `Ctrl+Y` | Undo / redo (unlimited, with a browsable History page) |
| `Ctrl+S` | Save the map |
| `Ctrl+C` / `Ctrl+V` | Copy the selection / paste it as a ghost drop |
| `Ctrl+D` | Duplicate the selection |
| `Ctrl+A` | Select everything on the map (skips locked layers) |
| `Ctrl+G` / `Ctrl+Shift+G` | Group / ungroup |
| `Delete` | Delete the selection |
| Arrow keys | Nudge the selection precisely. `Shift` multiplies by 5, `Alt` moves vertically |
| `Z` | Focus the camera on the selection |
| `G` | Toggle snapping |
| `X` | Gizmo world / local space |
| `F` | Toggle fly mode (drop into ped / lift off) |
| `H` | Hide the whole interface for clean screenshots |
| `N` | Night preview for lights |
| `B` | Toggle the asset shelf |
| `CapsLock` | Toggle mouse look |

A command palette (**Search actions...**) lists every action by name.

### Quick Menu

Right-**tap** a prop for the context menu: transform, duplicate, copy, paste, delete, drop to ground, focus, save as set, align, distribute, group, ungroup, assign layer, attach light, copy model to spawner. On a GTA world prop the menu offers **Delete world prop**, **Convert to editable**, and **Hide all of this model nearby**.

---

## Interface

The bottom **asset shelf** holds the panels. Each opens from the shelf tab bar.

| Panel | Purpose |
|---|---|
| **Prop library** | Categories, subcategories, search, favorites, recently used, and personal collections. Right-click a card to add it to a collection; right-click a category chip to rename it |
| **Properties** | The floating inspector for the selection: position, rotation, scale, opacity, LOD, tint, collision, layer, group, replace model |
| **Maps** | Create, open, save, save as, rename, flags, export, publish, recovery, deleted world props |
| **Layers** | Add, rename, show/hide, lock, set active, assign the selection; groups list |
| **Lights** | Add point or spot lights, list all lights, night preview |
| **History** | Every action with click-to-jump; clear history |
| **Sets** | Server-shared prefabs: save the selection, search, place, delete |
| **Settings** | Theme, density, accent, reduced motion, language, snap increments, key bindings, about |

The bottom-left coordinate chip is clickable. **Go to coordinates** teleports the camera anywhere.

### Direct Spawn and Custom Props

Searching the library for a model name that is not in the catalog offers a **Spawn this model directly** card, so any streamed model is placeable without config changes. The **Add a custom prop** action stores a model and display name in your personal library. The server validates the model before adding it.

---

## Maps

Create or open a map in the **Maps** panel. Every edit applies to the open map and syncs live to other builders in the same map.

| Action | Notes |
|---|---|
| **Save** | Writes `data/maps/<id>.json` and clears the autosave |
| **Save as** | Copies the live state into a new map owned by you and switches to it |
| **Close** | Prompts when there are unsaved changes |
| **Rename** | Up to 48 characters |
| **Browse contents** | Lists the objects, lights, and hides in the map with **Fly there** and **Select** |
| **Export** | Writes a file to `exports/` and offers **Copy file contents** for smaller files |
| **Publish as resource** | Generates a standalone resource in `published/<name>/` |

### Flags

| Flag | Effect |
|---|---|
| **Locked** | Only the builder who locked it can edit, save, rename, change flags, or resolve recovery. Others open it read-only. An admin with `gsms.admin` can override |
| **Public** | Every player on the server sees the map live, not just builders |
| **Autoload** | The map loads on server start, so a public autoload map is your persistent build |

### Autosave and Recovery

Whenever a map has unsaved changes, a rolling autosave writes `data/autosave/<id>.json` every `Config.AutosaveInterval` seconds (60 by default), when the last builder leaves the map, and when the resource stops. The status chip shows **Autosaved** and **Saved** timestamps.

If the server stops with unsaved work, the Maps panel shows **Unsaved autosave found** with **Restore** and **Discard** the next time the map is opened. Restore commits the autosave as the real save.

### Co-editing

Several authorized builders can edit one map at the same time. Changes travel as small operations with last-write-wins per object, and drags stream so others see movement live. If the server rejects part of a batch (cap reached, rate limit, invalid data), your view is resynced from the authoritative copy and a toast explains why.

---

## World Edit

World Edit lets you treat GTA's own map props like your own, at runtime.

- **Deleting** a stock prop uses the engine's model-hide system. The removal survives re-streaming, applies to every player inside the map's area, saves with the map, and is reverted on undo or when the map unloads.
- **Moving, rotating, or scaling** a stock prop converts it: the original is hidden and an identical editable object is spawned in its place. This is the only way such an edit can persist, because the engine respawns originals on stream-in.
- The **Deleted world props** list in the Maps panel restores any hide with one click.

Three ways to erase, from surgical to sweeping:

| Method | What it removes |
|---|---|
| **Erase tool click** | The one prop you clicked. Raise **Radius** in the tool bar to wipe every identical prop around the hit |
| **Right-click, Hide all of this model nearby** | Every instance of that model within the radius you enter |
| **Erase tool with a typed Model** | Any archetype at the clicked point, including static world geometry the engine never exposes as an entity. Enter a model name, `0x...` hex, or decimal hash and click where it stands |

All three create normal hide records: persisted, synced, undoable, restorable, and included in exports and publishes.

---

## Lights

GTA exposes no persistent light native, so lights are data records drawn every frame with the real draw natives. That makes them fully persistent, syncable, and exportable.

- Add a **point** or **spot** light from the Lights panel or attach one to a prop from the quick menu.
- Edit color, range, brightness, edge hardness, cone radius, falloff, animation (steady, pulse, flicker, strobe), speed, and shadow in the inspector.
- **Attach** binds a light to an object so it rides along. Press Attach and click the object, or Shift-select the light and the object together first.
- Everyone near a light sees it; builders additionally see a selectable marker.
- **Night preview** (`N`) jumps the clock to `Config.Lights.NightPreview` and restores it when toggled off or when you leave the studio.

---

## Sets (Prefabs)

Select any props and choose **Save as set**. Sets are stored on the server and shared with every builder. Placing a set shows a translucent ghost of the real props at the cursor; click to drop it, Ctrl+wheel rotates, right-click cancels. A set holds up to 512 objects and 512 lights.

---

## Engine Limitations

- **Runtime scale**: GTA V has no native to scale spawned objects. Scale is stored, editable, and honoured in YMAP exports, but live entities render at 1.0. The inspector says so when a selection carries non-default scale.
- **Static collision**: a hidden prop loses its collision with the entity, but a hidden static building's baked collision stays solid. Terrain and road meshes cannot be removed at runtime at all.
- **Lights**: true engine light entities cannot be created from script. Shadow-casting lights are limited in number by the engine, so use the shadow toggle sparingly.
- **Stock-prop moves** are implemented as hide-original plus spawn-copy, the only persistable approach.
- **Layer visibility** is an editor aid. Runtime viewers of public maps see all layers, and hidden layers still save and publish.
