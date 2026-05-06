# Configuration

All configuration is in `config/jagdauifgaiu.lua`.

---

## Debug

```lua
Config.Debug = true
```
Enables verbose console output for troubleshooting.

---

## Zombie Tasks

```lua
Config.EnableZombieTasks = true
```
`false` = zombies wander randomly without attack AI. Use for custom logic in open files.

---

## Zombie Models

```lua
Config.Zombies.Models = {
    `g_m_m_zombie_01`,  -- Bottom Dollar DLC
    `g_m_m_zombie_02`,
    `g_m_m_zombie_03`,
    `u_m_y_zombie_01`,  -- Legacy GTA zombie
}
```

---

## Infected Animals

```lua
Config.Zombies.Animals.Enabled = true
Config.Zombies.Animals.SpawnChance = 20  -- percent
Config.Zombies.Animals.RestrictToRegions = false
Config.Zombies.Animals.Models = {
    { model = `a_c_boar`, weight = 35 },
    { model = `a_c_shepherd`, weight = 40 },
    { model = `a_c_mtlion`, weight = 25 },
}
```

| Option | Description |
|--------|-------------|
| `Enabled` | Toggle animal zombie spawning |
| `SpawnChance` | Percent chance a spawn uses an animal model |
| `RestrictToRegions` | Limit animals to configured regions |
| `Models` | Weighted animal model pool |
| `Regions` | Optional spawn regions: `{ x, y, z, radius }` |

---

## Walking Styles

```lua
Config.Zombies.Walking = {
    "move_m@drunk@verydrunk",
    "move_m@drunk@moderatedrunk",
    "move_m@drunk@a",
    "anim_group_move_ballistic",
    "move_lester_CaneUp",
}
```
Legacy movement clipsets used for non-DLC zombie peds.

---

## Human Locomotion

```lua
Config.Zombies.HumanLocomotion = {
    WanderStyle = "mixed",  -- legacy, zombie, or mixed
    MixedZombieChance = 45,
    AttackUsesZombieClipset = true,
    LegacyOnlyModels = { `u_m_y_zombie_01` },
    ZombieClipset = {
        Movement = "clipset@anim@ingame@move_m@zombie@core",
        Strafe = "clipset@anim@ingame@move_m@zombie@core",
        Action = "clipset@anim@ingame@move_m@zombie@strafe",
        WeaponAnimation = "ZOMBIE",
        TransitionSpeed = 0.25,
    }
}
```

| Option | Description |
|--------|-------------|
| `WanderStyle` | `legacy` = drunk clipsets, `zombie` = DLC clipsets, `mixed` = random |
| `MixedZombieChance` | Chance of zombie clipset when using `mixed` |
| `AttackUsesZombieClipset` | Switch to zombie clipset on aggro |
| `LegacyOnlyModels` | Models forced to legacy movement |
| `ZombieClipset` | DLC zombie movement clipsets |

---

## Spawning

```lua
Config.Zombies.SpawnInterval = 1       -- minutes between spawn waves
Config.Zombies.SpawnAmount = 5         -- zombies per vendor per wave
Config.Zombies.SpawnRadius = 100.0     -- radius around player
Config.Zombies.MaxAmount = 50          -- global cap
Config.Zombies.SpawnVendors = 1        -- players chosen per wave
Config.Zombies.UsePlayerRoutingBucket = true
Config.Zombies.NetworkSyncWait = 5     -- seconds
Config.Zombies.ServerCommandThreadTime = 5
```

| Option | Description |
|--------|-------------|
| `SpawnInterval` | Minutes between spawn waves |
| `SpawnAmount` | Zombies per selected player per wave |
| `SpawnRadius` | Radius around player for spawn positions |
| `MaxAmount` | Maximum living zombies at once |
| `SpawnVendors` | How many random players get zombies each wave |
| `UsePlayerRoutingBucket` | Zombies inherit player's routing bucket |
| `NetworkSyncWait` | Client timeout for zombie entity sync |

---

## Safe Zones

```lua
Config.Zombies.SafeZones = {
    Enabled = false,
    Zones = {
        -- { x = -1370.75, y = -2937.86, z = 13.94, radius = 100.0 },
    }
}
```
Prevents new zombie spawns inside zones. Existing zombies can still wander in.

---

## Living Cleanup

```lua
Config.Zombies.LivingCleanup = {
    Enabled = true,
    CheckInterval = 60,
    Distance = 250.0,
    MinAge = 30,
    MaxDeletesPerCheck = 15,
    DeleteWhenNoPlayers = true,
    OnlyWhenAtLimit = false,
    RespectRoutingBuckets = true,
}
```

| Option | Description |
|--------|-------------|
| `Enabled` | Delete far-away zombies |
| `CheckInterval` | Seconds between cleanup checks |
| `Distance` | Max distance before cleanup |
| `MinAge` | Min seconds alive before eligible |
| `MaxDeletesPerCheck` | Max deleted per tick (0 = unlimited) |
| `DeleteWhenNoPlayers` | Delete all when server is empty |
| `OnlyWhenAtLimit` | Only cleanup when at MaxAmount |
| `RespectRoutingBuckets` | Only same-bucket players keep zombies alive |

---

## Perception

```lua
Config.Zombies.Perception = {
    CheckInterval = 1.0,
    SightDistance = 20.0,
    SightAngle = 140.0,
    SightLosFlags = 17,
    Hearing = { Enabled = true },
}
```

| Option | Description |
|--------|-------------|
| `CheckInterval` | Seconds between perception checks |
| `SightDistance` | Max visual aggro range |
| `SightAngle` | Vision cone in degrees (360 = all directions) |
| `SightLosFlags` | LOS trace flags for `HasEntityClearLosToEntity` |
| `Hearing.Enabled` | Use GTA native hearing |

---

## World Population

```lua
Config.Zombies.World = {
    DisablePopulationInRoutingBuckets = true,
    DisableTraffic = true,
    DisableParkedVehicles = true,
    DisableAmbientPeds = true,
    DisableScenarioPeds = true,
    DisableRandomCops = true,
    DisableRandomBoats = true,
    DisableGarbageTrucks = true,
}
```
Suppresses ambient population while zombies are active.

---

## Stats

```lua
Config.Zombies.Stats = {
    Health = 300,
    Armour = 25,
    CriticalHits = false,
    RagdollOnHit = false,
    RagdollFromPlayerImpact = false,
    DiesWhenInjured = false,
    Proofs = {
        Enabled = false,
        Bullet = false, Fire = false, Explosion = false,
        Collision = false, Melee = false, Steam = false, Drown = false,
    },
}
```

---

## Combat

```lua
Config.Zombies.Combat = {
    Ability = 2,           -- 0 poor, 1 average, 2 professional
    Movement = 3,          -- 0 stationary, 1 defensive, 2 offensive, 3 suicidal
    Range = 0,             -- 0 near/melee, 1 medium, 2 far
    Alertness = 3,         -- 0-3
    Accuracy = 70,
    SeeingRange = 60.0,
    HearingRange = 45.0,
    MoveRateOverride = 1.05,
    DisableFleeing = true,
    AlwaysFight = true,
    CanAttackFriendly = true,
    CanEvasiveDive = false,
    CanSwitchWeapon = false,
    BlockingEvents = true,
    DisablePainAudio = true,
    StopSpeaking = true,
    MeleeCombatLimits = {
        Enabled = true,
        Primary = 6,
        Secondary = 4,
        Population = 10,
    },
}
```

`MeleeCombatLimits` uses `SetPedMeleeCombatLimits` to allow more attackers. Requires a compatible FiveM artifact.

---

## Attack

```lua
Config.Zombies.Attack.Mode = "animation"  -- "combat" or "animation"
```

### Animation Mode

```lua
Config.Zombies.Attack.Animation = {
    Dict = "melee@unarmed@streamed_core_fps",
    Name = "ground_attack_0_psycho",
    BlendIn = 8.0,
    BlendOut = 1.0,
    Duration = 900,       -- ms
    ImpactDelay = 450,    -- ms
    Flag = 48,
    Range = 1.6,
    FacingAngle = 100.0,
    Damage = 25,
    Interval = 1500,      -- ms
    DamageFeedback = {
        Enabled = true,
        ScreenEffect = "DamageHitIn",
        ScreenEffectDuration = 350,
        CameraShake = "SMALL_EXPLOSION_SHAKE",
        CameraShakeIntensity = 0.08,
    },
}
```

| Option | Description |
|--------|-------------|
| `Duration` | Total animation length (ms) |
| `ImpactDelay` | When damage applies during animation (ms) |
| `FacingAngle` | Cone target must be within when hit lands. 360 = disabled |
| `Damage` | HP per hit |
| `Interval` | Min ms between hits from same zombie |

### DLC Native Melee

```lua
Config.Zombies.Attack.DlcNativeMelee = {
    Enabled = true,
    Models = { `g_m_m_zombie_01`, `g_m_m_zombie_02`, `g_m_m_zombie_03` },
}
```
Uses Rockstar's Bottom Dollar zombie melee on DLC peds.

---

## Vehicle Interaction

```lua
Config.Zombies.VehicleInteraction = {
    Enabled = true,
    AttackDistance = 2.5,
    ApproachDistance = 1.0,
    BodyDistancePadding = 0.75,
    ServerValidationPadding = 4.0,
    Damage = {
        Enabled = true,
        Interval = 1500,
        BodyHealthDamage = 35.0,
        EngineHealthDamage = 75.0,
        DisableVehicleWhenEngineHealthAtOrBelow = 0.0,
    },
    DragOut = {
        Enabled = false,
        Interval = 4000,
        Chance = 35,
        MaxVehicleSpeed = 2.5,
        OpenDoor = true,
        InstantExit = false,
    },
}
```

---

## Loot

```lua
Config.Zombies.Loot = {
    Enabled = true,
    TargetSystem = "auto",      -- auto, ox_target, qb-target, none
    Inventory = "auto",         -- auto, ox_inventory, qb, qb-inventory, none
    InteractionDistance = 2.0,
    Label = "Loot Zombie",
    Icon = "fas fa-hand",
    Rewards = {
        { name = "bandage", min = 1, max = 1, chance = 25 },
        { name = "water",   min = 1, max = 1, chance = 15 },
        { name = "lockpick", min = 1, max = 1, chance = 10 },
    },
}
```

---

## Audio

```lua
Config.Zombies.Audio = {
    Enabled = true,
    SoundSystem = "builtin",    -- builtin, auto, xsound, none
    Distance = 18.0,
    Volume = 0.35,
    MinInterval = 12000,
    MaxInterval = 28000,
    Listener = "camera",        -- camera or ped
    UpdateInterval = 100,
    MaxActiveEmitters = 10,
    Spatial = {
        RefDistance = 2.0,
        RollOffFactor = 1.35,
        MaxDistance = 28.0,
        ConeInnerAngle = 360.0,
        ConeOuterAngle = 360.0,
        ConeOuterGain = 1.0,
        PitchVariance = 0.06,
        Occlusion = {
            Enabled = true,
            VolumeMultiplier = 0.45,
            LowPassFrequency = 1350.0,
        }
    },
}
```

### Audio Categories

Full categorized audio pools in `Config.Zombies.Audio.Categories`:

- **Human**: Idle, Alert, Chase, Attack, Hit, Death
- **Animals**: Dog (Idle, Alert, Attack, Death), Boar (Idle, Alert, Attack, Death), Cougar (Idle, Alert, Attack, Death)
- **Vehicle**: Hit, Drag
- **Impact**: Miss
- **Horde**: Distant, Start, Ambient, Ended
- **Corpse**: Despawn

---

## Dead Zombie Cleanup

```lua
Config.Zombies.EnableDelayedCleanup = true
Config.Zombies.DeadZombieCleanupTime = 30     -- seconds
Config.Zombies.CleanupCheckInterval = 5        -- seconds
```

---

## Corpse Despawn

```lua
Config.Zombies.CorpseDespawn = {
    Effect = "sink",       -- instant, fade, sink, sink_fade
    Duration = 4500,       -- ms
    StartDelay = 0,
    SinkDistance = 1.2,
    DisableCollision = true,
    FreezeDuringEffect = true,
    Fade = true,
    FinalAlpha = 0,
}
```
