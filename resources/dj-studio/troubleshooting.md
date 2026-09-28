# Troubleshooting

Start with the server console. On a healthy boot the resource prints:

```text
[gs-djsystem] ready - framework <name>, N stations, interaction "<mode>"
```

For anything that happens at a station, stand inside it and run:

```text
/gsdj diag
```

Then open **F8**. The diagnostics explain, station by station, why it is silent, dark, or blank: speaker range, volumes, the audio page state, YouTube errors, screen modes, missing models, and the light show. Most answers below are what `diag` points to.

---

## Audio

### No sound at all

- Check you are not in streamer mode: `/gsdj streamer off`.
- Check your personal volume: `/gsdj volume` shows it; `/gsdj volume 100` resets it.
- Check the station volume in the DJ panel and `Config.Audio.MasterVolume`.
- `diag` reports `speakerGain is 0` when you are outside every speaker's coverage. Move closer or raise the speaker radius or the station's max distance.

### Uploaded or library songs are silent, or uploads hang

Local songs are streamed from your server's HTTP address. Set `Config.LocalMedia.PublicBaseUrl` to the address players connect to, such as `http://YOUR.SERVER.IP:30120`, or an https address behind a reverse proxy. See [Installation](installation.md#step-3-set-your-server-address-uploads-and-local-music).

- The console warns on boot when `web_baseUrl` is empty or a placeholder such as `deprecated-xxxxxxx.users.cfx.re`.
- `Local media streaming is not configured (web_baseUrl)` in game means no usable address was found.
- `diag` showing `media error` means the page could not load the file from that address.

### A YouTube link stays silent and blank

| `diag` says | Cause |
|-------------|-------|
| `youtube error 101` or `150` | The uploader does not allow embedding. Use another upload of the song |
| `youtube error 100` | The video is private, removed, or not found |
| `youtube error 2` | The link does not contain a valid video id |
| `youtube iframe API never loaded` | The game cannot reach youtube.com (firewall, DNS, or region block on the player's side) |

### Music is out of sync between players

Every client plays from the server's clock and is corrected every `Config.Playback.ResyncInterval` (15 seconds by default). A player who just loaded in catches up at the next resync. Lower the interval for tighter correction at a little more network traffic.

### A track never moves on to the next one

The track length is unknown, which is common for direct links. The listeners' audio pages report the end and the server then advances. If nobody is in range when it ends, set `Config.Playback.UnknownDurationFallback` to a number of seconds to force an advance.

### The music is muffled

That is the low-pass filter for listeners outside the speaker coverage. Move into the coverage, raise the speakers' radius, or tune `Config.Audio.LowPass` (set `Enabled = false` to switch it off).

---

## Access and Opening

### `No DJ station nearby`

`/gsdj` opens the nearest booth within 12 metres. Walk to the booth (the ring on the floor) or use `/gsdj menu <id>`.

### `This station is restricted`

The station's access rules do not match you. Check them in the **Manage** tab. See [DJ Access Rules](administration.md#dj-access-rules). Job rules need the framework core started **before** `gs-djsystem`; Discord rules need a supported Discord role resource.

### `This booth is in use by <name>`

Another player holds the DJ lock. The owner or an admin can open the panel and use **Take over DJ lock**, or an admin can run `/gsdj release <id>`.

### `You are too far from the station`

The server checks the distance to the booth: the station's interact distance plus 6 metres, at least 12. Stand at the booth. Admins are exempt.

### Cannot open the creator

Grant `gsdj.creator` or `gsdj.admin`, or add the player's job to `Config.Creator.Jobs` or identifier to `Config.Creator.Identifiers`. Editing an existing station also needs you to own it or be an admin; stand outside other stations or use `/gsdjcreator new` to start a fresh one.

### The E prompt or target option does not appear

- Check `Config.InteractionMode`. With `'target'`, `Config.Target.Resource` must match a started target resource (`ox_target`, `qb-target`, or `qtarget`), and it must start before `gs-djsystem`.
- The prompt only shows within the station's interact distance of the booth point. Use **Set booth here** in the creator if the booth is in the wrong place.

---

## Stations and the Creator

### `N element(s) could not be saved`

The toast names the most common reason:

| Reason | Fix |
|--------|-----|
| Too far from the station centre | Keep everything within `Config.Creator.PlacementRange` (120 m), or use **Move whole station here** |
| Model not on the allowed list | Add it to `Config.Creator.AllowedModels` or the prop palette |
| The per-station limit was reached | Raise `Config.Creator.MaxEmitters`, `MaxSpeakers`, `MaxScreens`, or `MaxProps` |

### `<model> is not on this game build`

The model ships with a newer GTA update. Set `sv_enforceGameBuild 3095` or newer in `server.cfg`, or pick another model. Models listed in `Config.ModelFallbacks` are swapped automatically and the substitution is logged.

### DJ desks show as plain tables, fog is faint

The server runs an old game build (FiveM defaults to 1604). Set `sv_enforceGameBuild 3095` or newer. The club smoke machine and the DJ desks need it.

### `Stations defined in config.lua cannot be deleted in-game`

Remove the station from `Config.Stations` and run `/gsdj reload`. To undo in-game edits of a config station instead, run `/gsdj reset <id>`.

### Access rule changes on a config station do not stick

Access rules of config stations always come from `config.lua`. Edit `permissions` in the file and run `/gsdj reload`.

### `Config reload failed, check the server console`

`config.lua` has a Lua error. The console line names the file position. Fix it and run `/gsdj reload` again, or restart the resource.

### Import says `Export file not found in data/exports/`

Pass the file name exactly as it is in `data/exports/`, with or without `.json`. Pasting the JSON itself into the Manage tab's **Import** box also works.

---

## Screens and Lights

### A screen stays black

`diag` lists every screen with its mode:

- `the prop is not in the world` means the model is missing on this build and no fallback loaded.
- In `'texture'` mode, `a black screen means the render target name is wrong`. Find the model's `script_rt_*` texture with a texture viewer and set it as `txn` in `Config.Screens.Models`, or remove `mode` so the screen uses drawn (`'poly'`) mode.

### Two stations show the same video

Both use the same screen model in `'texture'` mode, which shares one texture across every copy of the model. Switch that model to drawn (`'poly'`) mode.

### The light show is not running

`diag` says why: you are beyond `Config.Effects.RenderDistance`, the station has no emitters, every emitter is switched off, or the **Light show** master is off in the DJ panel.

### Lights flicker or some are missing

The engine drops script lights at random past a certain count. Lower `Config.Effects.MaxLights`, or remove emitters from dense stages.

### Fog ignores the colour

The particle in use has no tint channel. Keep `Config.Effects.ColouredHaze = true` so the cloud is lit from inside, and use game build 3095 or newer for the tintable club smoke machine. `diag` shows the particle in use and whether it is tintable.

### Low frame rate at a big stage

Lower `Config.Effects.MaxShadowLights` first, then `MaxLights` and `ParticleDistance`. Keep `LightsWithShadow = false`.

---

## Uploads and Library

| Message | Cause |
|---------|-------|
| `Only .mp3, .ogg and .wav files are allowed` | Wrong file type, or the type is missing from `Config.LocalMedia.AllowedExtensions` |
| `File is too large` | Over `Config.LocalMedia.MaxSizeMB` |
| `That file is not a valid audio file` | The content does not match the extension |
| `The server could not save the file` | The `data/media/` folder is missing or not writable |
| `Upload session expired, try again` | More than five minutes without progress |
| `Local media is disabled on this server` | `Config.Playback.AllowLocalMedia = false` |

### Uploads are slow

On a plain-http address uploads are sent in small chunks. Put the server behind https so uploads go through in a single request.

### Songs dropped into `data/media/` do not appear

Run `/gsdj scan`. File names with spaces or characters other than letters, digits, `_`, `-`, and `.` are skipped; rename them. See [Server Music Library](administration.md#server-music-library).

---

## Saving and Storage

### Stations or playlists are lost after a restart

- The `data/` folder must exist inside the resource with its JSON files. FiveM cannot create folders.
- Confirm the server process can write to the resource folder. The console logs `SaveResourceFile failed` when it cannot.
- In database mode, check that `oxmysql` starts first. The console logs `UseDatabase = true but oxmysql is not started` otherwise.

### A JSON file was ignored on boot

The console logs `data/<file>.json is not valid JSON and was ignored` after a bad manual edit. Restore it from a backup.

---

## Information to Send Support

- GS DJ Studio version
- Framework and framework version, and the game build (`sv_enforceGameBuild`)
- The `[gs-djsystem]` lines from the server console at boot
- The full `/gsdj diag` output from F8, taken while standing at the station
- The exact toast message and the steps that reproduce the problem
- Whether `config.lua`, the open Lua files, `html/`, or the resource folder name was changed

Remove license identifiers, Discord IDs, IP addresses, and player names before sharing logs publicly.
