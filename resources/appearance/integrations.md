# Integrations

GS Appearance is built to take over from an existing appearance system without touching the scripts that talk to it. This page covers what it replaces, how existing player data is migrated, and how third-party resources keep working.

---

## Resources It Replaces

`fxmanifest.lua` declares:

```lua
provides {
    'illenium-appearance',
    'qb-clothing',
    'skinchanger',
    'esx_skin',
    'fivem-appearance',
}
```

That satisfies other resources' dependency checks and makes `exports['illenium-appearance']` resolve to GS Appearance. It does **not** stop those resources from running if they are still ensured.

Stop and remove them. Two appearance resources fighting over the same ped is exactly what "the menu opens twice" and "my skin resets" are.

### Conflict detection

About three seconds after start, the server checks for `illenium-appearance`, `qb-clothing`, `qb-skinshop`, `esx_skin`, `skinchanger`, `fivem-appearance`, and `bl_appearance`, and prints a banner if any is genuinely running:

```text
[gs_appearance] ────────────────────────────────────────────────
[gs_appearance] CONFLICT: qb-clothing still running.
[gs_appearance] gs_appearance replaces these — stop/remove them in server.cfg.
[gs_appearance] Leaving them on causes double menus, resets and lost outfits.
[gs_appearance] ────────────────────────────────────────────────
```

The check resolves each name to a resource path and ignores anything that merely aliases to GS Appearance through `provides`, so a clean server never sees a false warning.

---

## Migrating From a Predecessor

`playerskins` and `users`.`skin` are shared with the resources GS Appearance replaces, but each predecessor stores a different shape. Conversion runs automatically, once per character, client-side.

| Source | Detected by | Read from |
|---|---|---|
| `qb-clothing` | `torso2` / `t-shirt` / `face` keys, face features on a −10…10 scale | `playerskins.skin` |
| `esx_skin`, `skinchanger` | Flat `torso_1` / `tshirt_1` / `pants_1` keys | `playerskins.skin`, then `users.skin` |

The conversion rescales face features from −10…10 to −1.0…1.0 and overlay opacity from 0…10 to 0…1, applies the result to the live ped, reads it back, and saves it in the current format.

{% hint style="info" %}
A row left by a predecessor counts as "this player has an appearance", so existing characters are never pushed back through the character creator on the first boot after the switch. This is what stops a whole server's population from being reset on install day.
{% endhint %}

Migration state resets on logout, so a relogin re-checks cleanly.

### Known limitation: QBCore character-select previews

`qb-multicharacter` reads `playerskins.model` with `tonumber()` and cannot resolve the model **name** stored in that column. Character-select previews on QBCore therefore show the right clothing on a random ped model. In game the player spawns correctly.

Storing a hash instead would break Qbox, which runs `joaat()` on the same column and works today. `illenium-appearance` carries the same trade-off.

---

## Third-Party Scripts

Every callback, net event, and export is registered under **both** the `gs_appearance` and `illenium-appearance` names, plus an export polyfill, so both of these resolve:

```lua
exports['gs_appearance']:getPedAppearance(ped)
exports['illenium-appearance']:getPedAppearance(ped)
```

The full surface is documented in [API & Exports](exports.md).

### ox_lib callbacks without ox_lib

Scripts written against `illenium-appearance` normally reach it through ox_lib:

```lua
local appearance = lib.callback.await('illenium-appearance:server:getAppearance', false)
```

GS Appearance reimplements ox_lib's callback wire protocol — the `__ox_cb_<name>` handler plus `exports.ox_lib:setValidCallback` — and re-claims it when `ox_lib` starts, so those calls work whether or not the server runs ox_lib.

It also exposes its own net-event request/response system as a fallback, with a 15-second timeout and pcall guards so a client can never hang waiting for a reply.

{% hint style="info" %}
`ox_lib` is not a dependency and does not need to be installed. When it is present it is used for nicer notifications, text UI, and menus. Notifications cascade framework-native first (Qbox, QBCore, ESX), then ox_lib, then the native GTA feed.
{% endhint %}

### `qb-clothing` shims

| Export | Behavior |
|---|---|
| `getPedAppearance(ped)` | Current appearance |
| `setPlayerAppearance(appearance)` | Apply an appearance |
| `reloadSkin()` | Reapply the saved appearance |
| `getOutfits()` | The caller's saved outfits |
| `IsCreatingCharacter()` | `true` while the creator is open |

Events `qb-clothing:client:openMenu`, `openOutfitMenu`, `loadOutfit`, `loadPlayerClothing`, `qb-clothes:loadSkin`, and `qb-clothes:client:CreateFirstCharacter` are all handled.

### `skinchanger` and `esx_skin` shims

| Export | Behavior |
|---|---|
| `skinchanger:GetSkin()` | Current skin in `skinchanger` key format |
| `skinchanger:LoadSkin(skin)` | Apply a `skinchanger`-shaped skin |
| `skinchanger:LoadClothes(_, clothes)` | Convert and apply clothing keys |
| `esx_skin:getPlayerSkin()` | Current skin in ESX key format |
| `esx_skin:openMenu(onSubmit, onCancel)` | Open the editor |
| `esx_skin:openSaveableMenu(onSubmit, onCancel)` | Open the editor and save on submit |

Events `skinchanger:loadSkin`, `loadSkin2`, `loadClothes`, `getSkin`, `esx_skin:save`, `esx_skin:getLastSkin`, `esx_skin:openMenu`, `openSaveableMenu`, `openRestrictedMenu`, `openSaveableRestrictedMenu`, `esx_skin:resetFirstSpawn`, and `esx_skin:playerRegistered` are all handled.

{% hint style="warning" %}
Every ESX spawn round-trip fires its callback unconditionally, even on failure. A spawn manager waiting on `skinchanger:loadSkin` or `esx_skin:getLastSkin` therefore cannot be left waiting forever on a permanent black screen.
{% endhint %}

---

## Multicharacter and Identity

| Resource | Behavior |
|---|---|
| `qb-multicharacter` | Its `closeNUIdefault` path raises the first-character event, which opens the creator |
| `qbx_core` | `spawnDefault` and `spawnNoApartments` raise the first-character event |
| `qbx_properties` | The apartment hand-off is picked up from `QBCore:Client:OnPlayerLoaded` |
| `esx_multicharacter` | Blocks on the creator's submit/cancel callback |
| `esx_identity` | Registration triggers the creator |
| None on ESX | `esx:playerLoaded` with `isNew` opens the creator, after waiting up to 15 seconds for a multichar or identity resource to claim it first |

On ESX all paths converge behind a single 60-second claim lock, so two surfaces never fight for NUI focus.

Identity resolution retries for up to 10 seconds, polling every 250 ms, because on every framework the player-loaded event can fire before the player object is queryable. Without that retry, a returning player is read as brand new and sent back through the creator.

---

## Framework Notes

| Framework | Notes |
|---|---|
| Qbox | Checked before QBCore. A Qbox server running `qb-core` as a legacy shim is still detected as Qbox, because only `qbx_core` owns the player |
| QBCore | Money is charged from cash; jobs and gangs both resolve |
| ESX Legacy | Cash account is `money`. The appearance is mirrored best-effort into `users`.`skin` for legacy scripts |
| ox | Player load is handled through `ox:playerLoaded` |
| Standalone | Appearances save against the license identifier and job features are disabled. A startup banner says so |

The framework's live job table is never mutated: jobs and gangs are normalized into a copy before use.

`users`.`skin` is the one column GS Appearance does not create — `es_extended`'s own player query selects it, so it exists on any working ESX server. If it is missing, the mirror is skipped with a notice rather than aborting the authoritative `playerskins` write.
