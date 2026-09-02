# Installation

A default GS Appearance install needs no config edit and no SQL import. The framework is detected at runtime, every table is created and repaired during startup, and the default shop zones are seeded on first run.

---

## 1. Install the Resource

Place the `gs_appearance` folder in your server's resources directory. Do not rename it — the exports, events, and `provides` aliases all use the name `gs_appearance`.

Start it after `oxmysql` and your framework:

```cfg
ensure oxmysql
ensure qbx_core          # or qb-core / es_extended / ox_core
ensure gs_appearance
```

`oxmysql` is the only hard dependency. `ox_lib` is optional: it is used for nicer notifications, text UI, and menus when the server happens to run it, and everything works without it.

{% hint style="info" %}
Detection tolerates a late start. A framework that comes up after GS Appearance is picked up on its `onResourceStart`, and the answer is only locked once a real framework is found. Starting in the order above is still recommended.
{% endhint %}

---

## 2. Remove the Resource You Are Replacing

GS Appearance replaces these. Stop and remove them from `server.cfg`:

```text
illenium-appearance
qb-clothing
qb-skinshop
esx_skin
skinchanger
fivem-appearance
bl_appearance
```

The `provides` block in `fxmanifest.lua` satisfies *other* resources that declare a dependency on those names. It does not stop them from running if they are still ensured.

About three seconds after startup the server prints a conflict banner if it finds one still up:

```text
[gs_appearance] CONFLICT: illenium-appearance still running.
[gs_appearance] gs_appearance replaces these — stop/remove them in server.cfg.
[gs_appearance] Leaving them on causes double menus, resets and lost outfits.
```

{% hint style="warning" %}
Do not delete your existing `playerskins` or `player_outfits` tables. GS Appearance reads and converts what a predecessor left behind, so players keep their look and their outfits. See [Integrations](integrations.md#migrating-from-a-predecessor).
{% endhint %}

---

## 3. Database

The tables are created and migrated during startup, retrying for about 25 seconds if the database is still coming up. No manual import is normally required.

| Table | Holds |
|---|---|
| `playerskins` | The active saved appearance per character |
| `player_outfits` | Named player outfits |
| `player_outfit_codes` | Shareable outfit codes |
| `management_outfits` | Job and gang uniforms |
| `gs_appearance_stores` | Store zones |
| `gs_appearance_item_rules` | Clothing item restrictions |

For a managed or locked-down deployment where the database account may not create tables, the schema files are in:

```text
gs_appearance/sql/
```

Setup is also self-healing on an existing install: missing columns are added, undersized columns are widened, and the `player_outfits` unique key is added only when no duplicate rows exist, so nobody's outfits are ever deleted. See [API & Exports](exports.md#database).

---

## 4. Admin Access

Admin gates `/gsadmin`, the item-rule editor, the accent color, and `/gscreatechar`. A player is an admin when **any** of these match:

| Source | Default |
|---|---|
| Identifier in `Config.Admins` | Empty |
| ACE permission | `gs_appearance.admin` |
| Qbox / QBCore permission | `god`, `admin` |
| ESX group | `superadmin`, `admin` |

The server console is always treated as admin.

Grant the ACE in `server.cfg`:

```cfg
add_ace group.admin gs_appearance.admin allow
```

{% hint style="danger" %}
`Config.Admins` ships empty and should stay that way. Anything listed there has permanent appearance-admin on that install. Use the ACE or your framework's own permissions instead.
{% endhint %}

---

## 5. Preview Images

The editor shows a real render for every piece of clothing, prop, hairstyle, face, overlay, and tattoo — about 7,900 images in total. Two settings decide where the UI loads them from:

```lua
Config.CaptureSource = 'fivemanage'
Config.CaptureBaseUrl = 'https://r2.fivemanage.com/Uq1keb5kV28kz4FMy4EB3'
```

| Value | Behavior |
|---|---|
| `fivemanage` | Images load from `Config.CaptureBaseUrl`. This is the shipped default. |
| `local` | Images are served from this resource's `default_captures/` folder over `nui://`. |

Every preview resolves as `<CaptureBaseUrl>/<resource-relative path>`, and anything that fails to resolve falls back to the local file, so a tile never goes blank. The resource ships pointed at a working CDN, so previews work with no setup.

{% hint style="warning" %}
Switching to `local` also means adding `'default_captures/**/*',` back to the `files{}` block in `fxmanifest.lua`. Registering those ~7,900 files on every start is what makes `ensure gs_appearance` slow enough to trip the `svMain seems hung` watchdog on a live restart. That glob is deliberately left out of the shipped manifest.
{% endhint %}

### Hosting the Previews Yourself

Every server running the shipped `Config.CaptureBaseUrl` loads images from the same account. To use your own storage, upload the images once and change that one line.

**1. Add your key** to `server/credentials.lua`, which is a server-only file and is never sent to clients:

```lua
GSA.Credentials = {
    FiveManageApiKey = 'YOUR_KEY_HERE',
    FiveManageUploadEndpoint = 'https://api.fivemanage.com/api/v3/file',
}
```

**2. Upload** from the resource root with Node 18 or newer. Test five images first, then run the full pass:

```bash
node tools/fivemanage_upload.mjs --fresh --limit=5
```

```bash
node tools/fivemanage_upload.mjs --fresh
```

**3. Paste the base url** the run prints when it finishes:

```text
Every url shares one prefix. Put this in shared/config.lua:

    Config.CaptureBaseUrl = 'https://r2.fivemanage.com/abc123'
```

Restart the resource. Moving to another host later is that one line — no re-upload.

{% hint style="warning" %}
`--fresh` is required when repointing at a different account. The resource ships with a complete url map, and without `--fresh` every file is skipped as "already mapped", leaving your server pointed at the original account.
{% endhint %}

The tool is resumable: after the first `--fresh` pass, plain re-runs pick up only what is still missing, so a rate limit or a dropped connection just means running it again. Useful flags are `--dry-run`, `--limit=N`, `--concurrency=N` (default 6), and `--endpoint=`.

Once `Config.CaptureBaseUrl` is set, `data/fivemanage_captures.json` is no longer read and can be deleted, which saves about 1.4 MB. The full instructions are in `gs_appearance/tools/FIVEMANAGE.md`.

{% hint style="info" %}
Any static host works, not just Fivemanage — upload `default_captures/` as-is and point `Config.CaptureBaseUrl` at the folder above it. There is nothing to run and no map to generate.
{% endhint %}

{% hint style="danger" %}
The API key is a write credential for the upload only. Keep it in `server/credentials.lua` or an environment variable, never in `shared/config.lua`, which any connected player can read.
{% endhint %}

---

## 6. Store Zones

Fifteen clothing stores, seven barbers, and six tattoo parlors are seeded into `gs_appearance_stores` on first run, at the standard GTA locations. Surgeon shops are not seeded.

Nothing else is required to start. Move, disable, or add stores from the in-game map with `/gsadmin` — see [Administration](administration.md).

---

## 7. Optional Settings

Everything below is off by default and can be left alone.

### Shop costs

```lua
Config.ClothingCost = 0
Config.BarberCost = 0
Config.TattooCost = 0
Config.SurgeonCost = 0
Config.ChargePerTattoo = false
```

Costs are charged server-side and are skipped while the character creator is open.

### Free-open commands and keybind

Appearance opens from store zones by default, which keeps clothing an in-world activity. To add chat commands:

```lua
Config.AppearanceCommands = { 'gsappearance', 'appearance' }
Config.AppearanceKeybind = 'F6'
Config.AppearanceCommandAdminOnly = true
```

The keybind binds the **first** command in the list. `Config.AppearanceCommandAdminOnly` is enforced on the server; leave it `true` unless you really want every player able to redress anywhere.

### Accent color

```lua
Config.AccentColor = '#6eb4ff'
```

This is only the initial value. An admin can change the accent live in `/gsadmin`, and the chosen color is stored server-side and applied for everyone.

---

## 8. Verify the Installation

1. Confirm the ready banner names the right framework:

```text
[gs_appearance] Server ready — stores, outfits, skins (framework: qbox)
```

2. Confirm there is no conflict banner and no `oxmysql is not running` line.
3. Confirm the seed line on a first run:

```text
[gs_appearance] Seeded 15 stores into database
```

4. Join, walk to a clothing store, and confirm the `[E] Clothing Store` prompt appears.
5. Press **E**, open the editor, and confirm the item carousel shows real preview images. If they are blank, run `/gscapcheck`.
6. Change something and press **Enter** to save.
7. Reconnect and confirm the look is restored.
8. Save an outfit with **O**, then reopen the store and load it from **Saved Outfit**.
9. Run `/gsdiag` in game and confirm `outfit write : OK`.
10. Run `/gsadmin` as an admin and confirm the store map opens.

See [Troubleshooting](troubleshooting.md) if any step fails.
