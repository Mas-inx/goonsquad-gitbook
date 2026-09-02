# API & Exports

Every export, callback, and net event listed here is registered under **both** the `gs_appearance` and `illenium-appearance` names. Use whichever your script already targets:

```lua
exports['gs_appearance']:getPedAppearance(ped)
exports['illenium-appearance']:getPedAppearance(ped)
```

The `qb-clothing`, `skinchanger`, and `esx_skin` shims are listed in [Integrations](integrations.md#third-party-scripts).

---

## The Appearance Table

Most of the API passes this shape around:

```lua
{
    model = 'mp_m_freemode_01',
    headBlend = { shapeFirst = 0, shapeSecond = 0, shapeMix = 0.5, skinFirst = 0, skinSecond = 0, skinMix = 0.5, ... },
    faceFeatures = { ... },   -- 20 values, -1.0 to 1.0
    headOverlays = { ... },   -- 12 overlays: style, opacity, color
    components = { ... },     -- { component_id, drawable, texture }
    props = { ... },          -- { prop_id, drawable, texture }
    hair = { style = 0, color = 0, highlight = 0 },
    tattoos = { ... },        -- by zone
    eyeColor = 0,             -- 0-31
}
```

---

## Client Exports

### Getters

| Export | Returns |
|---|---|
| `getPedModel(ped)` | Model name |
| `getPedComponents(ped)` | Clothing components |
| `getPedProps(ped)` | Props |
| `getPedHeadBlend(ped)` | Head blend |
| `getPedFaceFeatures(ped)` | Face feature values |
| `getPedHeadOverlays(ped)` | Head overlays |
| `getPedHair(ped)` | Hair style, color, and highlight |
| `getPedAppearance(ped)` | The complete appearance table |

```lua
local appearance = exports['gs_appearance']:getPedAppearance(PlayerPedId())
```

### Setters

| Export | Applies |
|---|---|
| `setPlayerModel(model)` | Ped model, by name or hash |
| `setPedComponent(ped, component)` | One clothing component |
| `setPedComponents(ped, components)` | All clothing components |
| `setPedProp(ped, prop)` | One prop |
| `setPedProps(ped, props)` | All props |
| `setPedHeadBlend(ped, headBlend)` | Head blend |
| `setPedFaceFeatures(ped, faceFeatures)` | Face features |
| `setPedHeadOverlays(ped, headOverlays)` | Head overlays |
| `setPedHair(ped, hair, tattoos)` | Hair, keeping its tint |
| `setPedEyeColor(ped, eyeColor)` | Eye color |
| `setPedAppearance(ped, appearance)` | A whole appearance onto a ped |
| `setPlayerAppearance(appearance)` | Model **and** appearance onto the local player |

```lua
exports['illenium-appearance']:setPedAppearance(PlayerPedId(), appearance)
```

{% hint style="info" %}
On a freemode ped, components `0` and `2` are protected from `setPedComponent`. Hair is always routed through `setPedHair` so it keeps its tint — this is what prevents the classic green-hair bug.
{% endhint %}

### Tattoos

| Export | Purpose |
|---|---|
| `setPedTattoos(ped, tattoos)` | Apply a full tattoo set |
| `addPedTattoo(tattoo)` | Add one tattoo |
| `removePedTattoo(tattoo)` | Remove one tattoo |
| `setPreviewTattoo(tattoo)` | Show a tattoo without committing it |

### Editor and session

#### `startPlayerCustomization(cb, config, cancelCb)`

Opens the editor and routes to the right surface based on what the config asks for.

```lua
exports['gs_appearance']:startPlayerCustomization(function(appearance)
    if appearance then
        print('saved')
    end
end, {
    ped = true,
    headBlend = true,
    faceFeatures = true,
    headOverlays = true,
    components = true,
    props = true,
    tattoos = true,
})
```

| Config asks for | Opens |
|---|---|
| `ped = true`, or head editing **and** wardrobe | The full character creator |
| `tattoos` only | The tattoo shop |
| `headOverlays` only | The barber |
| `headBlend` or `faceFeatures` without components | The surgeon |
| Anything else | The clothing editor |

`cb` receives the saved appearance; `cancelCb` fires when the player backs out. Both are cleared before either is invoked, so a blocking caller such as `esx_multicharacter` is never resumed twice.

| Export | Purpose |
|---|---|
| `LoadSavedAppearance()` | Reapply the appearance stored for this character. Returns `true` when one was applied |
| `ApplyAppearance(appearance)` | Apply an appearance table without saving it |
| `OpenOutfitMenu()` | Open the outfit browser. Also available as `openOutfitMenu()` |
| `IsCreatingCharacter()` | `true` while the character creator is open |
| `ForceRelease()` | Emergency release — NUI focus, ped control, camera, and screen fade. Same as `/gsrelease` |

### Context

| Export | Returns |
|---|---|
| `IsAppearanceAdmin()` | Whether the local player is an appearance admin |
| `GetLocalJob()` | The local player's job table |
| `GetFrameworkName()` | `qbox`, `qb`, `esx`, `ox`, or `standalone` |
| `GetAppearanceStores()` | Every store zone the client knows about |
| `GetAppearanceItemRules()` | The item rules the client received |
| `CanUseDrawableLocally(categoryId, drawable)` | Client-side allow check |
| `IsDrawableRestrictedLocally(categoryId, drawable)` | Whether any rule targets that item |

```lua
if exports['gs_appearance']:GetFrameworkName() == 'standalone' then
    -- job features are unavailable
end
```

{% hint style="warning" %}
`CanUseDrawableLocally` and `IsDrawableRestrictedLocally` are for filtering UI only. The server re-checks every item with `CanPlayerUseItem`, so a client that lies about a restricted item is rejected anyway.
{% endhint %}

---

## Server Exports

### `getAppearance(source, model)`

Returns the stored appearance for a player.

```lua
local appearance = exports['gs_appearance']:getAppearance(source)
```

### `saveAppearance(source, appearance)`

Stores an appearance for a player.

```lua
exports['gs_appearance']:saveAppearance(source, appearance)
```

### `GetPlayerID(source)`

Returns the identity GS Appearance resolves for a player — the framework's citizen id, or the license identifier on standalone. This is the key every table is written against.

```lua
local citizenId = exports['gs_appearance']:GetPlayerID(source)
```

### `CanPlayerUseItem(source, categoryId, drawable)`

The authoritative item-rule check. Returns `allowed, reason`.

```lua
local allowed, reason = exports['gs_appearance']:CanPlayerUseItem(source, 'tops', 42)
-- reason is nil, 'blacklist', 'job_lock', or 'invalid_item'
```

Precedence is player whitelist, then job locks, then blacklist. See [Administration](administration.md#item-rules).

### `GetItemRulesForNet()`

Returns every item rule in the shape sent to clients. Useful for an external admin panel.

---

## Server Callbacks

Callable through ox_lib when it is installed, and through the built-in bridge when it is not. Every name exists under both prefixes.

| Callback | Arguments | Returns |
|---|---|---|
| `getAppearance` | `model` | The stored appearance, or `nil` |
| `appearanceStatus` | — | Whether the identity resolved and whether the player has an appearance |
| `legacySkin` | — | The raw predecessor row, or `nil` once a real appearance exists |
| `getOutfits` | — | The caller's saved outfits |
| `getManagementOutfits` | `type`, `gender` | Job or gang uniforms the caller qualifies for |
| `getUniform` | — | The caller's cached uniform |
| `generateOutfitCode` | `outfitId` | A share code, owner only |
| `importOutfitCode` | `outfitName`, `code` | `true` when the outfit was cloned |
| `hasMoney` | `shopType` | `hasEnough, cost` |
| `payForTattoo` | `tattoo` | `true` when the charge succeeded |

```lua
local appearance = lib.callback.await('illenium-appearance:server:getAppearance', false)
```

`appearanceStatus` is the callback to use when deciding whether a character is new. It separates "this player has nothing saved" from "the server could not resolve who they are yet", which a plain `getAppearance` returning `nil` cannot:

```lua
{
    resolved = true,
    hasAppearance = true,
    legacy = false,
    skinRows = 1,
    activeSkinRows = 1,
    citizenid = 'ABC12345',
}
```

{% hint style="danger" %}
Tattoo and shop costs are read from the server config, never from the client. `payForTattoo` ignores any cost sent with the tattoo.
{% endhint %}

---

## Client Events

Trigger these to open a surface:

| Event | Opens |
|---|---|
| `illenium-appearance:client:openClothingShop` | The clothing editor |
| `illenium-appearance:client:OpenBarberShop` | The barber |
| `illenium-appearance:client:OpenTattooShop` | The tattoo shop |
| `illenium-appearance:client:OpenSurgeonShop` | The surgeon |
| `illenium-appearance:client:openOutfitMenu` | The outfit browser |
| `illenium-appearance:client:openJobOutfitsMenu` | The job uniform menu |
| `illenium-appearance:client:reloadSkin` | Reapply the saved appearance |
| `illenium-appearance:client:ClearStuckProps` | Clear every prop from the ped |
| `gs_appearance:client:openShop` | A shop, by `{ shopType = 'barber' }` |
| `gs_appearance:client:openCharacterCreator` | The character creator |

```lua
TriggerEvent('gs_appearance:client:openShop', { shopType = 'tattoo' })
```

Server-side, push a surface to one player:

```lua
TriggerClientEvent('illenium-appearance:client:OpenBarberShop', source)
```

---

## Outfit and Uniform Events

| Event | Side | Purpose |
|---|---|---|
| `illenium-appearance:server:saveAppearance` | Server | Save an appearance for the caller |
| `illenium-appearance:server:saveOutfit` | Server | Save a named outfit |
| `illenium-appearance:server:updateOutfit` | Server | Update an existing outfit |
| `illenium-appearance:server:deleteOutfit` | Server | Delete one of the caller's outfits |
| `illenium-appearance:server:saveManagementOutfit` | Server | Create or update a job/gang uniform |
| `illenium-appearance:server:deleteManagementOutfit` | Server | Delete a job/gang uniform |
| `illenium-appearance:server:syncUniform` | Server | Record the uniform a player is wearing |
| `illenium-appearance:server:chargeCustomer` | Server | Charge the configured cost for a shop type |
| `illenium-appearance:client:changeOutfit` | Client | Wear an outfit |
| `illenium-appearance:client:loadJobOutfit` | Client | Wear a job uniform |
| `illenium-appearance:client:saveOutfit` | Client | Open the save-outfit prompt |
| `illenium-appearance:client:deleteOutfit` | Client | Delete an outfit |

Ownership is checked on the server for every outfit action: an outfit id supplied by a client is resolved and confirmed to belong to the caller before anything is deleted or shared. Uniform management is checked against the job the outfit belongs to.

### Routing buckets

```lua
TriggerServerEvent('illenium-appearance:server:ChangeRoutingBucket')
TriggerServerEvent('illenium-appearance:server:ResetRoutingBucket')
```

Moves the player into a private bucket for a customization session and back to bucket `0` afterwards.

---

## Database

External panels should treat these tables as resource-owned and read-only. Use the exports and events above for writes so identity resolution, ownership checks, and the ESX mirror stay consistent.

| Table | Holds |
|---|---|
| `playerskins` | `citizenid`, `model`, `skin` (LONGTEXT), `active` |
| `player_outfits` | Named outfits, unique on `(citizenid, outfitname, model)` |
| `player_outfit_codes` | `outfitid` → `code` |
| `management_outfits` | Job and gang uniforms with `job_name`, `type`, `minrank`, `gender` |
| `gs_appearance_stores` | Store zones with `store_type`, `jobs`, and blip settings |
| `gs_appearance_item_rules` | `rule_type`, `category_id`, `drawable`, `jobs`, `identifiers`, `note` |

The schema is created and healed on start: missing columns are added, undersized columns widened, and the `player_outfits` unique key added only when no duplicate groups exist, so no outfit is ever deleted to satisfy it. `sql/` holds the same statements for a manual import.

{% hint style="info" %}
`player_outfits.citizenid` is `VARCHAR(64)`, not 50 and not 255. An ESX Legacy identifier (`char1:license:<40 hex>`) is 54 characters and overflowed 50; 64 keeps the three-column unique key under InnoDB's 767-byte index limit.
{% endhint %}

`users`.`skin` on ESX is written best-effort as a mirror. It is not created by this resource — `es_extended`'s own player query selects it, so it exists on any working ESX server. If it is absent the mirror is skipped with a notice and the authoritative `playerskins` write still succeeds.
