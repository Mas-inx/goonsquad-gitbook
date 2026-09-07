# Installation

## Step 1: Add the Resource

1. Copy the `gs-map-studio` folder into your server's `resources` directory.
2. Add the resource to `server.cfg`:

   ```cfg
   ensure gs-map-studio
   ```

No database, no dependencies, and no build step are required. If you use job-based access, make sure your framework core (`qbx_core`, `qb-core`, or `es_extended`) starts **before** `gs-map-studio` so it is detected.

{% hint style="info" %}
Everything the studio saves — maps, autosaves, prefabs, exports, and published resources — is written as files inside the `gs-map-studio` folder. Keep the `data/`, `exports/`, and `published/` folders in place; the server cannot create directories that do not exist.
{% endhint %}

---

## Step 2: Grant Access

A player may open the studio when **any** of the three checks in `config.lua` passes. Pick the one that fits your server.

### ACE Permission (default)

`Config.Permissions.UseAce` is `true` out of the box. Grant the ace in `server.cfg`:

```cfg
add_ace group.admin gsms.use allow      # who may open the studio
add_ace group.admin gsms.admin allow    # optional: delete anyone's maps and prefabs, override locks
```

### Identifier Allow-list

Add stable identifiers to `Config.Permissions.AllowedIdentifiers`:

```lua
AllowedIdentifiers = {
    'license:1234567890abcdef1234567890abcdef12345678',
    'discord:198765432109876543',
},
```

`license:` identifiers are recommended. Any identifier type returned by the server is accepted (`license`, `discord`, `fivem`, `steam`, and so on).

### Framework Job

Map job names to a minimum grade. This is only checked when a framework bridge is active:

```lua
AllowedJobs = {
    ['admin'] = 0,
    ['realestate'] = 3,
},
```

{% hint style="warning" %}
The shipped `config.lua` may contain example license identifiers in `AllowedIdentifiers`. Remove any entries that are not yours before going live.
{% endhint %}

---

## Step 3: Set the Command and Key

The defaults are `/mapstudio` with the alias `/mapeditor`. Change them in `config.lua`:

```lua
Config.Command      = 'mapstudio'
Config.CommandAlias = 'mapeditor'   -- '' to disable
Config.OpenKey      = ''            -- e.g. 'F7'; players can rebind it under FiveM key bindings
```

---

## Step 4: Optional Add-on Props

Any model streamed by another resource becomes placeable by listing it in `Config.CustomProps`. It appears in the prop library like a stock prop:

```lua
Config.CustomProps = {
    { model = 'bzzz_prop_plant_04', label = 'Potted Monstera', category = 'custom', sub = 'Bzzz Plants' },
}
```

Builders can also add custom props themselves from the library panel; those are stored per player and never touch `config.lua`.

---

## Step 5: Verify the Installation

Restart the resource and check the server console. On boot you should see:

```
[gs-map-studio] storage ready: 0 map(s), 0 prefab(s).
```

Join the server and run:

```text
/mapstudio
```

The studio opens straight into FLY mode. Tap **Alt** to bring up the cursor, open the **Maps** panel from the bottom shelf, and create your first map.

---

## Previewing the Interface Outside the Game

`web/index.html` opens in a normal browser with mock data for interface preview. The `scripts/` folder contains dev-only Node helpers (a Lua parse gate, a static server, and an NUI smoke test). They are not used in game and are safe to delete from a production install.
