# Installation

This guide covers the release package for GS Minimap Designer. The compiled interface is included.

---

## 1. Install the Resource

Place `gs-minimapdesigner` in your server's resources directory. Keep that folder name: the companion resource and integration examples use it.

The release is arranged as:

```text
releaseminimapdesigner/
  gs-minimapdesigner/
  private_modules/
    gsmd-ai/
```

Install the main resource first. The companion is optional and stays separate from the main resource's upload package.

Keep `web/build/`, `stream/`, `data/`, `custom_maps/` and `licenses/` with the resource. Source build tools are not needed on the server.

---

## 2. Choose Storage

The release uses `Config.Storage = 'mysql'`. Start the database adapter first:

```cfg
ensure oxmysql
ensure gs-minimapdesigner
```

The resource creates `gs_minimap_designs` and `gs_minimap_designs_thumbs` automatically. No SQL import is required.

For a standalone file-based installation, set this in `config/config.lua`:

```lua
Config.Storage = 'json'
```

Then only `ensure gs-minimapdesigner` is needed. If MySQL is selected but `oxmysql` is not started, the resource logs a warning and uses JSON storage for that session.

{% hint style="warning" %}
Choose your storage backend deliberately. Changing backends does not migrate the entire project catalogue. Back up both the database and `data/` before changing an existing installation.
{% endhint %}

---

## 3. Grant Editor Access

Grant the editor ACE to your admin group in `server.cfg`:

```cfg
add_ace group.admin minimapdesigner.edit allow
add_principal identifier.license:YOUR_LICENSE_IDENTIFIER group.admin
```

Alternatively, add your full identifier in the server-only `config/server.lua`:

```lua
Config.Admins = {
    'license:YOUR_LICENSE_IDENTIFIER',
}
```

Either method grants access to editing, saving, publishing, exports and AI requests. Set `Config.AcePermission = false` to use only the identifier list. The release ships with an empty list.

---

## 4. Configure Media

The release uses `Config.MediaProvider = 'database'`, which keeps image data inside the saved design and requires no credential. With JSON storage, those images live inside the JSON design files.

For hosted media, select `discord` or `fivemanage` and fill the matching field in `server/credentials.lua`. See [Configuration](configuration.md#media-storage).

Never put provider credentials into a shared or client script.

---

## 5. Check Map Conflicts

The live renderer is bundled at `stream/minimap_main_map.gfx`. Disable competing copies of that asset in other map or postal resources. The base installation does not require a separate tile-drawing resource.

Your HUD still controls the radar layout. `Config.FlatRadar = true` keeps the radar flat while live tiles are displayed to avoid clipping in vehicles.

---

## 6. Optional AI Companion

Copy `private_modules/gsmd-ai` into your resources directory as `gsmd-ai`, then configure its own `config.lua` and start it after the designer:

```cfg
ensure gs-minimapdesigner
ensure gsmd-ai
```

For top-down captures of the game world, also install and start `screenshot-basic`. Prompt generation and map snips do not require it. See [AI Companion](ai.md).

---

## 7. Verify the Installation

1. Join with an authorized identifier and run `/minimapdesigner`.
2. Confirm the map imagery and blip picker load.
3. Create a named sheet, add a blip and a text label, and select **Save draft**.
4. In **Radar**, enable **Bake onto radar tiles** and select **Apply to my radar now**.
5. Publish the sheet and confirm another player sees the result.
6. Enable postals, publish again, and test `/postal <code>` with a visible code.
7. Restart the resource and confirm the published sheet loads again.

See [Troubleshooting](troubleshooting.md) if any step fails.
