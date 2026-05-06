# API & Exports

---

## Server Events

### `gs_zombies:__server:lootZombie(netid)`

Triggered by client when a player interacts with a lootable zombie. Handles loot reward generation and item granting.

```lua
-- netid: network ID of the zombie entity
```

### `gs_zombies:__server:updateZombiePerception(netid, task, targetServerId)`

Sent by the zombie-owning client when perception state changes (wander -> attack).

```lua
-- netid: network ID of the zombie
-- task: "wander" or "attack"
-- targetServerId: server ID of the target player
```

### `gs_zombies:__server:attemptZombiePlayerAttack(netid, targetServerId)`

Server-validated player damage request. Checks range, facing angle, cooldowns, and ownership before applying damage.

### `gs_zombies:__server:attemptZombieVehicleDamage(netid, targetServerId, vehicleNetId)`

Server-validated vehicle damage request. Verifies zombie is close enough and attacking the correct target.

### `gs_zombies:__server:attemptZombieVehiclePullOut(netid, targetServerId, vehicleNetId)`

Server-validated drag-out attempt. Checks speed, proximity, and configured chance.

### `gs_zombies:__server:deleteZombie(netid)`

Deletes a zombie that failed to sync to a client (network timeout).

---

## Client Events

### `gs_zombies:__client:applyZombiePlayerDamage(damageAmount)`

Applies damage to the local player ped with damage feedback effects.

### `gs_zombies:__client:applyZombieVehicleDamage(vehicleNetId)`

Applies body + engine damage to a vehicle.

### `gs_zombies:__client:forceZombieVehicleExit(vehicleNetId, doorIndex)`

Forces a player out of their vehicle (used by drag-out system).

### `gs_zombies:__client:startCorpseDespawn(netid, effect, duration, startDelay, sinkDistance, ...)`

Starts a corpse despawn effect on a dead zombie entity.

### `gs_zombies:__client:FixZCoordinateForZombie(netid, x, y, z)`

Spawns a zombie at safe ground coordinates. Triggered by server after ped creation.

### `gs_zombies:__client:showNotification(message)`

Shows a GTA-style feed notification.

---

## Open File Hooks

These functions in `server/openserver.lua` can be overridden:

```lua
function OnZombieSpawn(zombie)
    -- Called when zombie is created on server
end

function CanLootZombie(source, zombie)
    -- Return false to prevent looting
    return true
end

function GetZombieLoot(source, zombie)
    -- Return custom loot table or nil for default
    return nil
end

function OnZombieLoot(source, zombie, rewards)
    -- Called after loot is granted
end
```
