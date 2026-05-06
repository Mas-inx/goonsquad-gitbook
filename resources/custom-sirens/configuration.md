# Configuration

All configuration is in `config/config.lua`.

---

## Debug

```lua
Config.Debug = true
```
Enables verbose console output for troubleshooting.

---

## Framework

```lua
Config.Framework = {
    Mode = "auto",  -- auto, none, esx, qbcore, qbox
    JobLock = {
        Enabled = false,
        AllowOffDuty = false,
        Jobs = {
            police = true,
            sheriff = true,
            state = true,
        }
    }
}
```

| Option | Description |
|--------|-------------|
| `Mode` | Framework detection mode. `auto` detects from running resources |
| `JobLock.Enabled` | Restrict siren use to specific jobs |
| `JobLock.AllowOffDuty` | Allow off-duty players with the correct job to use sirens |
| `JobLock.Jobs` | Table of job names allowed to use sirens |

---

## Controls

```lua
Config.Controls = {
    Enabled = true,
    RegisterKeyMappings = true,
    RequestCooldown = 250,
    NotifyDenied = true,
    Slots = {
        [1] = { Description = "LEO Siren Slot 1", DefaultMapper = "keyboard", DefaultKey = "NUMPAD1" },
        [2] = { Description = "LEO Siren Slot 2", DefaultMapper = "keyboard", DefaultKey = "NUMPAD2" },
        [3] = { Description = "LEO Siren Slot 3", DefaultMapper = "keyboard", DefaultKey = "NUMPAD3" },
        [4] = { Description = "LEO Siren Slot 4", DefaultMapper = "keyboard", DefaultKey = "NUMPAD4" },
        [5] = { Description = "LEO Siren Slot 5 / Long Tone", DefaultMapper = "keyboard", DefaultKey = "NUMPAD5" },
    },
    StopAll = {
        Command = "gsleosirens_stop",
        Description = "Stop active LEO siren",
        DefaultMapper = "keyboard",
        DefaultKey = "NUMPAD0",
    }
}
```

| Option | Description |
|--------|-------------|
| `Enabled` | Master toggle for all siren controls |
| `RegisterKeyMappings` | Auto-register FiveM key mappings (Esc > Settings > Key Bindings) |
| `RequestCooldown` | Milliseconds between siren requests from the same client |
| `NotifyDenied` | Show notification when a siren request is denied |
| `Slots[1-5]` | Siren slot configuration with key bindings |
| `StopAll` | Key to stop all active sirens on the vehicle |

---

## Audio

```lua
Config.Audio = {
    Enabled = true,
    ListenerMode = "camera",   -- camera or player
    ListenerUpdateInterval = 50,
    VehicleUpdateInterval = 75,
    VehicleDiscoveryInterval = 1500,
    StaleEmitterTimeout = 15000,
    Volume = 0.80,
    InsideVehicleVolumeMultiplier = 0.55,
    RefDistance = 18.0,
    MaxDistance = 525.0,
    RolloffFactor = 0.65,
    ConeInnerAngle = 360.0,
    ConeOuterAngle = 360.0,
    ConeOuterGain = 1.0,
    Pitch = 1.0,
    Occlusion = {
        Enabled = true,
        LosFlags = 17,
        LowPassFrequency = 1450.0,
        VolumeMultiplier = 0.70,
    }
}
```

| Option | Description |
|--------|-------------|
| `Enabled` | Master toggle for siren audio |
| `ListenerMode` | `camera` = audio relative to camera, `player` = relative to player ped |
| `ListenerUpdateInterval` | ms between listener position updates |
| `VehicleUpdateInterval` | ms between active siren emitter updates |
| `VehicleDiscoveryInterval` | ms between fallback scans for new siren vehicles |
| `StaleEmitterTimeout` | ms before removing an emitter not seen in updates |
| `Volume` | Master volume (0.0 - 1.0) |
| `InsideVehicleVolumeMultiplier` | Volume reduction when inside the siren vehicle |
| `RefDistance` | Distance at which audio is at full volume |
| `MaxDistance` | Maximum audible distance |
| `RolloffFactor` | Volume rolloff rate over distance |
| `ConeInnerAngle/ConeOuterAngle/ConeOuterGain` | Directional audio cone settings |
| `Pitch` | Global pitch multiplier |
| `Occlusion` | Wall/object obstruction simulation with low-pass filter |

---

## Native Siren Suppression

```lua
Config.NativeSirenSuppression = {
    Enabled = true,
    ScanInterval = 750,
    ScanDistance = 375.0,
}
```

Automatically mutes GTA's default sirens on configured vehicles and restores them when custom sirens stop.

---

## Sirens

Each siren is defined with per-sound spatial audio properties:

```lua
Config.Sirens = {
    wail = {
        Label = "Wail",
        File = "sounds/generated/police_wail_01.mp3",
        Volume = 1.0,
        RefDistance = 22.0,
        MaxDistance = 575.0,
        RolloffFactor = 0.58,
        Pitch = 1.0,
    },
    -- ... 20+ additional siren tones
}
```

### Available Sirens

| ID | Label | Max Distance |
|----|-------|-------------|
| `wail` | Wail | 575 |
| `yelp` | Yelp | 525 |
| `phaser` | Phaser | 475 |
| `hilo` | Hi-Lo | 525 |
| `airhorn` | Airhorn | 325 |
| `powercall_long` | Powercall Long Tone | 425 |
| `q_windup` | Q Siren Windup | 625 |
| `q_roll` | Q Siren Roll | 625 |
| `deep_wail` | Deep Wail | 625 |
| `wide_wail` | Wide Wail | 575 |
| `priority_yelp` | Priority Yelp | 525 |
| `urgent_yelp` | Urgent Yelp | 525 |
| `rapid_phaser` | Rapid Phaser | 475 |
| `piercer_phaser` | Piercer Phaser | 450 |
| `us_hilo` | US Hi-Lo | 525 |
| `euro_hilo_alt` | Euro Hi-Lo Alt | 525 |
| `dual_warble` | Dual Tone Warble | 525 |
| `intersection_burst` | Intersection Burst | 475 |
| `rumbler_low` | Rumbler Low | 325 |
| `rumbler_pulse` | Rumbler Pulse | 325 |
| `manual_wail_pull` | Manual Wail Pull | 575 |
| `evac_low_high` | Evac Low-High | 450 |

Each siren supports: `Label`, `File`, `Volume`, `RefDistance`, `MaxDistance`, `RolloffFactor`, `Pitch`, `ConeInnerAngle`, `ConeOuterAngle`, `ConeOuterGain`.

---

## Vehicles

Configure which vehicles get custom sirens and which slots they use:

```lua
Config.Vehicles = {
    [`police`] = {
        Label = "Police Cruiser",
        Enabled = true,
        RequireOccupant = true,
        RequireDriver = true,
        AllowPassengers = false,
        AllowOutsideVehicle = false,
        RequireEngineOn = false,
        StopWhenDestroyed = true,
        DisableDefaultSiren = true,
        EnableEmergencyLightsWithCustomSiren = true,
        TurnOffEmergencyLightsOnStop = false,
        Volume = 1.0,
        Sirens = {
            [1] = "Wail",       -- String: resolved by label or ID
            [2] = "Yelp",
            [3] = "Phaser",
            [4] = "Hi-Lo",
            [5] = { Siren = "Powercall Long Tone", Mode = "hold" },  -- Full slot config
        },
    },
    -- [`police2`] = { ... },
    -- [`sheriff`] = { ... },
}
```

### Slot Configuration

Slots can be defined three ways:

**String (by siren ID):**
```lua
[1] = "wail"
```

**String (by siren label):**
```lua
[1] = "Wail"
```

**Full table:**
```lua
[1] = {
    Label = "Custom Wail",           -- Display name
    Siren = "wail",                  -- Siren ID from Config.Sirens
    Mode = "toggle",                 -- "toggle" or "hold"/"momentary"
    Volume = 0.85,                   -- Per-slot volume multiplier
}
```

### Vehicle Options

| Option | Description |
|--------|-------------|
| `Enabled` | Enable custom sirens for this model |
| `RequireOccupant` | Player must be inside the vehicle |
| `RequireDriver` | Player must be the driver |
| `AllowPassengers` | Allow passengers to control sirens |
| `AllowOutsideVehicle` | Allow siren control from outside (with custom hook) |
| `RequireEngineOn` | Engine must be running |
| `StopWhenDestroyed` | Stop siren when vehicle is destroyed |
| `DisableDefaultSiren` | Mute GTA's default siren |
| `EnableEmergencyLightsWithCustomSiren` | Auto-toggle emergency lights |
| `TurnOffEmergencyLightsOnStop` | Turn off lights when siren stops |
| `Volume` | Per-vehicle volume multiplier |
| `Sirens` | Table of 5 siren slot definitions |
