# Installation

## Step 1: Add the Resource

1. Copy the resource into your server's `resources` directory.
2. Keep the shipped folder name exactly as `gs-annoucements`.
3. Add the resources to `server.cfg` in this order:

   ```cfg
   ensure oxmysql
   ensure gs-annoucements
   ```

{% hint style="warning" %}
The shipped resource name is `gs-annoucements` with this exact spelling. Renaming it can break resource paths, media URLs, and integration examples.
{% endhint %}

---

## Step 2: Configure MySQL

Set a valid oxmysql connection string before the resource starts. GS Announcements creates its tables automatically on first start; there is no SQL file to import manually.

The default table prefix is `gs_announce_`. Change it before the first start if the server needs a different prefix:

```lua
Config.Database = {
    TablePrefix = "gs_announce_",
}
```

If legacy `data/*.json` files exist, the resource imports them once and records the completed migration in the meta table. The files are then left untouched as a backup.

---

## Step 3: Grant Administrator Access

### Identifier Mode

Identifier mode is the default. Join the server and run:

```text
/gsid
```

Copy the printed `license:` identifier into `config.lua`:

```lua
Config.PermissionMode = "identifiers"
Config.Admins = {
    "license:1234567890abcdef1234567890abcdef12345678",
    { id = "discord:198765432109876543", name = "Head Admin" },
}
```

`license`, `license2`, `steam`, `discord`, `fivem`, `xbl`, `live`, and `ip` identifiers are accepted. License identifiers are recommended because IP addresses change and Discord is not always linked.

### ACE Mode

Set:

```lua
Config.PermissionMode = "ace"
Config.AcePermission = "gsannounce.admin"
```

Then grant the ACE in `server.cfg`:

```cfg
add_ace group.admin gsannounce.admin allow
add_principal identifier.license:YOUR_LICENSE_HERE group.admin
```

Entries in `Config.Admins` remain valid in ACE mode, so both methods can be combined.

---

## Step 4: Configure Player Access

Player mode lets non-admins create announcements for their own jobs and submit them for review:

```lua
Config.PlayerMode = {
    Enabled = true,
    JobProvider = "auto",
    MaxPending = 10,
}

Config.Whitelist = {
    Enabled = true,
}
```

With the whitelist enabled, add allowed jobs or players from **Manage → Access Control** after the first admin signs in. Set `Config.Whitelist.Enabled = false` to let every detected job use the limited Builder.

Player mode is optional. Set `Config.PlayerMode.Enabled = false` for a strictly admin-only panel.

---

## Step 5: Configure Businesses

Replace the example businesses and coordinates in `Config.Businesses`. The `name` is matched exactly when the resource looks up a default GPS waypoint.

```lua
Config.Businesses = {
    {
        name = "Bean Machine",
        category = "Cafe",
        coords = { x = -634.6, y = 226.6, z = 81.9 },
    },
}
```

The Builder's map picker can override these coordinates for an individual announcement.

---

## Step 6: Verify the Installation

Restart the resource, then run these from the server console:

```text
gsannounce:admins
gsannounce:db
gsannounce:whitelist
```

The output should show the MySQL connection, created table row counts, parsed administrators, whitelist state, and content rules.

Join the server and run:

```text
/gsannounce
```

The default keybind is **F6** and can be rebound under FiveM key bindings.

---

## Updating the Web Interface

A prebuilt `web/dist` is expected to ship with the resource. Only rebuild when changing files under `web/src`:

```bash
cd web
npm install
npm run build
```

Files in `web/public` are copied into `web/dist` during every build. Put self-hosted announcement media in `web/public/media`, not directly in `web/dist`.
