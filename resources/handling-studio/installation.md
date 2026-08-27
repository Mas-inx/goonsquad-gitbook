# Installation

## Step 1: Add to Server

1. Download the `GS-HandlingStudio` folder from [Tebex](https://store.goonsquadstudios.com/package/7643007) and place it in your server's `resources` directory.
2. Add to `server.cfg`:
   ```
   ensure oxmysql          # optional — omit to use JSON storage
   ensure GS-HandlingStudio
   ```
   If you use MySQL storage, make sure `oxmysql` starts **before** `GS-HandlingStudio`.

## Step 2: Database Setup (Optional)

Only needed if you want MySQL persistence. The resource **auto-creates its tables on first boot**, but you can import the schema manually:

```bash
mysql -u YOUR_USER -p YOUR_DB < resources/GS-HandlingStudio/sql/gs_handling.sql
```

Or paste the contents of `sql/gs_handling.sql` into HeidiSQL / phpMyAdmin / DBeaver.

This creates the following tables:

| Table | Purpose |
|-------|---------|
| `gsh_runtime` | One active override per (player, vehicle model), re-applied on spawn |
| `gsh_profiles` | Named custom tunes players can load later |

If `oxmysql` is not running, the resource stores everything as JSON files in `storage/<owner>.json` instead — no database required.

## Step 3: Restart Server

Run `refresh` then `start GS-HandlingStudio`, or restart your server entirely. On boot the console prints which storage backend was chosen:

```
[gs-handling] server ready (v1.0.0)
```

---

## Rebuilding the UI (Optional)

The React NUI is **already built** into `web/dist` — no build step is required. To rebuild it after making UI changes:

```bash
cd resources/GS-HandlingStudio/web-source
npm install
npm run build
```

The build outputs to `../web/dist` automatically.

---

## Troubleshooting

**UI doesn't open:**
- You must be in the **driver seat** of a vehicle.
- Check the F8 console for errors.
- If `Config.RequireAce = true`, make sure your player has the `gsh.use` ace (see [Configuration](configuration.md)).

**Tunes not saving:**
- With MySQL: ensure `oxmysql` starts before `GS-HandlingStudio`.
- With JSON: ensure the `storage/` folder exists inside the resource and is writable.

**Overrides not re-applying on spawn:**
- Confirm `Config.RebuildOnSpawn = true` in `shared/config.lua`.
