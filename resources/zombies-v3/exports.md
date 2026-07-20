# API & Exports

Profile and zone mutation exports are server-side. Dynamic entries remain registered until they are removed or `gs_zombies` restarts.

---

## Zombie Profiles

### Create or replace a profile

```lua
local success, nameOrError = exports.gs_zombies:CreateZombieProfile("brute", {
    Models = {
        { model = `g_m_m_zombie_03`, weight = 1 },
    },
    Stats = {
        Health = 900,
        Armour = 150,
    },
    Combat = {
        MoveRateOverride = 0.85,
    },
})
```

`CreateZombieProfile(name, data)` creates a profile or replaces an existing profile with the same name. Changes affect future spawns; existing zombies retain the tuning replicated when they spawned.

Compact shorthands are also accepted:

```lua
exports.gs_zombies:CreateZombieProfile("runner", {
    Model = `g_m_m_zombie_02`,
    Health = 200,
    Armor = 0,
    MovementSpeed = 1.35,
})
```

### Profile management

```lua
local removed = exports.gs_zombies:RemoveZombieProfile("brute")
local profile = exports.gs_zombies:GetZombieProfile("brute")
local profiles = exports.gs_zombies:GetZombieProfiles()
```

| Export | Returns |
|--------|---------|
| `CreateZombieProfile(name, data)` | `success, nameOrError` |
| `RemoveZombieProfile(name)` | `boolean` |
| `GetZombieProfile(name)` | Profile table or `nil` |
| `GetZombieProfiles()` | Table keyed by profile name |

---

## Profile Zones

```lua
local success, idOrError = exports.gs_zombies:CreateProfileZone("dock_brutes", {
    type = "box",
    coords = vec3(900.0, -3000.0, 6.0),
    size = vec3(500.0, 500.0, 40.0),
    rotation = 0.0,
    priority = 20,
    profiles = {
        { name = "brute", weight = 1 },
    },
})
```

The ID can instead be provided as `data.id` or `data.name`:

```lua
exports.gs_zombies:CreateProfileZone({
    name = "sandy_runners",
    coords = vec3(1700.0, 3600.0, 35.0),
    radius = 300.0,
    profiles = { "runner" },
})
```

### Link or unlink profiles

```lua
local linked = exports.gs_zombies:LinkZombieProfileToZone("dock_brutes", "walker", 10)
local unlinked = exports.gs_zombies:UnlinkZombieProfileFromZone("dock_brutes", "walker")
```

`LinkZombieProfileToZone(zoneId, profileName, weight)` requires both the zone and profile to exist. Calling it again updates the existing link's weight.

### Profile-zone management and queries

```lua
local zone = exports.gs_zombies:GetProfileZone("dock_brutes")
local zones = exports.gs_zombies:GetProfileZones()
local removed = exports.gs_zombies:RemoveProfileZone("dock_brutes")

local profileName, profile, zoneId = exports.gs_zombies:GetZombieProfileAtPosition(
    vec3(900.0, -3000.0, 6.0)
)
```

`GetZombieProfileAtPosition` also accepts separate `x, y, z` arguments. It returns the selected profile definition and matching zone ID without spawning anything.

---

## Dynamic Safe Zones

```lua
local success, idOrError = exports.gs_zombies:CreateSafeZone("temporary_event_safe", {
    type = "sphere",
    coords = vec3(215.0, -810.0, 30.0),
    radius = 80.0,
})

local inside, zoneId, zoneData = exports.gs_zombies:IsPositionInSafeZone(
    vec3(215.0, -810.0, 30.0)
)

local zone = exports.gs_zombies:GetSafeZone("temporary_event_safe")
local zones = exports.gs_zombies:GetSafeZones()
local removed = exports.gs_zombies:RemoveSafeZone("temporary_event_safe")
```

Dynamic safe zones work regardless of `Config.Zombies.SafeZones.Enabled`; that setting only controls safe zones loaded from configuration. Safe zones prevent future spawns and do not delete or pacify existing zombies.

The ID may be passed separately or included as `data.id`/`data.name`, just like `CreateProfileZone`.

---

## Supported Zone Shapes

```lua
-- Sphere
{
    type = "sphere",
    coords = vec3(0.0, 0.0, 30.0),
    radius = 100.0,
}

-- Rotated box
{
    type = "box",
    coords = vec3(0.0, 0.0, 30.0),
    size = vec3(100.0, 50.0, 20.0),
    rotation = 45.0,
}

-- Polygon/prism
{
    type = "poly",
    points = {
        vec3(0.0, 0.0, 30.0),
        vec3(100.0, 0.0, 30.0),
        vec3(100.0, 100.0, 30.0),
        vec3(0.0, 100.0, 30.0),
    },
    thickness = 20.0,
}
```

These definitions are compatible with ox_lib zone data. When ox_lib is unavailable, gs_zombies uses its own geometry checks.

---

## Client Exports

```lua
local managed = exports.gs_zombies:IsManagedZombie(ped)
local profileName, profileTuning = exports.gs_zombies:GetZombieProfile(ped)
```

| Export | Returns |
|--------|---------|
| `IsManagedZombie(ped)` | `true` when the entity is managed by gs_zombies |
| `GetZombieProfile(ped)` | `profileName, profileTuning`, or `nil` for a non-managed entity |

---

## Open File Hooks

### Client

```lua
function OnZombieSpawn(zombie, profileName, profileTuning)
    -- zombie is the local ped handle.
end
```

### Server

```lua
function OnZombieSpawn(zombie, profileName, profileZoneId)
    -- zombie is the server entity handle.
end

function CanLootZombie(source, zombie)
    return true
end

function GetZombieLoot(source, zombie)
    return nil -- Return custom rewards, or nil for configured rewards.
end

function OnZombieLoot(source, zombie, rewards)
end
```

The additional `OnZombieSpawn` arguments are backward compatible; existing one-argument hooks continue to work.

{% hint style="warning" %}
Events prefixed with `gs_zombies:__server` or `gs_zombies:__client` are internal implementation details. Use the documented exports and open-file hooks instead of triggering internal events from other resources.
{% endhint %}
