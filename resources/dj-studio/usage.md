# Usage

---

## Player Guide

### Opening a Booth

Walk to a station's booth (the lime ring on the floor) and press **E**, or use your target resource when `Config.InteractionMode = 'target'`. The prompt shows the station name, who is DJing, and what is playing. `/gsdj` opens the nearest booth within 12 metres.

Only one DJ holds a booth at a time. The first player with access who opens a free booth takes the DJ lock. The lock is released when the DJ closes the panel or leaves the server, or when an owner or admin takes over. With `Config.Playback.KeepPlayingWithoutDj = true` the music keeps playing after the DJ walks away.

| Badge | Who | Can do |
|-------|-----|--------|
| **You are the DJ** | The player holding the DJ lock | Full control: queue, playback, effects, soundboard |
| **Manager** | The station's owner or an admin while someone else holds the lock | Full control, the **Manage** tab, and **Take over DJ lock** |
| **DJ: name** / **Viewer** | Anyone with the panel open who does not have control | Watch only. Controls are locked |

Other players who try to open a booth that is in use get **This booth is in use by** and the DJ's name.

### Player Commands

| Command | What it does |
|---------|--------------|
| `/gsdj` | Open the nearest booth |
| `/gsdj menu <id>` | Open a specific station by id |
| `/gsdj playlist` | Your playlists, from anywhere |
| `/gsdj streamer [on/off]` | Mute every station for yourself only (streamer mode). No argument toggles it |
| `/gsdj volume <0-100>` | Set your personal volume. No value shows the current one |
| `/gsdj list` | Every station with distance, element counts, DJ, and current track |
| `/gsdj diag` | Write playback, screen, and light-show diagnostics to the F8 console |

Personal volume and streamer mode are stored on your own PC and apply to every station.

---

## The DJ Panel

The panel is split into the **deck** (what is playing and the transport controls) and a drawer with four tabs.

| Key | Action |
|-----|--------|
| `Space` | Play / pause |
| `Esc` | Close the panel |

### Deck

| Control | Notes |
|---------|-------|
| Progress bar | Click or drag to seek, when the track length is known |
| Restart, Back, Play / pause, Forward, Next, Stop | Back and Forward seek by `Config.Playback.SeekSeconds` (10 by default) |
| Loop | Cycles **off**, **queue** (finished tracks go back to the end), and **track** (repeat the current one) |
| Volume | Station volume heard by everyone, 0–100 |
| Light show | Master switch for the station's emitters |
| Soundboard | Opens the soundboard pop-up |
| My volume / Streamer | Your personal volume and streamer mode |
| Minimal mode | Collapses the panel to the deck so you can see the room |

The **Up next** strip shows the next tracks in the queue.

### Queue Tab

Add music in any of these ways:

| Source | How |
|--------|-----|
| **Search** | Type in the search box. It searches the server library and previously played tracks, and YouTube too when `Config.YouTube.ApiKey` is set |
| **YouTube link** | Paste any YouTube link. The title and thumbnail are looked up automatically |
| **Direct audio link** | Paste an https link ending in `.mp3`, `.ogg`, or `.wav` |
| **Upload from my PC** | Pick an `.mp3`, `.ogg`, or `.wav` file up to `Config.LocalMedia.MaxSizeMB` (50 MB). It is added to the server library for everyone |

If nothing is playing, the first track added starts straight away; the rest are queued. Each queue row can be played now, moved up or down, played next, removed, or saved to one of your playlists. **Clear** empties the queue.

Below the queue, **Recently played here** and **Top songs on this server** offer one-click picks.

A track whose length is unknown (some direct links) plays until it ends in the listeners' audio pages. The server then moves to the next track.

### Playlists Tab

Create up to `Config.Playlists.MaxPerPlayer` playlists (20 by default) with up to 200 tracks each. Add tracks from search results or the queue with **Add to my playlist**, reorder or remove them, rename or delete a playlist, and **Load into the queue** to queue the whole thing.

Playlists belong to the player: per character on QBCore and Qbox, per identifier on ESX, and per license on standalone. `/gsdj playlist` opens them anywhere, without a booth.

### Effects Tab

The **Light board** controls groups of emitters at once: **All**, then one group per type (lights, spotlights, lasers, fog, fire, sparklers). For each group you can switch it on or off, set brightness (Low / Mid / High), speed (Slow / Fast), and colour.

Below the board, **Elements** lists every emitter. Select one to edit it:

| Setting | Notes |
|---------|-------|
| Enabled | Switch this emitter on or off |
| Colour | Swatches from `Config.ColorSwatches` or a custom colour |
| Intensity, Speed, Swing | Sliders. Swing is the animation amplitude in degrees |
| Preset | `static`, `fan`, `sweep`, `cross`, `beat`, `rainbow` |
| Light shape | Lights and spotlights: **Beam**, **Wash**, or **Bulb** |
| Aim | **Aim down**, **Level**, **Aim up** |
| Apply to every emitter of this type | Copies the change to all emitters of the same type |

The type filter and **All on** / **All off** buttons narrow the list. Effect changes are live for everyone and are saved with the station.

### Soundboard

Ten clips ship with the resource: Airhorn, Applause, Siren, Drum roll, Crowd, Boom, Scratch, Rewind, Hands up, and Let's go. Everyone within the station's max distance plus `Config.Soundboard.Margin` hears them with distance falloff. Each player can fire one clip every `Config.Soundboard.Cooldown` milliseconds.

### Manage Tab

Visible to the station owner and admins.

| Section | Contents |
|---------|----------|
| Settings | Label, radius, max distance, falloff, interact distance, floor marker |
| Access | **Everyone can DJ here**, or a list of rules. See [DJ Access Rules](administration.md#dj-access-rules) |
| Elements | Every speaker, emitter, screen, and prop, with remove |
| Tools | **Edit in world (creator)**, **Export**, **Import**, **Take over DJ lock**, **Delete station** |

---

## Station Creator

Open the creator with `/gsdjcreator` (or `/gsdj creator`). Standing inside an existing station opens it for editing, which needs you to own it or be an admin; anywhere else starts a new station at your position. `/gsdjcreator <id>` edits a specific station and `/gsdjcreator new` forces a new one.

Creator access needs the `gsdj.creator` ace, an entry in `Config.Creator.Jobs` or `Config.Creator.Identifiers`, or `gsdj.admin`.

### Camera and Cursor

| Input | Action |
|-------|--------|
| `W` `A` `S` `D` | Fly |
| `Q` / `E` | Down / up |
| `Shift` | Fly faster (`Config.Creator.FastMultiplier`) |
| Mouse wheel | Change fly speed |
| Right-drag | Look around |
| `Alt` | Hide or show the panel for free look |
| `CapsLock` | Toggle the cursor |
| `Esc` | Step back: stop placing, cancel a grab, clear the selection, then exit |

### Placing Elements

The **Add** palette places lights, spotlights, lasers, fog, fire, sparklers, speakers, screens, and props. Pick the model from the **Speaker model**, **Screen model**, or **Prop model** dropdown first, or type a **Custom model** that is on the allow-list. Click in the world to drop; placing repeats until you press **Stop placing** or `Esc`.

The creator warns when a model does not exist on the current game build, because it would save but never appear.

### Selecting and Editing

| Input | Action |
|-------|--------|
| Click | Select an element |
| `Shift` + click | Add to the selection |
| Drag a box | Select many |
| Gizmo arrows | Drag to move |
| Lime ring | Drag to rotate the heading |
| Red ring | Drag to aim lights and fire up or down |
| `Ctrl` | Snap while dragging or rotating |
| `R` / `T` | Rotate by `Config.Creator.RotateStep` |
| `Alt` + wheel | Rotate the selection |
| `Ctrl` + wheel | Raise or lower the selection |
| Arrow keys | Nudge horizontally (`Shift` = ×5) |
| `Page Up` / `Page Down` | Nudge vertically |
| `G` | Grab: the selection follows the mouse; click to drop |
| `Delete` / `Backspace` | Remove the selection |
| Right-click | Context menu: move with mouse, drop here, rotate, drop to ground, duplicate, focus, remove, set centre here, set booth here |

The **Selected** panel shows the element's fields (position, heading, pitch, roll, type, model, light shape, enabled) for exact values. **Placed elements** lists everything with a filter by kind; click a row to select it or **Focus** to fly to it.

### Station Settings

| Button | Effect |
|--------|--------|
| **Set booth here** | Puts the DJ interaction point where the camera is aimed, facing your view direction |
| **Set centre here** | Moves the station centre only. Refused if it would leave elements further than `Config.Creator.PlacementRange` away |
| **Move whole station here** | Moves the centre, booth, and every element together |
| **Go to centre** | Flies the camera back to the station centre |
| **Clear all elements** | Removes everything placed |

Label, radius, max distance, falloff, interact distance, floor marker, and access rules can be set in the same panel.

### Stage Templates

Pick a template from the dropdown, stand where the **crowd** will be, look at the DJ spot, and press **Use stage template**. The stage is built facing you: the crowd side lands where you are standing. The creator measures every model in game and finds the real floor under each item, so props sit on the ground and stacked items sit on each other.

A template replaces the station's current elements and sets its radius and max distance. Everything it places can be adjusted afterwards.

### Saving

Press **Save station**. The server validates every element. Anything out of range, over a limit, or using a model that is not allowed is dropped, and a toast says how many and why. **Delete station** removes a created station; stations from `config.lua` cannot be deleted in game.

---

## How Playback Works

- The server owns each station's queue, current track, position, and volume. Every client derives the position from the same server clock, and the server re-broadcasts it every `Config.Playback.ResyncInterval` to correct drift.
- Each listener in range plays the track in a hidden audio page. Speaker coverage, direction, height, falloff, the low-pass filter, the station volume, and your personal volume are combined into what you hear.
- A station with no speakers uses one virtual speaker at its centre.
- The next track starts when the current one reaches its known length. For tracks without a length, the listeners' pages report the end and the server moves on.
- Queue, volume, loop mode, and the current track survive a restart. A track that was playing comes back paused at the same position.

---

## Engine Limitations

- **Track BPM** cannot be read from a stream. The `beat` preset pulses at `Config.Effects.BeatIntervalMs` (120 BPM by default).
- **Script lights** are limited by the engine. Past a certain count the game drops them at random, which looks like flickering. `Config.Effects.MaxLights` keeps the show under that ceiling.
- **Shadow-casting lights** are the most expensive effect. Keep `Config.Effects.MaxShadowLights` low.
- **Texture-mode screens** show the same video on every copy of that model in the world. Use drawn (`'poly'`) screens when two nearby stations use the same model.
- **Particles** that have no tint channel ignore the colour picker. Coloured haze lights them from inside so the colour still reads.
- **YouTube videos** whose uploader disabled embedding cannot play in game. `/gsdj diag` names this case.
