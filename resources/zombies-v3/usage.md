# Usage

---

## How Zombies Work (Server Owner Guide)

Zombies spawn in waves around random players. Each wave selects `SpawnVendors` random players and spawns `SpawnAmount` zombies within `SpawnRadius` of each.

Zombies use a perception system:
- **Sight**: Line-of-sight check within configured angle and distance
- **Hearing**: GTA native `CanPedHearPlayer` detection

When a zombie detects a player, it transitions: `wander -> alert -> chase -> attack`

---

## Zombie Behavior States

| State | Description |
|-------|-------------|
| Wander | Default. Random movement with idle sounds |
| Alert | Initial detection. Audio cue played |
| Chase | Pursuit with snarl/breath audio |
| Attack | Melee range. Configurable damage + animation |
| Dead | Corpse state. Loot target registration |

---

## Attack Modes

### Animation Mode (default)
Zombies play a custom attack animation (`ground_attack_0_psycho`). Damage is applied at a timed `ImpactDelay` within the animation. Requires the zombie to be facing the target within `FacingAngle`.

### Combat Mode
Uses GTA's native `TaskCombatPed`. Simpler but less cinematic. Animals always use direct melee tasks.

### DLC Native Melee
Bottom Dollar Bounties DLC zombie peds (`g_m_m_zombie_01/02/03`) can use Rockstar's built-in zombie melee animations when `DlcNativeMelee.Enabled = true`.

---

## Vehicle Interaction

When a player is inside a vehicle and a zombie is aggroed:

1. Zombie paths to the vehicle using `TaskGoToEntity`
2. At `AttackDistance`: zombie can damage the vehicle (body + engine health)
3. If `DragOut.Enabled`: zombie has a chance to pull the player out
   - Only works at speeds below `MaxVehicleSpeed`
   - Door opens automatically if `OpenDoor = true`
   - `InstantExit` forces the player out more aggressively

---

## Loot System

Dead zombies become lootable after death. Loot interaction uses:

- **ox_target** or **qb-target** for interaction UI
- **ox_inventory**, **qb-inventory**, or **qb-core** for item grants

Rewards are configured per-item with min/max quantity and percentage chance. Each zombie can only be looted once.

Custom loot logic can be added via `openserver.lua` using the `GetZombieLoot(source, zombie)` function.

---

## Corpse Despawn Effects

| Effect | Description |
|--------|-------------|
| `instant` | Immediately deleted |
| `fade` | Alpha fades to transparent |
| `sink` | Model sinks into ground |
| `sink_fade` | Combines sinking + fading |

Duration, delay, and sink distance are all configurable.

---

## World Suppression

When enabled, the following are disabled while zombies are active:
- Moving traffic
- Parked vehicles
- Ambient peds
- Scenario peds
- Random cops
- Random boats
- Garbage trucks

Routing bucket population can also be suppressed to keep zombie-active buckets clear.

---

## Audio System

### Built-in NUI Audio
Uses the FiveM browser's Web Audio API for 3D positional sound. No external dependencies required. Maximum of `MaxActiveEmitters` emitters update per frame (nearest zombies prioritized).

### xsound Backend
Alternative audio system. Set `SoundSystem = "xsound"` if you have xsound installed.

### Occlusion
When enabled, audio is muffled when line-of-sight is blocked between the zombie and the player's camera/ped. Volume is reduced and a low-pass filter is applied.

---

## Customization via Open Files

### `client/openclient.lua`

```lua
function OnZombieSpawn(Zombie)
    -- Called when a zombie ped becomes ready on the client
    -- Zombie = ped handle
end
```

Default applies:
- Block non-temporary events
- Combat tuning
- Disable pain audio
- Stop speaking
- Animal-specific flee attributes

### `server/openserver.lua`

```lua
function OnZombieSpawn(zombie)
    -- Called when a zombie is spawned on the server
    -- zombie = server entity handle
end

function CanLootZombie(source, zombie)
    return true  -- Return false to block looting
end

function GetZombieLoot(source, zombie)
    return nil  -- Return a custom loot table override
end

function OnZombieLoot(source, zombie, rewards)
    -- Called after loot is granted
end
```
