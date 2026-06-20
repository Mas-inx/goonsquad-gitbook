# Installation

## Step 1: Add to Server

1. Copy the `gs_loadingscreen` folder into your server's `resources` directory.
2. Add to `server.cfg`:
   ```
   ensure oxmysql
   ensure gs_loadingscreen
   ```
3. Ensure no other loading screen resource is running alongside it.

## Step 2: Requirements

- **oxmysql** — database connector (tables auto-created on first start)

## Step 3: Admin Access

In `config.lua`:

```lua
Config.AdminIdentifiers = {
    'license:abc123...',
    'discord:123456...',
}
```

Or optionally add admins via the database table using the console command:
```
lsadminadd <server_id> <label>
```

## Step 4: Default Config

On first start, the resource auto-seeds a default config from `shared/default-config.json`. Players will see this layout immediately. Customize it via the in-game builder.

---

## Troubleshooting

**Builder won't open (/lsbuilder):**
- Ensure you have a matching identifier in `Config.AdminIdentifiers` or the `gs_loadingscreen_admins` table.
- Check `Config.AdminCommand` is set to `lsbuilder`.

**Loading screen not showing:**
- Verify no other loadscreen resource is running.
- Ensure `loadscreen_manual_shutdown 'yes'` is not being overridden by another resource.

**Video backgrounds not playing:**
- Video files must be placed in `html/media/video/`.
- Alternatively, upload via FiveManage from the builder (configure API key in `config.lua`).

**Progress stuck:**
- The progress bar waits for `NetworkIsSessionStarted()` — ensure server is not stuck in connecting state.
