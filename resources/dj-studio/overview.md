# GS DJ Studio

GS DJ Studio turns any spot on your map into a working venue. Players step up to a booth, queue YouTube videos, direct audio links, or songs uploaded from their own PC, and everyone nearby hears the same track **in sync** through positional 3D speakers. A live light show, music-video screens, a soundboard, and an in-game venue creator with nine one-click stage templates come with it.

**Version:** 1.0.0 | **Price:** $9.99 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7702129)

{% embed url="https://youtu.be/lnJylwJQwZw" %}

{% hint style="info" %}
GS DJ Studio is protected by FiveM Asset Escrow. `config.lua`, the framework bridge (`shared/framework.lua`), the booth interaction and target bridge (`client/interaction.lua`), and the storage layer (`server/persistence.lua`) remain open and editable. The interface, language files, and soundboard clips are plain files and can also be edited.
{% endhint %}

---

## Features

### Synced Playback
- One authoritative queue per station, played in sync for every listener in range
- YouTube links (played as video), direct `.mp3` / `.ogg` / `.wav` links, and songs uploaded from the DJ's PC
- Library of server-hosted songs: drop files into `data/media/` and index them with one command
- Play, pause, seek, restart, skip, stop, loop the track or the whole queue, and per-station volume
- Periodic position re-broadcast corrects drift, and the queue keeps playing after the DJ walks away
- Optional YouTube search by name with exact track lengths through a YouTube Data API v3 key

### Positional Audio
- Real speakers: each one is a sound source with its own coverage radius and direction
- Linear, quadratic, or exponential falloff with a hard cutoff distance per station
- Directional speakers, vertical fade between floors, and HRTF panning
- A low-pass filter muffles the music as you walk out of the club, like hearing it through a wall
- Personal volume and a streamer mode that mutes every station for that player only

### Light Show
- Six emitter types: lights, spotlights, lasers, fog, fire jets, and cold-spark sparklers
- Six animation presets: static, fan, sweep, cross, beat, and rainbow
- Light shapes: a visible beam, a soft wash, or an omnidirectional bulb
- A live light board in the DJ panel: switch whole groups, set brightness, speed, and colour at once
- Coloured haze: fog and fire are lit from inside so any colour shows
- Hard performance ceilings on lights, shadow casters, and particle range

### Screens
- Screens show the station's live picture: a YouTube track plays as video on the prop
- Drawn mode works on any flat prop with no texture names needed; texture mode replaces a model's render target
- Idle card when nothing is playing

### Venue Creator
- Freecam editor with a 3D gizmo: drag arrows to move, rings to rotate and aim
- Place speakers, emitters, screens, and decorative props from a curated palette
- Box select, grab, duplicate, drop to ground, arrow-key nudging, and Ctrl snapping
- Nine one-click stage templates: nightclub, cocktail lounge, beach bar, festival concert, rooftop terrace, warehouse rave, neon disco, pool party, and street block party
- Automatic model fallbacks keep a stage complete on older game builds

### Community Features
- Personal playlists that follow the player (per character on QBCore and Qbox)
- "Recently played here" and "Top songs on this server" recommendations
- Ten-clip soundboard (airhorn, applause, siren, and more) heard by everyone near the station
- Per-station DJ access: everyone, or rules by ACE, job and grade, identifier, or Discord role
- One DJ lock per booth, with take-over for owners and admins
- Station export and import as JSON

### Interface
- DJ panel with Now playing, Queue, Playlists, Effects, and Manage tabs, plus a minimal mode
- "Now playing" pill when you walk into a station's coverage
- Four interaction styles: custom E prompt, 3D text, GTA help text, or a target resource
- Six languages: English, Spanish, German, French, Brazilian Portuguese, and Russian

---

## Framework Support

| Framework | Support | Usage |
|-----------|---------|-------|
| Qbox (`qbx_core`) | Full | Job rules, character names, per-character playlists, notifications |
| QBCore (`qb-core`) | Full | Job rules, character names, per-character playlists, notifications |
| ESX (`es_extended`) | Full | Job rules, character names, notifications |
| Standalone | Full | ACE, identifier, and Discord rules; license-based ownership; built-in notifications |

The framework is detected automatically. Framework data is only used for job rules, display names, ownership identifiers, and notifications.

---

## Requirements

| Dependency | Required | Purpose |
|------------|----------|---------|
| FiveM server artifact | Yes | A recent artifact with Lua 5.4 support |
| Game build 3095 or newer | Recommended | DJ desks, the club smoke machine, and several speakers ship with GTA updates. Older builds get base-game fallbacks |
| Qbox, QBCore, or ESX | Optional | Job rules and character names |
| `ox_lib` | Optional | Notifications |
| `ox_target`, `qb-target`, or `qtarget` | Optional | Third-eye booth interaction |
| `oxmysql` | Optional | Database storage. JSON files are the default |

No inventory items are used and nothing needs to be registered in an inventory resource.

---

## Quick Start

1. Follow [Installation](installation.md).
2. Review [Configuration](configuration.md).
3. Build your first venue and learn the DJ panel in [Usage](usage.md).
4. Manage access, stations, and media in [Administration](administration.md).
5. Control stations from your own scripts with [API & Exports](exports.md).
