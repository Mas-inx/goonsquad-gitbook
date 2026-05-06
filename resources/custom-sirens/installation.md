# Installation

## Step 1: Add to Server

1. Copy the `gs_leosirens` folder into your server's `resources` directory.
2. Add to `server.cfg`:
   ```
   ensure gs_leosirens
   ```

## Step 2: Requirements

- **OneSync** must be enabled:
  ```
  set onesync on
  ```
- **Lua 54** is required (`lua54 'yes'` in fxmanifest).

## Step 3: Configuration

All configuration is in `config/config.lua`. Key settings to review:

```lua
Config.Framework.Mode = "auto"  -- or "none", "esx", "qbcore", "qbox"
Config.Framework.JobLock.Enabled = false  -- set to true to restrict by job
```

Add your police vehicle model names to `Config.Vehicles`:

```lua
Config.Vehicles[`police`] = {
    Label = "Police Cruiser",
    Enabled = true,
    -- ... see configuration reference
}
```

See [Configuration](configuration.md) for the full reference.

## Step 4: Job Lock Setup (Optional)

In `config/config.lua`:

```lua
Config.Framework.JobLock = {
    Enabled = true,
    AllowOffDuty = false,
    Jobs = {
        police = true,
        sheriff = true,
        state = true,
    }
}
```

Framework auto-detection handles QBCore, ESX, and Qbox automatically. Set `Config.Framework.Mode` to force a specific framework.

## Step 5: Open Files (Customization)

Two files are unencrypted for custom logic:

- `client/openclient.lua` — Client-side hooks (custom vehicle control, permission checks, notifications)
- `server/openserver.lua` — Server-side hooks (job/permission checks)

---

## Troubleshooting

**No siren audio heard:**
- Ensure OneSync is enabled.
- Check `Config.Audio.Enabled = true`.
- Verify audio files exist in `sounds/generated/`.
- Check distance from the siren vehicle — default max is 525 units.
- FiveM browsers require HTTPS; audio uses `cfx-nui-` protocol.

**Siren keybinds don't work:**
- Default binds are NUMPAD1-5 and NUMPAD0 for stop.
- Check `Config.Controls.RegisterKeyMappings = true`.
- Verify no other resource is using the same keybinds.

**Vehicle not playing sirens:**
- Ensure the vehicle model is in `Config.Vehicles` with `Enabled = true`.
- Check the vehicle's model name matches your config key.
- Ensure `DisableDefaultSiren = true` is set if you want to mute GTA sirens.

**Job lock not working:**
- Verify `Config.Framework.JobLock.Enabled = true`.
- Check your framework is being detected (set `Config.Framework.Mode` manually if needed).
- Confirm your job name matches a key in `JobLock.Jobs`.

**Siren stops immediately:**
- If using hold mode, releasing the key stops the siren.
- Check `StopWhenDestroyed` if the vehicle is damaged.
