# Troubleshooting

Start with the server console. On a healthy boot the resource prints:

```text
[gs-map-studio] storage ready: N map(s), N prefab(s).
```

Every denied action in game shows a toast that names the reason. The most useful ones are listed below.

---

## Access and Opening

### `/mapstudio` says you do not have access

- Confirm at least one permission method passes in `config.lua`: the `gsms.use` ace, an entry in `AllowedIdentifiers`, or a job in `AllowedJobs`.
- Check for identifier typos, especially `license:` versus `identifier.license:` syntax in `server.cfg`.
- Job checks need the framework core started **before** `gs-map-studio`. Check `Config.Framework` and the **About** line in the Settings panel, which shows the detected framework.
- Restart the resource after changing `config.lua`.

### The studio opens but nothing responds to the keyboard

You are in FLY mode, where the game owns the input. Tap **Left Alt** to bring up the cursor; tool keys and shortcuts fire in EDIT mode. To keep shortcuts live while flying, set `Config.Camera.KeepFocusInFly = true`.

### The mouse does not turn the camera

- Make sure you are in FLY mode (tap Alt until the cursor disappears).
- Set `Config.Camera.Debug = true` to show an on-screen mouse and camera readout, then compare mouse movement with the reported delta.
- If the camera spins on its own, a gamepad is drifting. Unplug it or raise `Config.Camera.LookDeadzone`.
- If look drifts or freezes with `KeepFocusInFly = true`, switch back to the default `false`.

### Clicks miss the prop under the cursor

The cursor position comes from the NUI and matches the rendered frame at any resolution or DPI. If clicks still land off target, check for another resource that changes the game camera or FOV while the studio is open, and disable it during building.

### Stuck without a cursor, camera, or ped after a crash

Stopping or restarting the resource restores the camera, ped visibility, radar, and NUI focus, and deletes every spawned object and ghost. Run `restart gs-map-studio` from the console.

---

## Saving and Storage

### `Saving failed — check server console`

- The `data/`, `data/maps/`, and `data/autosave/` folders must exist inside the resource. FiveM cannot create directories, and a missing folder makes the write fail silently.
- Confirm the server process can write to the resource folder.
- Look for `ERROR: failed to encode` in the console, which points to a corrupt in-memory document.

### `This map is locked by another builder`

The map is locked and you are not the locker. Ask the locker to unlock it from the Maps panel, or have an admin with `gsms.admin` unlock it.

### `Only the author (or an admin) can do that`

Deleting a map or prefab is limited to its author. Grant `gsms.admin` to staff who need to delete other builders' work.

### Recovery keeps appearing for a map

An autosave newer than the last save exists. Open the map and choose **Restore** to commit it or **Discard** to drop it. Both clear the recovery state.

### Maps or prefabs vanished after moving the resource

The registry is `data/index.json`. Copy it together with `data/maps/` and `data/prefabs/`; a map file without its index entry is not listed.

### A map with unsaved edits was lost on restart

Set `Config.AutosaveInterval` above `0`. With autosave enabled, dirty maps are also flushed when the last builder leaves and when the resource stops cleanly. A hard crash between autosaves loses at most one interval of work.

---

## Editing and Sync

### `Some edits were rejected by the server — your view was resynced`

Part of a batch failed validation or hit a cap. Common causes:

- The map reached `Config.MaxObjectsPerMap`, `MaxLightsPerMap`, `MaxHidesPerMap`, `MaxLayersPerMap`, or `MaxGroupsPerMap`.
- A single operation produced more than `Config.MaxOpsPerBatch` changes.
- The builder exceeded `Config.OpsPerSecondLimit`. Large scatter strokes and array fills are the usual trigger; lower the count or density.

### `Too many objects in one operation`

A single array, scatter, paste, or prefab exceeded `Config.MaxOpsPerBatch` (default 150) or a prefab exceeded 512 objects. Split the operation or raise the cap. Keep `MaxOpsPerBatch` well under FiveM's reliable-event size limit; oversized events disconnect the client.

### `Model X is not valid on this server`

The model is not streamed on this server. For add-on props, confirm the streaming resource is started, then either add the entry to `Config.CustomProps` or use **Add a custom prop** in the library. Catalog entries that a specific game build lacks are skipped automatically.

### Other builders do not see my changes

- Both builders must have the **same map** open. Check the map name in the top bar.
- A public map streams to everyone; a private map is only visible to builders who have it open.
- If a builder's session was closed by a permission change, they need to reopen the studio.

### Players do not see a public map

- Confirm the map is **Public** and either open by a builder or flagged **Autoload**.
- Objects only spawn within `Config.StreamRadius` of the player.
- New joiners request runtime state a couple of seconds after spawning. This request is throttled to once every three seconds.

---

## World Edit

### A deleted world prop comes back

- The hide is only active while the map is loaded. Keep a builder in the map, flag it **Public + Autoload**, or publish the map as a resource.
- Static geometry (vegetation, fences, building add-ons) is not clickable. Use the erase tool with a typed model name or hash instead.

### A hidden building is still solid

This is an engine limit. Hidden props lose collision with their entity, but a static building's baked collision stays. Removing it needs a CodeWalker edit of the `.ybn` and `.ymap` files. Terrain and road meshes cannot be removed at runtime.

### A moved world prop snapped back

Moving a stock prop converts it into an editable object and hides the original. If the conversion toast did not appear, the click missed the prop. Select it again and use the Move tool or **Convert to editable** from the quick menu.

---

## Lights and Rendering

### Lights are invisible to players

Lights draw within `Config.Lights.DrawDistance` and are capped at `Config.Lights.MaxPerScene` nearest lights per frame. Raise the values or reduce the light count in dense areas.

### Shadows only appear on some lights

The engine renders a limited number of dynamic shadows. Use the shadow toggle sparingly and rely on non-shadow lights for fill.

### Objects placed inside an interior are invisible or flicker

The studio assigns interior rooms automatically when it spawns objects. If an object was placed on the boundary of two rooms, nudge it further inside. Published resources use the same room assignment.

### Scaled objects render at normal size

Expected. GTA cannot scale runtime objects. Scale is stored and applied in the YMAP export only.

---

## Export and Publish

### The YMAP does not load on the server

FiveM does not read `.ymap.xml`. Open it in CodeWalker, save as binary `.ymap`, and stream it from a resource with `this_is_a_map 'yes'`. Deleted world props and lights are not part of a ymap; publish the map instead.

### `Export failed` or `Publish failed`

- The `exports/` or `published/` folder must exist inside the resource.
- Export and publish are limited to one per three seconds per builder. Wait and retry.
- Read the console for `export failed:` with the Lua error.

### Copy file contents is missing after an export

Files over 700 KB are written to disk only. Fetch them from `gs-map-studio/exports/` on the server.

### Published map shows nothing

- Copy the whole `published/<name>/` folder into `resources/` and `ensure <name>`.
- The name is sanitized; check the folder that was actually written.
- Add-on props still need their streaming resource on the target server.

---

## Interface

### The interface is blank

The NUI is plain HTML, CSS, and JavaScript under `web/` (source files in the open edition, `web/dist/` in the escrowed edition). Restore the shipped files if they were modified, and check the F8 console for JavaScript errors. `web/index.html` also opens in a normal browser with mock data to confirm the files are intact.

### Thumbnails do not load

`Config.PropThumbs` points at the public gtahash.com CDN by default. Cards fall back to text when an image fails. Point the template at your own host or set it to `''` for text-only cards with no external requests.

### Key bindings or favorites reset

Per-player preferences live in client KVPs. They reset when the player clears their FiveM cache or the KVP store. **Reset all bindings** in Settings restores the defaults deliberately.

---

## Information to Send Support

- GS Map Studio version (shown in **Settings → About**)
- Framework and framework version
- The `[gs-map-studio]` lines from the server console at boot
- Relevant server-console and player F8 errors
- The exact toast message and the steps that reproduce the problem
- Whether `config.lua`, `shared/proplib.lua`, `web/`, or the resource folder name was changed

Remove license identifiers, Discord IDs, and player names before sharing logs publicly.
