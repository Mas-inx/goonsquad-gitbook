# API & Exports

GS DJ Studio exposes client and server exports for opening the panel, reading what is playing, and controlling stations from your own scripts. Server exports act with server authority: they skip the DJ lock and access rules, so call them only from trusted server code.

The internal `gs_djsystem:` network events are validated against the caller's permission on every call and are not intended for third-party scripts.

---

## Track Object

Exports that return a track use this shape:

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique id for this queue entry |
| `kind` | string | `'youtube'` or `'audio'` |
| `url` | string | YouTube video id, or the audio URL |
| `source` | string | `'youtube'`, `'url'` (direct link), or `'local'` (server library) |
| `file` | string? | Library file name, for `source = 'local'` |
| `title` | string | Track title |
| `duration` | number | Length in seconds, `0` when unknown |
| `thumb` | string? | Thumbnail URL (YouTube) |
| `by` | string | Name of the player who added it, or `'Server'` |
| `addedAt` | number | Unix timestamp |

---

## Client Exports

### OpenMenu

Opens the DJ panel for a station, or the nearest one when `stationId` is omitted. The usual access rules and DJ lock apply.

```lua
exports['gs-djsystem']:OpenMenu(stationId)
```

### OpenPlaylists

Opens the player's playlists without a booth.

```lua
exports['gs-djsystem']:OpenPlaylists()
```

### OpenCreator

Opens the station creator. Pass a station id to edit it, or nothing to start a new station at the player's position. Creator permission, and ownership when editing, are checked on the server.

```lua
exports['gs-djsystem']:OpenCreator(stationId)
```

### SetStreamerMode / IsStreamerMode

Mutes or unmutes every station for this player only. The setting is saved on the player's PC.

```lua
exports['gs-djsystem']:SetStreamerMode(true)
local muted = exports['gs-djsystem']:IsStreamerMode()
```

The same toggle is available as a local client event, for streamer-mode scripts that broadcast one:

```lua
TriggerEvent('gs_djsystem:StreamerMode', true)
```

### SetPersonalVolume

Sets the player's personal volume, 0–100.

```lua
exports['gs-djsystem']:SetPersonalVolume(50)
```

### GetNearestStation

Returns the id of the nearest station whose booth is within `maxDist` metres (default 12), or `nil`.

```lua
local stationId = exports['gs-djsystem']:GetNearestStation(20.0)
```

### GetNowPlaying

Returns what a station is playing as this client sees it, or `nil` when nothing is playing.

```lua
local np = exports['gs-djsystem']:GetNowPlaying('pier_stage')
if np then
    print(np.track.title, np.position, np.playing, np.volume)
end
```

| Field | Description |
|-------|-------------|
| `track` | [Track object](#track-object) |
| `position` | Current position in seconds |
| `playing` | `false` while paused |
| `volume` | Station volume, 0–100 |

---

## Server Exports

### GetStations / GetStation

```lua
local list = exports['gs-djsystem']:GetStations()          -- every station, owner identifiers removed
local def = exports['gs-djsystem']:GetStation('pier_stage') -- full definition, or nil
```

A station definition contains `id`, `label`, `source` (`'config'` or `'created'`), `coords`, `booth`, `radius`, `maxDistance`, `interactDistance`, `soundType`, `speakers`, `emitters`, `screens`, `props`, `permissions`, and `marker`, plus `owner` and `ownerName` on created stations. Treat it as read-only.

### IsPlaying

```lua
local playing = exports['gs-djsystem']:IsPlaying('pier_stage')
```

### GetNowPlaying

Same shape as the client export, from the server's authoritative state.

```lua
local np = exports['gs-djsystem']:GetNowPlaying('pier_stage')
```

### Play

Adds a track to a station. If nothing is playing it starts immediately; otherwise it is queued. Returns `true` on success, or `false` and an error key.

```lua
local ok, err = exports['gs-djsystem']:Play('pier_stage', 'https://www.youtube.com/watch?v=VIDEO_ID')
local ok2 = exports['gs-djsystem']:Play('pier_stage', 'https://example.com/set.mp3')
local ok3 = exports['gs-djsystem']:Play('pier_stage', 'local:opening_track.mp3')
```

`input` is a YouTube link, a direct `.mp3` / `.ogg` / `.wav` link, or `local:<file>` for a song in the server library. The `Config.Playback.Allow*` switches still apply.

| Error | Meaning |
|-------|---------|
| `err_unknown_station` | No station with that id |
| `err_bad_url` | The input is not a recognised link or library entry |
| `err_youtube_disabled` / `err_url_disabled` / `err_local_disabled` | That source is switched off in `Config.Playback` |
| `err_unknown_media` | No library file with that name |
| `err_local_unavailable` | Local media has no reachable address (`PublicBaseUrl` / `web_baseUrl`) |
| `err_queue_full` | The queue has reached `Config.Playback.MaxQueue` |

### Stop / Next

```lua
exports['gs-djsystem']:Stop('pier_stage')   -- stop and clear the current track (the queue is kept)
exports['gs-djsystem']:Next('pier_stage')   -- skip to the next queued track
```

### SetVolume

Sets the station volume heard by everyone, 0–100.

```lua
exports['gs-djsystem']:SetVolume('pier_stage', 80)
```

### SetEffects

Switches a station's light show on or off. Returns `false` for an unknown station.

```lua
exports['gs-djsystem']:SetEffects('pier_stage', false)
```

---

## State Bags

Each station's live playback state is published as a global state bag named `gsdj:<stationId>`. Read it from any client or server script; never write to it.

```lua
local state = GlobalState['gsdj:pier_stage']
if state and state.playing then
    print(('%s is playing %s'):format(state.dj or 'Nobody', state.track.title))
end
```

| Field | Description |
|-------|-------------|
| `dj` | Name of the player holding the DJ lock, or `nil` |
| `playing` / `paused` | Playback state |
| `track` | `{ id, kind, url, title, duration, thumb, source, by }`, or `nil` |
| `position` / `stamp` | Position in seconds at server game time `stamp` (milliseconds) |
| `volume` | Station volume, 0–100 |
| `loop` | `'off'`, `'all'`, or `'one'` |
| `fx` | Light show master switch |
| `qn` | Tracks in the queue |

---

## Examples

### Start a Set When a Venue Opens

```lua
-- server
RegisterNetEvent('myclub:open', function()
    exports['gs-djsystem']:SetEffects('bahama_mamas', true)
    exports['gs-djsystem']:SetVolume('bahama_mamas', 70)
    exports['gs-djsystem']:Play('bahama_mamas', 'local:house_intro.mp3')
end)
```

### Open the Booth From a Custom Interaction

```lua
-- client
exports.ox_target:addBoxZone({
    coords = vec3(-1372.9, -604.6, 30.3),
    size = vec3(2, 2, 2),
    options = { {
        label = 'DJ booth',
        onSelect = function() exports['gs-djsystem']:OpenMenu('bahama_mamas') end,
    } },
})
```

---

## Data Files

Exported stations use this envelope:

```json
{
  "format": "gs-djsystem/station",
  "version": 1,
  "exportedAt": "2026-09-28T12:00:00Z",
  "station": { "id": "pier_stage", "label": "Del Perro Pier Stage", "coords": { "x": -1660.35, "y": -1095.55, "z": 13.15 }, "...": "..." }
}
```

Treat `data/*.json` as resource-owned. Edit stations through the creator, the Manage tab, or `config.lua` rather than by hand.
