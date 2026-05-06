# Installation

## Step 1: Add to Server

1. Copy the `gs_zombies` folder into your server's `resources` directory.
2. Add to `server.cfg`:
   ```
   ensure gs_zombies
   ```
   Ensure it starts after any framework or inventory resources.

## Step 2: Requirements

- **OneSync** must be enabled in your `server.cfg`:
  ```
  set onesync on
  ```
- **Game build 3258 or newer** is required:
  ```
  sv_enforceGameBuild 3258
  ```
- **Lua 5.4** is recommended for best performance.

## Step 3: Configuration

All configuration is in `config/jagdauifgaiu.lua`. Key settings to review:

```lua
Config.Framework = 'auto'  -- or set to your framework
Config.Zombies.MaxAmount = 50  -- adjust based on server size
```

See [Configuration](configuration.md) for the full reference.

## Step 4: Loot System (Optional)

If you want zombie looting, set up your target and inventory systems.

### Target System

In `config/jagdauifgaiu.lua`:

```lua
Config.Zombies.Loot.TargetSystem = "auto"  -- or "ox_target", "qb-target", "none"
Config.Zombies.Loot.Inventory = "auto"      -- or "ox_inventory", "qb", "qb-inventory", "none"
```

### Loot Rewards

Edit the `Config.Zombies.Loot.Rewards` table to match your server's items:

```lua
Config.Zombies.Loot.Rewards = {
    { name = "bandage", min = 1, max = 1, chance = 25 },
    { name = "water",   min = 1, max = 1, chance = 15 },
    { name = "lockpick", min = 1, max = 1, chance = 10 },
}
```

## Step 5: Open Files (Customization)

Two files are unencrypted for your custom logic:

- `client/openclient.lua` — Client-side zombie spawn hook
- `server/openserver.lua` — Server-side zombie hooks (spawn, loot)

No encryption means you can modify freely.

---

## Troubleshooting

**Zombies don't spawn:**
- Verify OneSync is enabled.
- Check `Config.Zombies.MaxAmount` isn't too high for your server.
- Ensure game build is 3258+.
- Check server console for debug messages (enable `Config.Debug = true`).

**Zombies don't attack:**
- Verify `Config.EnableZombieTasks = true`.
- Check perception settings — `SightDistance`, `SightAngle`, and `Hearing`.

**No zombie audio:**
- Ensure `Config.Zombies.Audio.Enabled = true`.
- Check audio files exist in `audio/generated/`.
- FiveM browsers require HTTPS; audio uses `cfx-nui-` protocol.

**Loot interaction not showing:**
- Verify `Config.Zombies.Loot.Enabled = true`.
- Ensure your target system (ox_target/qb-target) is started.
- Check the `Loot.TargetSystem` config matches your installed resource.

**Performance issues:**
- Reduce `Config.Zombies.MaxAmount` and `Config.Zombies.SpawnAmount`.
- Increase `Config.Zombies.SpawnInterval`.
- Disable animal spawning (`Animals.Enabled = false`).
- Reduce audio distance or disable audio.
