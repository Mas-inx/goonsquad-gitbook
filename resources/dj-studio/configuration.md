# Configuration

All owner-facing settings live in `config.lua`. Every value is validated again on the server; nothing in the file is trusted from the client at runtime.

`/gsdj reload` re-reads `config.lua` and rebuilds the stations without a restart (see [Administration](administration.md#commands)). Settings that are read once when the resource starts still need a restart: the framework, the command names, the soundboard clip list, the model allow-lists, and the upload and media limits.

## General

| Option | Default | Description |
|--------|---------|-------------|
| `Config.Framework` | `'auto'` | `'auto'` detects `qbx_core`, `qb-core`, or `es_extended`. Force one of `'standalone'`, `'qb'`, `'qbox'`, `'esx'` |
| `Config.Language` | `'en'` | `en`, `es`, `de`, `fr`, `pt-BR`, or `ru` (files in `html/locales/`) |
| `Config.Debug` | `false` | Verbose console logging |
| `Config.Notify` | `'auto'` | `'nui'` (built-in toast, no dependency), `'framework'`, `'ox_lib'`, or `'auto'` (ox_lib, then the framework, then the built-in toast) |
| `Config.Command` | `'gsdj'` | Main chat command |
| `Config.CreatorCommand` | `'gsdjcreator'` | Shortcut for `/gsdj creator` |
| `Config.AdminAce` | `'gsdj.admin'` | Ace that manages any station, force-releases DJ locks, imports, exports, reloads, scans, and deletes |

## Creator

| Option | Default | Description |
|--------|---------|-------------|
| `Creator.Ace` | `'gsdj.creator'` | Players with this ace may open the creator and create stations |
| `Creator.Jobs` | `{}` | Job names allowed to use the creator, e.g. `{ 'dj', 'nightclub' }` |
| `Creator.Identifiers` | `{}` | Raw identifiers allowed to use the creator, e.g. `{ 'license:…', 'discord:…' }` |
| `Creator.NoPermissionInDev` | `false` | **Development only.** `true` lets everyone use the creator. Ship with `false` |
| `Creator.MaxEmitters` | `64` | Lights, lasers, spotlights, fog, fire, and sparklers per station |
| `Creator.MaxSpeakers` | `16` | Speakers per station |
| `Creator.MaxScreens` | `8` | Screens per station |
| `Creator.MaxProps` | `64` | Decorative props per station |
| `Creator.MaxRadius` | `150.0` | Station radius ceiling in metres |
| `Creator.PlacementRange` | `120.0` | Every element must lie within this distance of the station centre |
| `Creator.FlySpeed` | `0.35` | Freecam base speed (metres per frame at 60 fps, about 21 m/s) |
| `Creator.FastMultiplier` | `3.0` | Speed multiplier while Shift is held |
| `Creator.LookSensitivity` | `0.12` | Degrees per pixel of right-drag |
| `Creator.RotateStep` | `5.0` | R / T heading step in degrees. Ctrl snaps the heading to this grid |
| `Creator.NudgeStep` | `0.05` | Arrow-key nudge in metres (Shift = ×5) |
| `Creator.AllowedModels` | see file | Allow-list for props, speakers, and fixtures. `'*'` allows everything. A station may override it with its own `allowedModels` |
| `Creator.PropPalette` | see file | `{ label, model }` entries shown in the creator's **Prop** dropdown |

Anything in the prop palette, `Config.SpeakerModels`, `Config.Screens.Models`, `Config.EmitterModels`, or a stage template is allowed automatically. You never need to add those models to `AllowedModels` as well.

### Speaker Models

`Config.SpeakerModels` lists the cabinets offered by the creator's **Speaker model** dropdown as `{ label, model }` entries. The first entry is the default. Speakers are real sound sources, so this list is separate from the decorative props. DLC cabinets fall back to base-game ones on builds that lack them.

## Interaction

| Option | Default | Description |
|--------|---------|-------------|
| `Config.InteractionMode` | `'custom'` | `'custom'` (E prompt), `'text'` (3D text), `'classic'` (GTA help text), or `'target'` |
| `Config.OpenKey` | `38` | Control id for the prompt modes (38 = E) |
| `Config.OpenKeyLabel` | `'E'` | Key label shown in the prompt |
| `Config.InteractDistance` | `2.0` | Default distance from the booth point. Each station can override it |
| `Target.Resource` | `'ox_target'` | `'ox_target'`, `'qb-target'`, or `'qtarget'`. Used when `InteractionMode = 'target'` |
| `Target.Icon` | `'fas fa-music'` | Target option icon |
| `Target.Distance` | `2.0` | Target zone radius and reach |
| `Marker.Enabled` | `true` | Floor marker at each booth (stations can switch theirs off) |
| `Marker.Type` / `Color` / `Size` | `27` / lime / `{ 1.1, 1.1, 0.5 }` | Marker appearance. Type 27 is a flat ring |
| `Marker.DrawDistance` | `25.0` | Metres within which the marker is drawn |
| `Marker.Bob` | `false` | Bob the marker up and down |
| `Config.NowPlayingHud` | `true` | Small "now playing" pill when you enter a station's coverage |

## Playback

| Option | Default | Description |
|--------|---------|-------------|
| `Playback.AllowYouTube` | `true` | Allow YouTube links |
| `Playback.AllowMp3Url` | `true` | Allow direct https `.mp3`, `.ogg`, and `.wav` links |
| `Playback.AllowLocalMedia` | `true` | Allow uploads from the DJ's PC and files in `data/media/` |
| `Playback.SeekSeconds` | `10` | Seek step for the back and forward buttons |
| `Playback.DefaultVolume` | `60` | Starting volume per station (0–100), remembered once changed |
| `Playback.KeepPlayingWithoutDj` | `true` | The queue keeps playing after the DJ closes the panel or leaves |
| `Playback.ResyncInterval` | `15000` | Milliseconds between position re-broadcasts for drift correction |
| `Playback.MaxQueue` | `100` | Tracks per station queue |
| `Playback.MaxTitleLength` | `120` | Title length limit |
| `Playback.MaxDuration` | `21600` | Seconds (6 h). Anything longer is clamped |
| `Playback.UnknownDurationFallback` | `0` | Seconds after which a track with an unknown length auto-advances. `0` never auto-advances such a track |

## Local Media

| Option | Default | Description |
|--------|---------|-------------|
| `LocalMedia.Folder` | `'data/media'` | Folder inside the resource for uploads and dropped-in files |
| `LocalMedia.MaxSizeMB` | `50` | Upload size limit |
| `LocalMedia.AllowedExtensions` | `{ 'mp3', 'ogg', 'wav' }` | Accepted file types. Files are also checked by content, not only by name |
| `LocalMedia.ChunkSize` | `8192` | Chunk size for the fallback upload route. Clamped to 2–16 KiB. **Do not raise it**: an oversized network event disconnects the client |
| `LocalMedia.PublicBaseUrl` | `''` | Address clients use to reach the resource. Empty uses `web_baseUrl`. See [Installation](installation.md#step-3-set-your-server-address-uploads-and-local-music) |

## YouTube

| Option | Default | Description |
|--------|---------|-------------|
| `YouTube.ApiKey` | `''` | Optional Data API v3 key. Enables search by name and exact durations |
| `YouTube.ResolveTitles` | `true` | Look up titles for pasted links through oEmbed (no key needed) |
| `YouTube.ScrapeDuration` | `true` | Best-effort track length from the watch page when no key is set |

## Audio

| Option | Default | Description |
|--------|---------|-------------|
| `Audio.SoundType` | `'quadratic'` | Default falloff: `'linear'`, `'quadratic'`, or `'exponential'` |
| `Audio.MaxDistance` | `60.0` | Default hard cutoff in metres. Stations override it with `maxDistance` |
| `Audio.SpeakerRadius` | `25.0` | Default coverage radius of a speaker |
| `Audio.Directionality` | `0.35` | `0` = omnidirectional speakers, `1` = only audible in front of the speaker |
| `Audio.ElevationFade` | `18.0` | Metres of vertical offset (beyond 3 m) before the sound is fully gone |
| `Audio.LowPass.Enabled` | `true` | Muffle the music outside the speaker coverage |
| `Audio.LowPass.MaxFrequency` | `20000` | Cut-off inside coverage (full range) |
| `Audio.LowPass.MinFrequency` | `300` | Cut-off far outside (through-the-wall sound) |
| `Audio.LowPass.FadeDistance` | `20.0` | Metres outside the coverage edge to reach `MinFrequency` |
| `Audio.TickMs` | `250` | Spatial update rate. Gains are smoothed in the page, so this stays cheap |
| `Audio.MasterVolume` | `1.0` | Server-wide volume multiplier |
| `Audio.PannerModel` | `'HRTF'` | `'HRTF'` or `'equalpower'` |
| `Audio.DuiWidth` / `DuiHeight` | `1280` / `720` | Audio page size, also the screen texture size |

## Effects

| Option | Default | Description |
|--------|---------|-------------|
| `Effects.RenderDistance` | `80.0` | The light show runs only for players within this range |
| `Effects.ParticleDistance` | `45.0` | Fog, fire, and sparklers stop well before the lights, since particles are the expensive half |
| `Effects.MaxLights` | `22` | Hard ceiling on script lights drawn per frame across every station in range. Beams win; coloured haze gives way |
| `Effects.WorldDistanceMax` | `500.0` | Props, screens, and speakers stay spawned out to the larger of `RenderDistance` and the station's max distance, capped here |
| `Effects.BeatIntervalMs` | `500` | Pulse interval of the `beat` preset (120 BPM). Track BPM cannot be read from a stream |
| `Effects.LaserLength` | `40.0` | Laser beam length in metres |
| `Effects.LaserLines` | `3` | Parallel strands per laser (visual thickness) |
| `Effects.ColouredHaze` | `true` | Light fog and fire from inside so any colour shows |
| `Effects.LaserGlow` | `true` | Adds a narrow spot light along each laser (volumetric in fog) |
| `Effects.LightsWithShadow` | `false` | Shadow casting for `light` emitters. Costly; spotlights always cast |
| `Effects.MaxShadowLights` | `2` | Shadow-casting beams per frame. The first thing to lower when frame rate drops |
| `Effects.SparklerInterval` | `1500` | Milliseconds between sparkler bursts at speed 0.5 |
| `Effects.Particles` | see file | Candidate particle effects per type. **Order matters**: the first one that starts on the current build wins |

`Config.LightPresets` lists the animation presets: `static`, `fan`, `sweep`, `cross`, `beat`, `rainbow`. `Config.ColorSwatches` sets the RGB swatches offered in the Effects tab.

### Emitter Defaults

`Config.EmitterDefaults` holds the starting values for each emitter type when it is placed in the creator or expanded from a template. Colours are RGB 0–255, `intensity` and `speed` are 0–1, and `swing` is the animation amplitude in degrees.

| Field | Applies to | Meaning |
|-------|-----------|---------|
| `shape` | light, spotlight | `'beam'` (visible cone), `'wash'` (soft pool), or `'point'` (bulb that ignores heading and pitch) |
| `range` | all lights | Metres the light reaches. For a point light it is the bulb diameter |
| `cone` | beam, wash | Spot **radius**, not an angle. Bigger is wider |
| `hardness` | beam, wash | Edge softness, from `0` (fades out) to `1` (sharp rim) |
| `scale` | fog, fire, sparkler | Particle size multiplier |
| `glow` | fog, fire | `{ range, intensity, height }` of the light inside the cloud that makes colour show |

### Emitter Fixtures

`Config.EmitterModels` sets the physical fixture each emitter type wears, or `false` for none. Lights and spotlights use clamp fixtures (`prop_spot_clamp_02`, `prop_spot_clamp`) that turn with the light's heading and pitch; fog uses a generator body. Missing models are skipped.

## Screens

`Config.Screens.Models` lists the props that can show the station's video. Each entry is `{ model, label, mode, ... }`.

| Mode | How it works | Needs |
|------|--------------|-------|
| `'poly'` (default) | The video is drawn onto the prop's flat face every frame, worked out from the model's bounding box. Each screen shows its own station | Just the model name |
| `'texture'` | The video replaces a render target inside the model | `txn`, the render-target texture name from the model's `.ytd`. `txd` defaults to the model name |

Drawn screens accept optional tuning in a `poly` table: `sides` (`'both'`, `'pos'`, `'neg'`), `axis` (`'x'` or `'y'`), `inset`, `insetTop`, `insetBottom`, `push`, and `flip`.

```lua
{ model = 'v_res_lest_bigscreen', label = 'Big screen (lounge)' },
{ model = 'prop_tv_flat_01', mode = 'texture', txd = 'prop_tv_flat_01', txn = 'script_rt_tvscreen', label = 'Flat TV (wide)' },
```

{% hint style="info" %}
In `'texture'` mode every copy of that model in the world shows the same video. This is an engine limitation. Use `'poly'` mode when two stations near each other use the same screen model.
{% endhint %}

| Option | Default | Description |
|--------|---------|-------------|
| `Screens.IdleArt` | `true` | Show the Goonsquad idle card when nothing is playing |

## Soundboard

| Option | Default | Description |
|--------|---------|-------------|
| `Soundboard.Cooldown` | `2000` | Milliseconds per player between sounds (server enforced) |
| `Soundboard.Margin` | `12.0` | Metres beyond the station's max distance that still hear the sound |
| `Soundboard.Volume` | `0.9` | 0–1 multiplier applied on top of distance falloff |
| `Soundboard.Sounds` | 10 clips | `{ id, label, icon, file }` entries. Files live in `html/sounds/` |

The shipped clips are original and made for this resource. To use your own, point `file` at it. A file type other than `.wav` also needs its pattern added to the `files` block in `fxmanifest.lua`.

## Persistence

| Option | Default | Description |
|--------|---------|-------------|
| `Persistence.UseDatabase` | `false` | `true` stores stations and playlists in the oxmysql tables from `sql/gs_djsystem.sql` |
| `Persistence.AutosaveDebounce` | `2000` | Milliseconds of quiet before changes are written |
| `Playlists.MaxPerPlayer` | `20` | Playlists per player |
| `Playlists.MaxTracks` | `200` | Tracks per playlist |
| `Playlists.MaxNameLength` | `32` | Playlist name length |
| `Recommendations.RecentPerStation` | `10` | Tracks in "Recently played here" |
| `Recommendations.TopSongs` | `10` | Tracks in "Top songs on this server" |

---

## Stations in `config.lua`

Stations can be built in game with the creator or defined in `Config.Stations`. A config station looks like this:

```lua
{
    id = 'pier_stage',
    label = 'Del Perro Pier Stage',
    coords = vector3(-1660.35, -1095.55, 13.15),     -- centre of the dance floor
    booth = vector4(-1660.35, -1095.55, 13.15, 320.0), -- where the DJ interacts, with heading
    radius = 40.0,
    maxDistance = 80.0,
    soundType = 'linear',
    template = 'stage',                               -- expand a stage template around coords
    permissions = { everyone = true },
    marker = { enabled = true },
},
```

| Field | Description |
|-------|-------------|
| `id` | Unique key: letters, digits, `_` and `-`, up to 32 characters |
| `label` | Name shown in prompts and the panel |
| `coords` | Centre of the dance floor (`vector3`) |
| `booth` | Where the DJ interacts (`vector4` with heading). Defaults to `coords` |
| `radius` | Coverage and effects radius in metres |
| `maxDistance` | Hard audio cutoff. Defaults to `Config.Audio.MaxDistance` |
| `interactDistance` | Booth reach. Defaults to `Config.InteractDistance` |
| `soundType` | `'linear'`, `'quadratic'`, or `'exponential'` |
| `speakers` | `{ x, y, z, heading, radius, model }` entries. Empty means one virtual speaker at `coords` |
| `emitters` | `{ id, type, x, y, z, heading, pitch, color, intensity, speed, preset, swing, enabled, shape }` entries |
| `screens` | `{ x, y, z, heading, model }` entries. The model must be in `Config.Screens.Models` |
| `props` | `{ model, x, y, z, heading, pitch, roll }` entries |
| `permissions` | `{ everyone = true }` or `{ rules = { ... } }`. See [DJ Access Rules](administration.md#dj-access-rules) |
| `allowedModels` | Optional per-station allow-list that overrides `Config.Creator.AllowedModels` |
| `marker` | `{ enabled = false }` hides this booth's floor marker |
| `template` | A stage template id. Its elements fill any of `speakers`, `emitters`, `screens`, and `props` left empty |

{% hint style="warning" %}
The example coordinates are real GTA V locations but may not match your map or MLOs. Adjust them or remove the examples before going live.
{% endhint %}

---

## Stage Templates

`Config.StageTemplates` holds the one-click venues used by the creator's **Use stage template** button and by `template = '<id>'` on a config station. The first entry is the default.

| Id | Label | Radius | Max distance |
|----|-------|--------|--------------|
| `stage` | Nightclub | 30 | 60 |
| `lounge` | Cocktail bar & lounge | 22 | 45 |
| `beach` | Outdoor beach bar | 35 | 70 |
| `concert` | Festival concert | 45 | 90 |
| `rooftop` | Rooftop terrace bar | 20 | 40 |
| `warehouse` | Warehouse rave | 40 | 80 |
| `disco` | Neon disco (funky) | 25 | 50 |
| `pool` | Pool party | 35 | 70 |
| `street` | Street block party | 35 | 65 |

Template coordinates are relative to the DJ and the booth heading:

- `x` is right, `y` is forward (towards the crowd), and `z` is height **above the floor**.
- Emitters: heading `0` aims at the crowd; pitch `-90` aims straight down and `90` straight up.
- Models (speakers, screens, desks, seating): heading `180` faces the crowd, because a GTA model's front is its −Y side.
- Stacking: give an item a `key` and another item `on = '<key>'` to stand it on top, such as a TV on a speaker or beer taps on the bar. The creator measures every model in game and finds the real floor under each item, so nothing floats or sinks.
- `radius` and `maxDistance` on a template are applied to the station along with it.

Every model used by a template is allowed automatically.

---

## Model Fallbacks

Prop names differ between game builds and DLC levels, and a model that does not exist simply never spawns. `Config.ModelFallbacks` lists replacements to try in order; the first one the current build has wins, and the substitution is logged once.

```lua
Config.ModelFallbacks = {
    prop_dj_deck_01 = { 'ba_prop_battle_dj_table_01', 'vw_prop_casino_dj_01', 'prop_table_03' },
    v_res_lest_bigscreen = { 'prop_tv_flat_01', 'prop_tv_flat_02' },
}
```

A fallback for a screen model must itself be listed in `Config.Screens.Models`, or the substitute prop can never show a picture.

---

## File Layout

```
gs-djsystem/
├── fxmanifest.lua
├── config.lua                 owner-facing settings (open)
├── shared/   utils.lua · framework.lua (open)
├── client/   state · interaction (open) · audio · screens · effects · gizmo · creator · nui · main
├── server/   persistence (open) · stations · media · playlists · playback · effects · soundboard · main
├── html/     index.html · dui.html · css/ · js/ · locales/ (6) · sounds/ · img/
├── data/     stations.json · playlists.json · history.json · media/ · exports/
└── sql/      gs_djsystem.sql (optional database tables)
```
