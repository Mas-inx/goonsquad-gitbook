# GS Handling Studio

GS Handling Studio is an in-game vehicle handling & engine-sound editor for FiveM. Players open a movable, resizable tuning panel while driving, edit physics values (mass, drive force, traction, suspension, brakes and more) and swap engine audio **live**, then save tunes as per-model overrides or named profiles — no `.meta` editing, no restarts.

**Price:** Free | [Get on Tebex](https://store.goonsquadstudios.com/package/7643007)

{% embed url="https://www.youtube.com/watch?v=-_mgHdu91PU" %}

{% hint style="success" %}
GS Handling Studio is **fully open source** — every Lua file is escrow-ignored, so you can read, learn from, and modify all of the code.
{% endhint %}

---

## Highlights

### Live Handling Editor

- Edit mass, drag, drive force, gears, drive bias, brakes, traction curve/bias, suspension (springs, dampers, ride height, anti-roll, roll centre), steering lock, centre of mass, and more
- Sliders plus typed numeric inputs with sensible ranges, and keyboard fine-tuning
- Changes apply instantly while you drive — the panel keeps game input alive so you feel every change in real time

### Engine & Exhaust Sound

- Swap the engine audio bank between 14 curated character sets (muscle V8, NASCAR, V10/V12 supercar, turbo inline-4/6, rotary, diesel, rally, EV, hyper-hybrid, and more)
- Pitch, volume, backfire/pop, and turbo-flutter parameters
- Turbo flutter is made audible by fitting the turbo mod when supported — an existing turbo is never stripped

### Presets & Profiles

- 6 built-in presets: TRACK, DRIFT, STREET, OFFROAD, RACE, DEMO
- Name and save full tunes as custom profiles, stored per player
- Per-model runtime overrides re-apply automatically whenever the player drives that model

### Code Export

- One click generates a complete `<Item type="CHandlingData">` block ready to paste into a `handling.meta`
- An audio config snippet with the matching `ForceVehicleEngineAudio` call is generated alongside it

### Mod-Friendly Runtime

- Keeps a clean base snapshot per model and only writes the fields you changed on top of it
- Applies once per vehicle instance — no per-frame enforcement loop, so mechanic scripts and engine swaps are never fought
- Client and server exports/events for clean interoperability (see [API & Exports](exports.md))

---

## Compatibility

| Framework | Support |
|---|---|
| Standalone | Supported (no framework required) |
| QBCore / Qbox / ESX | Works alongside — no framework hooks needed |

### Storage

| Backend | Requirement |
|---|---|
| MySQL | `oxmysql` |
| JSON flat files | None (built-in fallback) |

The storage backend is selected automatically: MySQL when `oxmysql` is running, JSON otherwise. See [Configuration](configuration.md).

---

## Requirements

- A modern FiveM server artifact
- `oxmysql` (optional — only for MySQL persistence)

---

## Next Steps

1. Follow [Installation](installation.md).
2. Review [Configuration](configuration.md).
3. Learn the editor in [Usage](usage.md).
4. Integrate other scripts through [API & Exports](exports.md).
