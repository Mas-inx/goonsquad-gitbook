# GS Appearance

GS Appearance is a clothing, barber, tattoo and character-creation system for FiveM. It replaces `illenium-appearance`, `qb-clothing`, `skinchanger`, `esx_skin`, and `fivem-appearance` with a cinematic NUI layered over the live ped, and keeps every one of their exports, events, and callbacks working so the scripts already written against them keep running unchanged.

These pages document version **2.0.0**.

**Price:** $9.99 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7652989)

{% embed url="https://www.youtube.com/watch?v=ODhNO2fJ5H0" %}

{% hint style="info" %}
The release is protected by FiveM Asset Escrow. `shared/config.lua`, `server/credentials.lua`, the tattoo catalogue, the framework and compat bridge files, the SQL schema, the seed and preview data, and the NUI bundle all remain open and editable. See [Configuration](configuration.md#file-layout).
{% endhint %}

---

## Highlights

### Drop-in installation

- No config editing and no SQL import: the framework is detected at runtime and every table is created and repaired on start
- Default shop zones are seeded on first run — 15 clothing stores, 7 barbers, 6 tattoo parlors
- `provides` the five resource names it replaces, so other scripts' dependency checks are satisfied
- Existing `playerskins` and `users`.`skin` rows written by a predecessor are detected and converted per character, so nobody is pushed back through the character creator
- A startup banner names the detected framework and warns loudly if a competing appearance resource is still running

### The editor

- Cinematic dark HUD over a transparent NUI, with the ped kept clear in the center — not a flat dashboard
- Arc-positioned category rails, a curved item carousel with real preview renders, a drag dial for mixes and sliders, and a 64-swatch hair palette
- Scripted camera with rotate, zoom, pan and tilt, plus one-tap focus presets that frame the face, shirt, pants, or shoes
- Full freemode pipeline: ped model, components, props, head blend, 20 face-feature sliders, 12 head overlays, hair with tint, eye color, makeup, and tattoos by body zone
- Live preview thumbnails for clothing, props, hair, faces, overlays, and tattoos
- Server-wide accent color changed live from the admin panel, and per-icon overrides from config with no rebuild

### Shops and stores

- Four illenium-compatible shop types — **clothing**, **barber**, **tattoo**, **surgeon** — each opening its own editor
- Store zones live in the database and are created, moved, and deleted from an in-game map, never a config file
- Per-type blips and interaction prompts, with per-store overrides and a global toggle
- Job and gang locks per store, with a minimum grade
- Configurable per-shop costs, charged server-side
- `Config.StoreMenus` decides which sections each shop type offers, so a barber can also sell clothing or a clothing store can offer tattoos

### Outfits

- Unlimited named outfits, saved with **O** from the clothing editor and browsed in a vertical rail
- Shareable outfit codes: the owner mints a code, anyone can import it as a copy of that outfit
- Job and gang uniforms from `management_outfits`, filtered by job, grade, and gender, and merged into the player's outfit list
- Saved appearances load automatically on player load across QBCore, Qbox, ESX, and ox

### Administration

- `/gsadmin` opens an interactive map dashboard: create, move, edit, toggle, and delete every store
- Item rules restrict individual clothing items three ways — blacklist, job lock, or player whitelist — enforced on the server and filtered out of the UI
- Admin access from an ACE, a framework permission, an ESX group, or an identifier list
- `/gsdiag` answers "why is my appearance not saving?" in one command, with a real database write-and-delete round trip

---

## Compatibility

### Frameworks

| Framework | Support |
|---|---|
| Qbox (`qbx_core`) | Supported |
| QBCore (`qb-core`) | Supported |
| ESX Legacy (`es_extended`) | Supported, including `esx_identity` and `esx_multicharacter` |
| ox (`ox_core`) | Supported |
| Standalone | Supported (license identifier fallback, job features disabled) |

Detection order is `qbx_core` → `qb-core` → `es_extended` → `ox_core`. Qbox is checked before QBCore deliberately: many Qbox servers still run `qb-core` as a shim for legacy resources, but only `qbx_core` owns the player there. No framework file needs to be edited.

### Resources it replaces

| Resource | Behavior |
|---|---|
| `illenium-appearance` | Full export, event, and callback surface reimplemented |
| `qb-clothing` | Export shims and events (`qb-clothing:client:*`, `qb-clothes:*`) |
| `skinchanger` | `GetSkin`, `LoadSkin`, `LoadClothes` plus its events |
| `esx_skin` | `getPlayerSkin`, `openMenu`, `openSaveableMenu` plus its events |
| `fivem-appearance` | Satisfied through `provides` |

Stop and remove the resource you are replacing. See [Integrations](integrations.md).

### Optional integrations

| Resource | Use |
|---|---|
| `ox_lib` | Nicer notifications, text UI, and menus when present. Never required — the bridge reimplements ox_lib's callback protocol so third-party scripts keep working without it |
| `qb-multicharacter`, `qbx_properties` | Character creation hand-off on first spawn |
| `esx_multicharacter`, `esx_identity` | Character creation hand-off on first spawn |
| Fivemanage | Optional CDN hosting for the ~7,900 preview thumbnails |

---

## Requirements

- A modern FiveM server artifact
- `oxmysql` — the only hard dependency
- Qbox, QBCore, ESX, ox, or standalone
- No SQL import and no config edit for a default install

{% hint style="warning" %}
Only one appearance resource may own the ped. Leaving `illenium-appearance`, `qb-clothing`, `esx_skin`, `skinchanger`, or `bl_appearance` running alongside GS Appearance causes double menus, resets, and lost outfits. The server console prints a conflict banner about three seconds after start when it finds one.
{% endhint %}

---

## Next Steps

1. Follow [Installation](installation.md).
2. Set your admins and shop costs in [Configuration](configuration.md).
3. Place your stores and item rules with [Administration](administration.md).
4. Read [Usage](usage.md) for the player and editor flows.
5. If you are migrating, read [Integrations](integrations.md).
6. Connect custom resources through [API & Exports](exports.md).
