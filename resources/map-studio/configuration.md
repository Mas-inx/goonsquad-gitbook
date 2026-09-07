# Configuration

All owner-facing settings live in `config.lua`. The built-in prop catalog lives in `shared/proplib.lua`. Changes to either file need a resource restart.

## Framework

| Option | Default | Description |
|--------|---------|-------------|
| `Config.Framework` | `'auto'` | `'auto'` detects `qbx_core`, `qb-core`, or `es_extended` at runtime. Force one of `'standalone'`, `'qb'`, `'qbox'`, `'esx'` |

## Permissions

A player may use the studio when **any** of these pass. Every privileged server event is re-validated against the same rules; the client and NUI are never trusted.

| Option | Default | Description |
|--------|---------|-------------|
| `Permissions.UseAce` | `true` | Enable the ace check |
| `Permissions.AcePermission` | `'gsms.use'` | Ace node that grants studio access |
| `Permissions.AllowedIdentifiers` | `{}` | Explicit identifier allow-list (`license:`, `discord:`, `fivem:`, …) |
| `Permissions.AllowedJobs` | `{}` | `['job'] = minGrade` map, checked only when a framework bridge is active |

The separate `gsms.admin` ace is not configurable. It lets a player delete other builders' maps and prefabs, override another builder's lock on save, rename, flags, and recovery, and run the reload command in game.

## Commands and Keys

| Option | Default | Description |
|--------|---------|-------------|
| `Config.Command` | `'mapstudio'` | Chat command that toggles the studio |
| `Config.CommandAlias` | `'mapeditor'` | Second command with the same behaviour (`''` = off) |
| `Config.OpenKey` | `''` | Default key mapping, e.g. `'F7'`. Players rebind it under **Settings → Key Bindings → FiveM** |

## Saving and Sync

| Option | Default | Description |
|--------|---------|-------------|
| `Config.AutosaveInterval` | `60` | Seconds between autosave passes (`0` = off) |
| `Config.StreamRadius` | `300.0` | Metres around each player in which map objects spawn |
| `Config.StreamDespawnPad` | `60.0` | Extra metres before a spawned object is despawned |
| `Config.MaxObjectsPerMap` | `6000` | Server-side cap on objects per map |
| `Config.MaxLightsPerMap` | `2000` | Cap on dynamic lights per map |
| `Config.MaxHidesPerMap` | `4000` | Cap on hidden world props per map |
| `Config.MaxLayersPerMap` | `128` | Cap on layers per map |
| `Config.MaxGroupsPerMap` | `512` | Cap on groups per map |
| `Config.MaxOpsPerBatch` | `150` | Maximum mutations per network event. Kept well under FiveM's reliable-event size limit |
| `Config.OpsPerSecondLimit` | `600` | Per-builder rate limit; excess batches are dropped and the builder is resynced |

## Editor Defaults

Every value here is also adjustable live from the Settings panel and is remembered per player.

| Option | Default | Description |
|--------|---------|-------------|
| `Editor.HistoryLimit` | `500` | Undo steps kept in memory (a toast warns once when trimming starts) |
| `Editor.GhostAlpha` | `165` | 0–255 alpha of the placement ghost |
| `Editor.Snap` | `{ pos = 0.25, rot = 15.0, scale = 0.05, enabled = false }` | Grid, angle, and scale snap increments and whether snapping starts on |
| `Editor.Locale` | `'en'` | `en`, `es`, `de`, `fr`, `pt-BR`, or `tr` |
| `Editor.Theme` | `'dark'` | `dark` or `light` |
| `Editor.Accent` | `'lime'` | `lime`, `indigo`, `cyan`, `emerald`, `brass`, `teal`, `crimson`, `violet`, `steel` |
| `Editor.Density` | `'compact'` | `comfortable` or `compact` (compact shows more of the world) |

## Camera

| Option | Default | Description |
|--------|---------|-------------|
| `Camera.Fov` | `55.0` | Freecam field of view |
| `Camera.BaseSpeed` | `12.0` | Flight speed in m/s at speed multiplier 1.0 |
| `Camera.FastMultiplier` | `3.5` | Multiplier while Shift is held |
| `Camera.SlowMultiplier` | `0.25` | Reserved slow multiplier; use the mouse wheel to lower cruising speed |
| `Camera.Sensitivity` | `8.0` | Mouse-look multiplier |
| `Camera.LookDeadzone` | `0.01` | Radial dead-zone that kills zero-jitter. Raise it if gamepad stick drift spins the camera |
| `Camera.Smoothing` | `18.0` | Flight response; higher is snappier, lower is floatier (8–30 is sane) |
| `Camera.MaxSpeedMult` / `MinSpeedMult` | `16.0` / `0.05` | Scroll-wheel speed range |
| `Camera.CarryPed` | `true` | Classic noclip: the hidden, frozen ped follows the camera so your server-side position matches where you build |
| `Camera.Debug` | `false` | On-screen mouse and camera readout for diagnosing input problems |
| `Camera.KeepFocusInFly` | `false` | `false` hands the mouse fully to the game in FLY mode (reliable look; keybinds need the cursor). `true` keeps NUI focus so keybinds fire while flying, at the cost of possible look drift on some builds |

## Lights

| Option | Default | Description |
|--------|---------|-------------|
| `Lights.DrawDistance` | `250.0` | Metres within which map lights are rendered |
| `Lights.MaxPerScene` | `96` | Soft cap on lights drawn per frame; the nearest win |
| `Lights.NightPreview` | `{ hour = 23, minute = 30 }` | Clock time used by the night preview toggle |

## Export and Publish

Both write inside the resource folder, because a FiveM resource can only write into its own directory.

| Option | Default | Description |
|--------|---------|-------------|
| `Config.ExportDir` | `'exports'` | Folder for `<map>.ymap.xml`, `.json`, `.lua`, and `.csv` exports |
| `Config.PublishDir` | `'published'` | Folder for generated standalone resources (`published/<name>/`) |

## Prop Thumbnails

| Option | Default | Description |
|--------|---------|-------------|
| `Config.PropThumbs` | `'https://cdn.gtahash.com/objects/thumbs/{model}.webp'` | URL template for library thumbnails. `{model}` is replaced with the model name. Point it at your own host, or set `''` for text-only cards with no external requests |

Cards fall back to text automatically when an image fails to load.

## Custom Props

| Option | Default | Description |
|--------|---------|-------------|
| `Config.CustomProps` | `{}` | Add-on models streamed by other resources. Each entry is `{ model, label, category, sub }` |
| `Config.CustomCategories` | `{ { id = 'custom', label = 'Custom', icon = 'sparkle' } }` | Extra library categories for the entries above |

```lua
Config.CustomProps = {
    { model = 'bzzz_prop_plant_04', label = 'Potted Monstera', category = 'custom', sub = 'Bzzz Plants' },
}
```

Invalid models toast and are skipped; they never crash the spawner.

---

## Prop Catalog

`shared/proplib.lua` holds the built-in catalog and assigns `Config.PropLibrary`. It is curated from well-known base-game prop families and can be edited freely. If a specific game build lacks an entry, the spawner skips it cleanly.

## File Layout

```
gs-map-studio/
├── fxmanifest.lua
├── config.lua                 owner-facing settings
├── shared/   utils.lua · framework.lua · proplib.lua (the catalog)
├── client/   state · screen · camera · objects · lights · worldedit · history
│             spawner · gizmo · tools · array · collections · sync · nui · input · main
├── server/   persistence · sessions · sync · export · publish · main
├── web/      index.html · css/app.css · js/* (vanilla, no build step) · locales/* (6)
├── data/     index.json, maps/, autosave/, prefabs/
├── exports/  written on export        published/  written on publish
└── scripts/  dev-only Node helpers (safe to delete)
```

The escrowed edition ships the interface as `web/dist/app.js` and `web/dist/app.css` instead of the `js/` and `css/` sources, and has no `scripts/` folder. The language files in `web/locales/` are identical in both editions.
