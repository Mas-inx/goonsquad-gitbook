# Administration

---

## Access Levels

GS DJ Studio has four access layers, all enforced on the server. Hiding controls in the interface is not the security boundary.

| Layer | Purpose | Granted By |
|-------|---------|------------|
| Listener | Hear stations, use personal volume and streamer mode, manage their own playlists | Everyone |
| DJ | Hold a booth's DJ lock: queue, playback, effects, soundboard, uploads | The station's access rules (see below) |
| Creator | Open the creator and build new stations; import stations | `gsdj.creator` ace, `Config.Creator.Jobs`, or `Config.Creator.Identifiers` |
| Owner / Admin | Manage a station: settings, access rules, elements, export, delete, take over the DJ lock | The station's creator owns it; `gsdj.admin` manages every station |

```cfg
add_ace group.admin gsdj.admin allow
add_ace group.mod gsdj.creator allow
```

An admin passes every check: creator access, every station's access rules, the booth distance check, and ownership. Admins can also remove songs from the server library.

---

## DJ Access Rules

Each station either lets **everyone** DJ or lists rules. A player may DJ when **any** rule matches.

| Rule type | Value | Matches when |
|-----------|-------|--------------|
| `ace` | An ace, e.g. `gsdj.dj` | The player has that ace |
| `job` | A job name, optional `grade` | The player's framework job matches, at or above the grade |
| `identifier` | A full identifier, e.g. `license:…` | The player has that identifier (also matches the framework id, such as `cid:` on QBCore / Qbox) |
| `discord` | A Discord role id | A supported Discord role resource reports that role for the player |

```lua
permissions = { rules = {
    { type = 'job', value = 'dj' },
    { type = 'job', value = 'nightclub', grade = 2 },
    { type = 'ace', value = 'gsdj.dj' },
} },
```

A station can have up to 16 rules. With no rules, nobody but the owner and admins can DJ there. Rules are edited in the **Manage** tab or the creator.

Discord rules read roles from `Badger_Discord_API`, `discord_perms`, or `zdiscord`, whichever is started. Without one of them, Discord rules never match.

{% hint style="warning" %}
The access rules of a station defined in `config.lua` always come from `config.lua`. Edits to them in game are ignored, so change them in the file and run `/gsdj reload`.
{% endhint %}

---

## Station Ownership

| Station source | Owner | Who can manage it | Can be deleted in game |
|----------------|-------|-------------------|------------------------|
| Built in the creator | The player who created it | The owner and admins | Yes |
| Imported | The player who imported it | The owner and admins | Yes |
| `config.lua` | None | Admins only | No. Remove it from `Config.Stations` |

Ownership is stored as the framework identifier: `cid:<citizenid>` on QBCore and Qbox (so it follows the character), `esx:<identifier>` on ESX, and the `license:` identifier on standalone.

### Editing Config Stations In Game

Admins can move, redecorate, and retune a config station in game. The edits are saved as an overlay on top of the `config.lua` definition, so they survive restarts. To throw the overlay away and return to the file's definition:

```text
/gsdj reset <id>
```

Removing a station from `Config.Stations` also removes its saved overlay on the next start.

---

## Commands

| Command | Who | What it does |
|---------|-----|--------------|
| `/gsdjcreator [id\|new]` or `/gsdj creator [id\|new]` | Creator | Open the station creator |
| `/gsdj export <id>` | Owner / admin | Write the station to `data/exports/<id>_<timestamp>.json` |
| `/gsdj import <file>` | Creator | Load an exported station from `data/exports/` |
| `/gsdj scan` | Admin | Index songs dropped into `data/media/` |
| `/gsdj reload` | Admin | Re-read `config.lua` and rebuild all stations without a restart |
| `/gsdj reset <id>` | Admin | Undo in-game edits of a config station |
| `/gsdj delete <id>` | Owner / admin | Delete a created station |
| `/gsdj release <id>` | Admin | Free a station's DJ lock and close that DJ's panel |
| `/gsdj list` | Everyone | All stations, DJs, and current tracks |

### Server Console

The same command works from the server console, printing results as JSON:

```text
gsdj list
gsdj scan
gsdj reload
gsdj export pier_stage
gsdj import pier_stage_20260928_120000.json
gsdj reset vanilla_unicorn
gsdj release bahama_mamas
```

`gsdj` with no argument prints the station list.

---

## Export and Import

**Export** (Manage tab or `/gsdj export <id>`) writes the full station definition to `data/exports/<id>_<timestamp>.json` and shows the JSON with a **Copy** button. The file never contains the owner's identifier.

**Import** (Manage tab or `/gsdj import`) accepts either pasted JSON or the name of a file inside `data/exports/`. An imported station:

- is validated like a new creator save, so elements out of range, over a limit, or using models that are not allowed are dropped;
- gets a new id if one with the same id already exists;
- is owned by the player who imported it.

Use export and import to move venues between servers or to keep backups of a build before a big change.

---

## Server Music Library

The library holds every song uploaded from a DJ's PC plus files you drop into `data/media/`.

### Adding Songs by Hand

1. Copy `.mp3`, `.ogg`, or `.wav` files into `gs-djsystem/data/media/`.
2. Run `/gsdj scan` (or `gsdj scan` in the console).

The scan indexes new files, reads their length, and drops index entries whose file is gone. It also runs on every resource start. Titles come from the file name with `_` and `-` turned into spaces.

{% hint style="warning" %}
File names may only contain letters, digits, `_`, `-`, and `.`, up to 96 characters, and must not start with `.` or `_`. Files with spaces or other characters are skipped by the scan. Rename `My Song.mp3` to `My_Song.mp3`.
{% endhint %}

### Removing Songs

Admins see a delete button on library tracks in the Queue tab search results. It removes the file and its index entry.

### How Songs Are Served

Library songs are streamed from the resource's own HTTP endpoint at `<PublicBaseUrl>/gs-djsystem/media/<file>`, with range requests so seeking works. Uploads are sent to `<PublicBaseUrl>/gs-djsystem/upload/<session>` in one request, or in small chunks over network events when that address is plain http or unreachable. Upload sessions expire after five minutes of inactivity.

---

## Data Storage

| Path | Purpose |
|------|---------|
| `data/stations.json` | Created stations, config-station overlays, and every station's queue, volume, loop mode, effects state, and current track |
| `data/playlists.json` | Player playlists, keyed by owner identifier |
| `data/history.json` | Recently played per station and server-wide play counts |
| `data/media/index.json` | Library index: title, length, size, who added it |
| `data/media/` | Uploaded and dropped-in audio files |
| `data/exports/` | Files written by export |

Writes are batched: a change is saved `Config.Persistence.AutosaveDebounce` milliseconds (2 seconds) after the last edit, and everything pending is flushed when the resource stops.

### Database Mode

With `Config.Persistence.UseDatabase = true` and `oxmysql` started, station definitions and playlists are also written to the `gs_djsystem_stations` and `gs_djsystem_playlists` tables and loaded from them on boot. Queues, history, and the media index always stay in the JSON files.

Back up the `data/` folder (and the two tables, in database mode) like any other server data.

---

## Server-side Limits

Every request is validated and rate-limited on the server.

| Limit | Default | Behaviour when exceeded |
|-------|---------|-------------------------|
| Elements per station | 64 emitters, 16 speakers, 8 screens, 64 props | Extra elements are dropped on save |
| `Config.Creator.PlacementRange` | 120 m | Elements further from the centre are dropped on save |
| `Config.Creator.MaxRadius` | 150 m | Radius is clamped |
| `Config.Playback.MaxQueue` | 100 | **The queue is full** |
| `Config.Playlists.MaxPerPlayer` / `MaxTracks` | 20 / 200 | **You have reached the playlist limit** / **This playlist is full** |
| `Config.LocalMedia.MaxSizeMB` | 50 | **File is too large** |
| `Config.Soundboard.Cooldown` | 2 s per player | The clip is ignored |
| Playback controls | 10 per second per player | **Slow down** |
| Queue edits | 5 per second per player | **Slow down** |
| Search | 2 per second per player | **Slow down** |
| Creator save | 1 per second per player | **Slow down** |
| Booth reach | Interact distance + 6 m (at least 12 m) | **You are too far from the station**. Admins are exempt |

Uploaded files are checked by content as well as extension, so a renamed non-audio file is refused.

---

## Performance

- The light show only runs for players within `Config.Effects.RenderDistance` (80 m); particles stop at `ParticleDistance` (45 m).
- At most `Config.Effects.MaxLights` script lights and `MaxShadowLights` shadow casters are drawn per frame across every station in range. Beams win; coloured haze gives way.
- Props, screens, and speakers are client-side objects spawned out to the larger of `RenderDistance` and the station's max distance, capped at `Config.Effects.WorldDistanceMax`. They cost nothing from the OneSync entity budget.
- An audio page exists only for a station that is playing and within the player's range of it.

For a big festival stage, lower `MaxLights`, `ParticleDistance`, or `MaxShadowLights` first.
