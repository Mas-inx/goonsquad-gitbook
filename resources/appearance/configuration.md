# Configuration

Almost every install runs on the shipped defaults. `shared/config.lua` is the only file a server owner normally edits; stores and item rules are managed in game from `/gsadmin` instead of a config file.

---

## File Layout

| File | Purpose | Client-readable |
|---|---|---|
| `shared/config.lua` | Framework, branding, admin access, previews, stores, costs, behavior | Yes |
| `shared/tattoos.lua` | Static tattoo and overlay catalogue by body zone | Yes |
| `data/stores.json` | Optional seed list used on the very first run | Yes |
| `data/fivemanage_captures.json` | Optional per-file preview url map | Yes |
| `server/credentials.lua` | Fivemanage upload key and endpoint | No |

These stay open and editable under FiveM Asset Escrow, together with the bridge files you may need to adapt:

| File | Purpose |
|---|---|
| `shared/framework.lua` | Framework detection shared by both sides |
| `client/framework.lua` | Client framework adapter — job, gang, player data |
| `server/framework.lua` | Server framework adapter — identity, money, jobs, appearance storage |
| `client/bridge.lua` | Notifications, text UI, input, menus, client callbacks |
| `server/bridge.lua` | ox_lib callback protocol, net-event callbacks, notification cascade |
| `client/compat/legacy_skin.lua` | Predecessor skin format conversion |
| `client/compat/qb.lua` | QBCore / Qbox events and `qb-clothing` export shims |
| `client/compat/esx.lua` | ESX events and `skinchanger` / `esx_skin` export shims |
| `server/compat_esx.lua` | ESX `users`.`skin` mirroring |
| `sql/*.sql` | Manual schema import |
| `tools/*` | Preview upload and index tooling |
| `web/dist/**` | The built NUI |
| `web/images/**` | Your own preview and icon overrides |

{% hint style="danger" %}
`shared/config.lua` is readable by any connected player. Keep the Fivemanage key in `server/credentials.lua`, which is a server script and is never sent to clients.
{% endhint %}

---

## Framework

```lua
Config.Framework = 'auto'
```

| Value | Meaning |
|---|---|
| `auto` | Detect at runtime. Correct for almost every server. |
| `qbox` | Force Qbox (`qbx_core`) |
| `qb` | Force QBCore (`qb-core`) |
| `esx` | Force ESX (`es_extended`) |
| `ox` | Force ox (`ox_core`) |
| `standalone` | No framework |

Loose spellings such as `qbx`, `QBCore`, `es_extended`, `ox_core`, and `none` are understood.

Detection probes `qbx_core` → `qb-core` → `es_extended` → `ox_core`, counting a resource as up when it is `started` **or** `starting`. Qbox is checked before QBCore on purpose: many Qbox servers still run `qb-core` as a shim for legacy resources, but only `qbx_core` owns the player there. A real framework locks the answer; a `standalone` result is re-probed every second and is never locked, so a framework that starts after GS Appearance is still picked up.

Set this explicitly only when your server runs an unusual combination and auto-detect picks the wrong adapter.

---

## Branding

```lua
Config.BrandTitle = 'GS APPEARANCE'
Config.BrandSubtitle = 'Customize'
Config.NotifyPrefix = 'GS Appearance'
Config.AccentColor = '#6eb4ff'
Config.CustomIcons = true
```

| Setting | Description |
|---|---|
| `BrandTitle` | Large title shown top-left in the editor. |
| `BrandSubtitle` | Muted line under the title. Replaced with the shop name while a shop is open. |
| `NotifyPrefix` | Title used on notifications. |
| `AccentColor` | Initial UI accent, as a `#rrggbb` hex string. |
| `CustomIcons` | `true` shows the bundled photo-style category icons; `false` falls back to the built-in line-art SVG icons. |

`AccentColor` is only the starting value. An admin changes the accent live in `/gsadmin`, and the choice is persisted server-side and pushed to every connected player, so it survives restarts and overrides this setting from then on.

### Icon Overrides

Swap any individual UI icon without rebuilding the NUI:

```lua
Config.IconOverrides = {
    tops  = 'web/images/icons/my_tops.png',       -- a file inside this resource
    hairs = 'https://cdn.example.com/hair.png',   -- or a hosted image
}
```

The key is the icon name; the value is either a resource-relative path, served over `nui://`, or a full `http`/`https` url. Anything not listed keeps its bundled icon.

A resource-relative file only has to sit somewhere already covered by the `files{}` block. `web/images/` is, so `web/images/icons/<name>.png` works with no manifest edit — drop the file in and restart the resource.

That folder sits next to `web/dist`, not inside it. `npm run build` empties `web/dist` on every run, so files placed there would be deleted by the next UI build; nothing under `web/images/` is bundled or compiled, which is why no rebuild is ever needed.

| Group | Icon names |
|---|---|
| Clothing | `shirt` `undershirt` `arms1` `legs1` `bottoms` `shoes` `hats` `glasses` `mask` `chain` `vests` `bags` `watches` `bracelets` `ears` `hanger` `design1` `hairs` |
| Face | `face1` `head1` `nose1` `nose_bone_clean` `eyes1` `eyes-lips1` `lips1` `brows` `eyebrowforward1` `cheeks` `cheek_width_clean` `chin` `chin-size` `jaw-w` `jaw-back` `jaw-chin1` `bone-w1` `neck` `twist1` `peak1` `size1` `shape` |
| Appearance | `beard` `skin` `skin1` `complexion` `blemish` `moles` `aging` `sun` `body` `body1` `highlight` `color1` `style1` `brush1` `lipstick1` `makeupcolor1` |
| Blend | `father1` `mother1` `mix1` `race1` `ped1` |
| Limbs | `l-arm1` `r-arms` |

{% hint style="info" %}
An override that fails to load falls back to the bundled icon, and then to the built-in SVG — a wrong path degrades to the shipped icon instead of a broken-image box. `Config.CustomIcons = false` turns images off entirely and overrides are ignored.
{% endhint %}

---

## Admin Access

```lua
Config.Admins = {}
Config.AdminAce = 'gs_appearance.admin'
Config.QBPermissions = { 'god', 'admin' }
Config.ESXGroups = { 'superadmin', 'admin' }
Config.AdminCommand = 'gsadmin'
```

| Setting | Description |
|---|---|
| `Admins` | Explicit identifier list (`license:`, `discord:`, `fivem:`, `steam:`). |
| `AdminAce` | ACE permission checked with `IsPlayerAceAllowed`. |
| `QBPermissions` | Qbox / QBCore permission names accepted as admin. |
| `ESXGroups` | ESX groups accepted as admin. |
| `AdminCommand` | Command that opens the admin store map. |

A player is an admin if **any** of the four match. The server console is always admin. Every admin action is validated on the server, so the client-side check is only used to decide what the UI offers.

{% hint style="danger" %}
Leave `Config.Admins` empty. An identifier committed there has permanent appearance-admin on every install of the resource. Grant admin with the ACE or your framework's own permission system.
{% endhint %}

---

## Preview Images

```lua
Config.CaptureRoot = 'default_captures/clothing'
Config.AppearanceCaptureRoot = 'default_captures/appearance'
Config.CaptureSource = 'fivemanage'
Config.CaptureBaseUrl = 'https://r2.fivemanage.com/Uq1keb5kV28kz4FMy4EB3'
```

| Setting | Description |
|---|---|
| `CaptureRoot` | Folder holding clothing and prop renders. |
| `AppearanceCaptureRoot` | Folder holding hair, face, overlay, and tattoo renders. |
| `CaptureSource` | `fivemanage` loads previews from a CDN; `local` serves them from this resource over `nui://`. |
| `CaptureBaseUrl` | The CDN to load from. Change this one line to repoint every preview. |

In `fivemanage` mode a preview resolves in three tiers:

1. `Config.CaptureBaseUrl` + `/` + the resource-relative path
2. the per-file map in `data/fivemanage_captures.json`, when no base url is set
3. the local file over `nui://`, so nothing ever goes blank

Because the base url is a plain config value, moving to another account or host is a config edit and a restart — no re-upload and no rebuild. Once it is set the url map is never read and can be deleted, saving about 1.4 MB. See [Installation](installation.md#5-preview-images) for hosting the previews on your own account.

{% hint style="info" %}
Any static host works. Upload `default_captures/` as-is and point `CaptureBaseUrl` at the folder above it. The per-file map is only for a host that does not mirror the folder layout — leave `CaptureBaseUrl = ''` and supply your own `path -> url` pairs.
{% endhint %}

If you change either capture root, re-upload with `--fresh` — the layout is keyed to the defaults.

Local overrides can be dropped into the `web/images/` folder using the `{collection}_{index}.png` naming scheme; they are used when a capture is missing or fails to load. Preview overrides must sit directly in that folder — subfolders are only searched for icon overrides, which are addressed by their full path.

---

## Stores

```lua
Config.StoreInteractControl = 38
Config.StoreInteractLabel = '[E] Customize appearance'
Config.DefaultStoreRadius = 3.0
```

| Setting | Description |
|---|---|
| `StoreInteractControl` | Control index used to enter a store. `38` is **E**. |
| `StoreInteractLabel` | Fallback prompt, used when a shop type has no entry in `ShopInteractLabels`. |
| `DefaultStoreRadius` | Radius in meters for stores created from the admin map. Per-store radius is clamped to 1.0–25.0. |

Store locations themselves are not configured here. They live in `gs_appearance_stores` and are managed from [the admin map](administration.md).

### Blips and prompts

```lua
Config.StoreBlip = {
    enabled = true,
    sprite = 73,
    color = 47,
    scale = 0.7,
    label = 'Clothing Store',
}

Config.ShopBlips = {
    clothing = { sprite = 73, color = 47, scale = 0.7, label = 'Clothing Store' },
    barber   = { sprite = 71, color = 0,  scale = 0.7, label = 'Barber' },
    tattoo   = { sprite = 75, color = 1,  scale = 0.7, label = 'Tattoo Parlor' },
    surgeon  = { sprite = 102, color = 4, scale = 0.7, label = 'Plastic Surgeon' },
}

Config.ShopInteractLabels = {
    clothing = '[E] Clothing Store',
    barber = '[E] Barber Shop',
    tattoo = '[E] Tattoo Shop',
    surgeon = '[E] Surgeon',
}
```

`ShopBlips` sets the default blip per shop type; `StoreBlip` is the legacy fallback and its `enabled` flag is the global on/off switch. Individual stores can override sprite, color, scale, and blip visibility from the admin map.

---

## Store Menus

`Config.StoreMenus` decides which customization sections each shop type offers. Entering a barber or tattoo shop — or choosing **Buy Clothes** at a clothing store — shows a vertical menu of these sections; picking one opens that editor, and its Back button returns to the menu.

```lua
Config.StoreMenus = {
    clothing = { 'clothes' },
    barber = {},
    tattoo = { 'tattoos' },
}
```

| Section id | Opens |
|---|---|
| `clothes` | All clothing and props on two scrolling rails |
| `appearance` | Hair, beard, eyebrows, skin, body |
| `makeup` | Makeup, blush, lipstick |
| `tattoos` | Body tattoos by zone |
| `faceFeatures` | Face-sculpt sliders |
| `ped` | Ped model |

Behavior:

| List | Result |
|---|---|
| Two or more entries | The section menu is shown, then the chosen editor opens |
| Exactly one entry | That editor opens directly, with no one-item menu |
| Empty (`{}`) | The shop's built-in editor opens — clothing the full editor, barber the combined appearance editor, surgeon the face-blend editor |
| Key removed entirely | The shipped default for that shop type is used |

An entry is either a section id or a table that overrides the label and icon:

```lua
clothing = {
    'clothes',
    { id = 'tattoos', label = 'Ink', icon = 'tattoo' },
},
```

Available icon names are `clothes`, `props`, `hat`, `bag`, `vest`, `top`, `pants`, `shoes`, `glasses`, `watch`, `chain`, `mask`, `hair`, `appearance`, `makeup`, `tattoo`, `face`, `ped`, and `inheritance`.

Unknown ids are dropped, so a typo cannot break the menu. `surgeon` is deliberately left out of the shipped table and opens the built-in face-blend editor.

---

## Costs

```lua
Config.ClothingCost = 0
Config.BarberCost = 0
Config.TattooCost = 0
Config.SurgeonCost = 0
Config.ChargePerTattoo = false
```

| Setting | Description |
|---|---|
| `ClothingCost` | Charged once per clothing-store visit. |
| `BarberCost` | Charged once per barber visit. |
| `TattooCost` | Charged per tattoo when `ChargePerTattoo` is on, otherwise once per tattoo-shop visit. |
| `SurgeonCost` | Charged once per surgeon visit. |
| `ChargePerTattoo` | Bill each tattoo individually instead of charging shop entry. |

Charges are server-authoritative — the cost is read from the config on the server, never from the client — and are taken from the cash account. A visit is charged once even when the player moves through several sections of the same shop menu, and the character creator never charges.

---

## Behavior

```lua
Config.TrustFrameworkNewCharacter = true
Config.AutomaticFade = true
Config.RCoreTattoosCompatibility = false
Config.PersistUniforms = false
Config.ReloadSkinCooldown = 5000
Config.OutfitCodeLength = 10
Config.EnablePedMenu = true
```

| Setting | Description |
|---|---|
| `TrustFrameworkNewCharacter` | When `true`, the framework's own first-character event opens the creator directly. When `false`, the database is checked first and the creator is skipped if anything is already saved. |
| `AutomaticFade` | Applies a matching hair-fade decoration when the hair style changes. Recommended. |
| `RCoreTattoosCompatibility` | Compatibility mode for rcore tattoo collections. |
| `PersistUniforms` | Keeps a player's job uniform across a disconnect. |
| `ReloadSkinCooldown` | Milliseconds between `/reloadskin` uses. |
| `OutfitCodeLength` | Length of a generated share code. The alphabet excludes the ambiguous `0`, `O`, `1`, and `I`. |
| `EnablePedMenu` | Allows changing the ped model from the editor. |

{% hint style="info" %}
Leave `TrustFrameworkNewCharacter` at `true`. Stock `qbx_core`, `qb-multicharacter`, and `esx_multicharacter` only raise their first-character event for a genuinely new character. Setting it to `false` is only for a multicharacter fork that raises that event on every login, and it costs a new character their creator if the lookup fails for any reason.
{% endhint %}

---

## Free-Open Commands

```lua
Config.AppearanceCommands = {}
Config.AppearanceKeybind = false
Config.AppearanceCommandAdminOnly = true
```

| Setting | Description |
|---|---|
| `AppearanceCommands` | Chat commands that open the editor anywhere. Empty by default. |
| `AppearanceKeybind` | Key bound to the **first** command in the list, through `RegisterKeyMapping` so players can rebind it. |
| `AppearanceCommandAdminOnly` | Restricts those commands to admins. Enforced on the server. |

```lua
Config.AppearanceCommands = { 'gsappearance', 'appearance' }
Config.AppearanceKeybind = 'F6'
```

Only the first command receives the keybind — mapping several commands to one key just overwrites itself.

{% hint style="warning" %}
Setting `AppearanceCommandAdminOnly = false` lets every player redress anywhere in the world, which removes the reason for clothing stores to exist. Leave it `true` unless that is what you want.
{% endhint %}
