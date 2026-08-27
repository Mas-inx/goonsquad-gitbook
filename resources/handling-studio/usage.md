# Usage

---

## Player Guide

### Opening the Editor

| Action | Default |
|---|---|
| Toggle the editor (must be in a driver seat) | **F7** / `/handling` |
| Hold to free the mouse for camera/steering while the editor is open | **Left ALT** |
| Close the editor (or the export sheet, if open) | **ESC** |

Both keys can be rebound under **Settings → Key Bindings → FiveM**.

### Driving While Editing

The panel keeps game input alive (`SetNuiFocusKeepInput`), so throttle, brake and steering still reach the game — you feel handling changes in real time. The mouse cursor drives the panel; hold **ALT** to release the cursor and look around or steer with the mouse. Typing in a numeric or name field temporarily suppresses game input so `WASD` doesn't drive the car.

### Fine Tuning

Focus a slider and use:

| Keys | Effect |
|---|---|
| `←` / `→` | ± one step |
| `Shift + ←/→` | ×10 step |
| `PageUp` / `PageDown` | ±10% |
| `Home` / `End` | Min / max |

Or type an exact value in the numeric box.

### Presets & Profiles

- Apply one of the 6 built-in presets (TRACK, DRIFT, STREET, OFFROAD, RACE, DEMO) as a starting point.
- Save your full tune as a **named profile** — profiles are stored per player and can be loaded or deleted any time.

### Engine Sound

- Pick one of the 14 sound sets to swap the engine audio bank live.
- Adjust pitch, volume, backfire/pop, and turbo flutter. Flutter fits the turbo mod when the value is above 0 and the vehicle supports it — a turbo you already have is never removed.

---

## Runtime Mode vs. Code Export

**Runtime mode (default).** Every edit is applied to your current vehicle immediately and saved (debounced ~0.7s) as the *active override* for that vehicle **model**. When you next enter a vehicle of that model, the override re-applies automatically — no file edits, no restart.

**Code export.** Click **Export Code** for:

- a complete `<Item type="CHandlingData">` block (merged base + your edits, with `CCarHandlingData` fields nested in `<SubHandlingData>`) to paste into your `handling.meta`, and
- an audio config snippet showing the `ForceVehicleEngineAudio` call and the chosen parameters.

Use export when you want the tune baked permanently into a vehicle resource.

---

## FiveM Audio Limitations (Honest Notes)

- **Engine bank swap is reliable** — it uses `ForceVehicleEngineAudio` and works well.
- **Pitch / volume** have no stable per-vehicle native; they are stored in profiles and the code export so the intent travels, but they are not applied at runtime.
- **Backfire/pop** has no stable trigger native; it drives a cosmetic exhaust-flash on throttle lift and is best-effort.
- **`fDownforceModifier`** lives on `CCarHandlingData` and is not present on every model; on models without it the write is a no-op.
- The top-speed readout is an estimate derived from `fInitialDriveMaxFlatVel`.
